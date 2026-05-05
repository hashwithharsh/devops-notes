# S3 Practical Notes

S3 is object storage.

## Main concepts

```text
Bucket
 ↓
Object
 ↓
Key
```

An object consists of data plus metadata and is identified by its key within a bucket.

## Common DevOps uses

- build artifacts
- backups
- logs
- static assets
- Terraform state backends

## Useful CLI

```bash
aws s3 ls
aws s3 ls s3://my-bucket
aws s3 cp file.txt s3://my-bucket/
aws s3 cp s3://my-bucket/file.txt .
aws s3 sync ./build s3://my-bucket/build/
```

## Security

Do not make a bucket public just because an application needs access.

Prefer:

- IAM roles
- bucket policies
- least privilege
- encryption
- logging/auditing

## Versioning

Versioning can help recover from accidental overwrites/deletions and provides multiple object versions.

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

### Practical lab: work with an S3 bucket safely

List buckets:

```bash
aws s3 ls
```

Create a uniquely named test bucket in the intended Region, then upload a harmless test file:

```bash
echo hello > test.txt
aws s3 cp test.txt s3://<bucket>/test.txt
```

Verify:

```bash
aws s3 ls s3://<bucket>/
aws s3 cp s3://<bucket>/test.txt ./downloaded.txt
```

When finished, delete the test object and bucket if it is no longer needed.

I pay attention to bucket policy, Block Public Access, encryption and IAM permissions before making anything public.

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

For **S3 Practical**, the workflow I want to remember is:

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
bucket, object, test, verify, bash, before, build, change, does, my-bucket, only, result
```

I am keeping these here because when I search the repository later, these are the words I am likely to remember.

### If I had to explain this to a teammate

I would start with the problem, not the tool:

> "The reason we use S3 Practical here is because it gives us a repeatable way to handle s3 practical. The practical part is that I can inspect the current state, make the intended change, verify it, and troubleshoot it without relying on manual guesswork."

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
