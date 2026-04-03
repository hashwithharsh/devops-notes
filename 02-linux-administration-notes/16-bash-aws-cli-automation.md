# Bash + AWS CLI Automation

I like this combination for small operational tasks, but I need to be careful because a bad loop around a destructive AWS command can affect a lot of resources.

## Verify identity first

```bash
aws sts get-caller-identity
aws configure get region
```

If a script can run under different profiles, I make the profile explicit rather than assuming whatever happens to be configured in my terminal.

## Prefer structured output

Example:

```bash
aws ec2 describe-instances   --query 'Reservations[].Instances[].InstanceId'   --output text
```

For more complicated data I prefer JSON and a JSON-aware parser such as `jq` instead of scraping a formatted table.

## Practical: inspect stopped instances

```bash
aws ec2 describe-instances   --filters Name=instance-state-name,Values=stopped   --query 'Reservations[].Instances[].{Id:InstanceId,Name:Tags[?Key==`Name`]|[0].Value}'   --output table
```

First I list them. I don't immediately terminate anything.

## Safe automation pattern

```bash
#!/usr/bin/env bash
set -euo pipefail

PROFILE="dev"
REGION="ap-south-1"

aws sts get-caller-identity   --profile "$PROFILE"

aws ec2 describe-instances   --profile "$PROFILE"   --region "$REGION"   --output table
```

Only after verifying account, Region and target resources would I add a modifying command.

## When Bash is no longer the right tool

If I need complex dependency handling, long-lived infrastructure, reusable infrastructure definitions or sophisticated error recovery, I would rather use Terraform, Ansible, Python or a proper CI/CD workflow.

Bash is good glue. It is not automatically the best orchestration language.

## Security

I never put permanent AWS access keys in a Git repository. For automation I prefer the credential mechanism appropriate to the environment, ideally short-lived/workload identity where supported.
