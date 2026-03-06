# Environments and Release Management

## Why multiple environments?

A common progression is:

```text
Development
    ↓
Testing
    ↓
Staging
    ↓
Production
```

Each environment serves a different purpose.

## Development

Developers make frequent changes.

## Testing

Automated and manual tests validate behavior.

## Staging

Attempts to resemble production closely enough to validate the release.

## Production

Real users and real business traffic.

## Release

A release is a controlled version of software made available to an environment.

Important concepts:

- version
- artifact
- change record
- deployment
- approval
- rollback

## Good release practice

Before production:

```text
Build
 ↓
Test
 ↓
Security checks
 ↓
Deploy staging
 ↓
Verify
 ↓
Approval if required
 ↓
Production
 ↓
Monitor
```

## Rollback

A release process is incomplete if there is no answer to:

> "What do we do if the new version fails?"

Rollback can mean:

- deploy previous application version
- switch traffic
- restore a known-good image
- revert a configuration change

The exact mechanism depends on the architecture.

## Why this topic matters in the larger system

The purpose of this topic is to understand the engineering problem before memorizing a tool or command. A junior DevOps engineer should be able to explain the idea in plain language, connect it to the software lifecycle, and give a practical example.


## Practical exercise

Create a small Git repository and document one complete software-delivery flow. Start with a simple application, describe its development workflow, add a test step, package it, and write down how you would deploy, monitor and roll back the application. The objective is to connect the concepts rather than to build a large system.


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

### Practical: promote one artifact across environments

I want the release flow to look like:

```text
Git commit abc123
      ↓
build image abc123
      ↓
development
      ↓
testing
      ↓
staging
      ↓
production
```

I avoid rebuilding a different artifact for each environment. Environment-specific configuration should be injected separately.

For a failed production release, I want to know:

```text
what changed?
which artifact?
which configuration?
which deployment?
can I roll back?
```

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

For **Environments And Release Management**, the workflow I want to remember is:

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

I reduce the problem to a small application delivery example.

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
release, change, production, text, environments, result, verify, artifact, before, configuration, development, explain
```

I am keeping these here because when I search the repository later, these are the words I am likely to remember.

### If I had to explain this to a teammate

I would start with the problem, not the tool:

> "The reason we use Environments And Release Management here is because it gives us a repeatable way to handle environments and release management. The practical part is that I can inspect the current state, make the intended change, verify it, and troubleshoot it without relying on manual guesswork."

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
