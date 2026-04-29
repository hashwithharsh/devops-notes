# IAM Policies and Least Privilege

A policy defines permissions.

Conceptually:

```text
Effect
Action
Resource
Condition
```

Example:

```json
{
  "Effect": "Allow",
  "Action": [
    "s3:GetObject"
  ],
  "Resource": "arn:aws:s3:::my-bucket/*"
}
```

## Least privilege

Give only the permissions required for the task.

Bad:

```text
AdministratorAccess
```

when the application only needs:

```text
s3:GetObject
```

## Troubleshooting AccessDenied

Ask:

1. Which identity is making the request?
2. What role/user is being used?
3. What policies apply?
4. Is there an explicit deny?
5. Is the resource policy involved?
6. Are there organization/service-control restrictions?
7. Is the resource ARN correct?
8. Is the Region/account correct?

## CLI identity check

```bash
aws sts get-caller-identity
```

## Why this topic matters in the larger system

AWS knowledge is useful for DevOps only when services are understood as parts of an architecture. Learn the responsibility of each service, how services interact, what the security boundary is, and how to troubleshoot the path between them.


## Practical exercise

Build a small AWS environment only when you understand the cost and cleanup implications. Document the architecture, IAM permissions, network path, monitoring, and teardown procedure. Use Terraform for repeatability and AWS CLI commands for inspection.


## Troubleshooting habit

When something fails, do not immediately change several things. Record the symptom, collect evidence, form a hypothesis, test one hypothesis at a time, and verify the result. This habit is transferable across Linux, AWS, Git, Ansible, Terraform, Docker, Kubernetes and CI/CD.

## My Practical Reference

I want this note to be useful when I am actually working, not only when I am studying. I therefore keep a practical procedure here that I can return to later.

### What I do before changing anything

1. Identify the environment and target.
2. Check the current state.
3. Decide what result I expect.
4. Make the smallest controlled change.
5. Verify the result independently.

### Practical application

### Practical lab: design one narrow policy

Suppose a CI job only needs to read an S3 bucket.

I start with the exact actions and resource rather than broad permissions:

```text
s3:GetObject
s3:ListBucket
```

Then I identify the bucket/object ARNs.

After applying the policy, I test the real operation.

If something fails, I ask:

```text
Which API call failed?
Which identity made it?
Which resource was targeted?
Is there an explicit deny?
```

Least privilege is an iterative process: start narrow, observe required permissions, and refine.

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

For **Iam Policies Least Privilege**, the workflow I want to remember is:

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
only, text, resource, permissions, change, does, least, policy, privilege, result, verify, before
```

I am keeping these here because when I search the repository later, these are the words I am likely to remember.

### If I had to explain this to a teammate

I would start with the problem, not the tool:

> "The reason we use Iam Policies Least Privilege here is because it gives us a repeatable way to handle iam policies least privilege. The practical part is that I can inspect the current state, make the intended change, verify it, and troubleshoot it without relying on manual guesswork."

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
