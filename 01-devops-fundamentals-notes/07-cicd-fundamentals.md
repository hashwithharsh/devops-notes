# CI/CD Fundamentals

## Continuous Integration

Developers integrate changes frequently.

Automation then:

```text
Checkout
 ↓
Build
 ↓
Unit Test
 ↓
Static Analysis
 ↓
Security Checks
```

The goal is to discover integration problems early.

## Continuous Delivery

The software is kept in a deployable state.

A release may still require an approval or manual trigger.

## Continuous Deployment

A passing change is automatically deployed.

## Example pipeline

```text
Git Push
   ↓
CI Trigger
   ↓
Checkout
   ↓
Build
   ↓
Test
   ↓
Scan
   ↓
Package
   ↓
Publish
   ↓
Deploy
   ↓
Health Check
```

## Artifacts

An artifact is a versioned output of a build.

Examples:

- JAR
- Python package
- Docker image
- binary
- static website bundle

## Pipeline principle

Build once, promote the same artifact.

Do not rebuild different binaries for staging and production if the goal is to deploy exactly what was tested.

## Common tools

Jenkins and GitHub Actions are commonly used for CI/CD.

Argo CD is commonly used for Kubernetes GitOps-based delivery.

## Why this topic matters in the larger system

The purpose of this topic is to understand the engineering problem before memorizing a tool or command. A junior DevOps engineer should be able to explain the idea in plain language, connect it to the software lifecycle, and give a practical example.


## Practical exercise

Create a small Git repository and document one complete software-delivery flow. Start with a simple application, describe its development workflow, add a test step, package it, and write down how you would deploy, monitor and roll back the application. The objective is to connect the concepts rather than to build a large system.

## Deep explanation

A pipeline should represent a trustworthy promotion path, not a random collection of shell commands.

A useful model is:

```text
Source
 ↓
Build
 ↓
Verify
 ↓
Package
 ↓
Publish
 ↓
Deploy
 ↓
Verify production
```

### Build once

Suppose commit `abc123` produces:

```text
myapp:abc123
```

The same image should be promoted through environments.

Do not rebuild:

```text
staging image
production image
```

from potentially different dependency state and then assume they are identical.

### Failure handling

Every stage should answer:

- What does success mean?
- What evidence do we record?
- What happens on failure?
- Can the operation be retried safely?
- Can the deployment be rolled back?

This is where CI/CD becomes engineering rather than automation for automation's sake.

## My Practical Reference

I want this note to be useful when I am actually working, not only when I am studying. I therefore keep a practical procedure here that I can return to later.

### What I do before changing anything

1. Identify the environment and target.
2. Check the current state.
3. Decide what result I expect.
4. Make the smallest controlled change.
5. Verify the result independently.

### Practical application

### Practical: build a minimal CI pipeline

For a small application:

```text
git push
 ↓
checkout
 ↓
install dependencies
 ↓
test
 ↓
build/package
```

Once that is stable I add:

```text
static analysis
security scanning
image build
registry push
deployment
smoke test
```

I add stages gradually because a pipeline that fails in five places at once is difficult to learn from.

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

For **Cicd Fundamentals**, the workflow I want to remember is:

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

## What I want to remember

### Why I am keeping this note

I am using this repository to connect the individual tools to the software delivery problem. I do not want to memorize DevOps buzzwords without being able to explain what changes in a real team.

### Where I would use it

I reproduce the stage in a small pipeline before adding more automation.

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
git status
docker build
terraform plan
ansible-playbook --check
kubectl get pods
systemctl status <service>

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

I try not to call a tool the solution just because it is popular. The first question should be what problem I am solving and what evidence tells me the solution is working.

Another thing I want to avoid is copying a command from a random blog without understanding what resource it is changing. A command can be syntactically correct and still be completely wrong for the environment.

### Keywords from this note

These are the terms I should recognize when I come back to the note:

```text
build, text, test, change, image, once, package, pipeline, result, deploy, verify, application
```

I am keeping these here because when I search the repository later, these are the words I am likely to remember.

### If I had to explain this to a teammate

I would start with the problem, not the tool:

> "The reason we use Cicd Fundamentals here is because it gives us a repeatable way to handle cicd fundamentals. The practical part is that I can inspect the current state, make the intended change, verify it, and troubleshoot it without relying on manual guesswork."

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
