# AWS CLI Deep Dive

## Configure

```bash
aws configure
```

But understand what credentials are being configured and where they come from.

## Confirm identity

```bash
aws sts get-caller-identity
```

Always do this when you are unsure which account/profile is active.

## EC2

```bash
aws ec2 describe-instances
aws ec2 describe-vpcs
aws ec2 describe-subnets
aws ec2 describe-security-groups
```

## S3

```bash
aws s3 ls
aws s3 cp file.txt s3://bucket/
aws s3 sync ./build s3://bucket/build/
```

## IAM

```bash
aws iam list-users
aws iam list-roles
```

## DevOps scripting

AWS CLI can be combined with Bash:

```bash
INSTANCE_ID=$(aws ec2 describe-instances   --query 'Reservations[0].Instances[0].InstanceId'   --output text)

echo "$INSTANCE_ID"
```

Do not blindly copy CLI snippets into production scripts.

Validate account, region, output and failure behavior.

## Why this topic matters in the larger system

AWS knowledge is useful for DevOps only when services are understood as parts of an architecture. Learn the responsibility of each service, how services interact, what the security boundary is, and how to troubleshoot the path between them.


## Practical exercise

Build a small AWS environment only when you understand the cost and cleanup implications. Document the architecture, IAM permissions, network path, monitoring, and teardown procedure. Use Terraform for repeatability and AWS CLI commands for inspection.

## Deep explanation

The AWS CLI is more than a convenience for avoiding the console. It gives DevOps engineers a scriptable interface to AWS APIs.

### Safe CLI workflow

Before a destructive operation:

```text
Identity
 ↓
Account
 ↓
Region
 ↓
Resource
 ↓
Action
 ↓
Expected result
```

Verify identity:

```bash
aws sts get-caller-identity
```

Use explicit profiles when working across accounts:

```bash
aws sts get-caller-identity --profile dev
aws sts get-caller-identity --profile prod
```

### Querying

AWS CLI can filter JSON output using `--query` and choose formats using `--output`.

Example:

```bash
aws ec2 describe-instances   --query 'Reservations[].Instances[].InstanceId'   --output text
```

This is useful in scripts because it avoids parsing large human-oriented output.

### Scripting caution

A script should check failure conditions.

For example, do not assume:

```bash
INSTANCE_ID=$(aws ec2 ... --output text)
```

always returns a valid instance ID.

A production script should validate the result before performing the next operation.

### Why CLI knowledge matters

Terraform describes infrastructure declaratively, while CLI commands are extremely useful for inspection, troubleshooting and targeted operational tasks.

## My Practical Reference

I want this note to be useful when I am actually working, not only when I am studying. I therefore keep a practical procedure here that I can return to later.

### What I do before changing anything

1. Identify the environment and target.
2. Check the current state.
3. Decide what result I expect.
4. Make the smallest controlled change.
5. Verify the result independently.

### Practical application

### Practical: use CLI as my AWS troubleshooting tool

I begin every session with:

```bash
aws sts get-caller-identity
```

Then I use focused queries:

```bash
aws ec2 describe-instances   --query 'Reservations[].Instances[].{Id:InstanceId,State:State.Name}'   --output table
```

For scripting, I prefer machine-readable output such as JSON when another command needs to parse it.

I keep destructive commands separate from discovery commands and verify the target resource before modifying it.

### Verification checklist

After the practical work, I check:

- Did the intended state actually change?
- Can I prove the result with a command, log, metric, or object status?
- Did I accidentally change anything else?
- Can I repeat the procedure?
- Can I clean up the lab safely?
- If it failed, can I explain the first useful error?

### Troubleshooting notes

If the expected result does not appear, I go one layer at a time:

```text
Current state
    ↓
Configuration
    ↓
Dependency
    ↓
Runtime/process
    ↓
Network/storage/permissions
    ↓
Logs and events
```

I avoid random fixes. I record the symptom, evidence, hypothesis, change and verification.

### Quick recall

For **Aws Cli Deep Dive**, the workflow I want to remember is:

```text
Understand
   ↓
Inspect
   ↓
Apply
   ↓
Verify
   ↓
Break
   ↓
Troubleshoot
   ↓
Document
```

> **Lab safety:** practice destructive operations such as disk changes, firewall changes, `git reset --hard`, `terraform destroy`, cloud deletion and cluster deletion only in disposable/test environments unless I fully understand the impact.

## Practical notes from studying this

### Why I am keeping this note

I am trying to learn AWS as an infrastructure system rather than as a list of service definitions. Whenever I read a service, I ask where it sits in the request path and what IAM/network boundary controls it.

### Where I would use it

I use a test AWS resource and confirm account/Region before changing anything.

### A way I would approach a real task

I would not start by changing configuration immediately. First I would collect enough information to decide which layer is actually involved.

```text
1. What exactly is failing?
2. What changed recently?
3. What is the expected state?
4. What is the current state?
5. Which dependency could explain the difference?
6. What is the smallest safe test?
7. How will I know the fix worked?
```

That sequence sounds simple, but it prevents a lot of random troubleshooting.

### Commands I want at my fingertips

```bash
aws sts get-caller-identity
aws configure list
aws ec2 describe-instances
aws ec2 describe-security-groups
aws ec2 describe-route-tables
aws logs describe-log-groups

```

I would not run all of these blindly. The useful command depends on the symptom. The point is knowing what question each command answers.

### What I should see

For this topic, a successful practical run should leave me with observable evidence rather than just a command that returned without an error.

I want to be able to say something concrete such as:

- the service is active
- the filesystem is mounted
- the route exists
- the API request is authorized
- the image was built with the expected tag
- the Terraform plan contains the expected change
- the Pod is Ready
- the pipeline produced the expected artifact
- the Git history contains the expected commit

### Common beginner mistake

One mistake I want to avoid is solving every problem by giving more permissions or making a resource public. I should first identify the exact action, identity, resource and network path.

Another thing I want to avoid is copying a command from a random blog without understanding what resource it is changing. A command can be syntactically correct and still be completely wrong for the environment.

### Keywords from this note

These are the terms I should recognize when I come back to the note:

```text
bash, output, result, text, before, get-caller-identity, useful, describe-instances, only, query, understand, verify
```

I am keeping these here because when I search the repository later, these are the words I am likely to remember.

### If I had to explain this to a teammate

I would start with the problem, not the tool:

> "The reason we use Aws Cli Deep Dive here is because it gives us a repeatable way to handle aws cli deep dive. The practical part is that I can inspect the current state, make the intended change, verify it, and troubleshoot it without relying on manual guesswork."

Then I would show the smallest working example and explain what each important line is doing.

### Personal revision checklist

Before I consider this topic understood, I should be able to:

- explain the concept without reading the definition
- run the basic practical example
- identify the important commands/configuration
- recognize a normal successful state
- intentionally create one common failure
- find the useful evidence
- explain the fix
- clean up the lab
- answer a basic interview question about it

> **Note to myself:** understanding is not the same as remembering syntax. If I cannot reproduce the task or troubleshoot a small failure, I should practice it again.
