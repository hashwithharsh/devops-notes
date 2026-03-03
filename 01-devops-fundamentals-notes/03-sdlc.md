# SDLC — Software Development Life Cycle

## What is SDLC?

SDLC is a structured way of describing how software is planned, developed, tested, deployed and maintained.

## Common phases

```text
Planning
   ↓
Requirements
   ↓
Design
   ↓
Development
   ↓
Testing
   ↓
Deployment
   ↓
Maintenance
```

The exact names vary between organizations.

## Planning

Questions:

- What are we building?
- Why are we building it?
- Who is the user?
- What is the scope?
- What resources are required?

## Requirements

Requirements describe expected behavior.

Example:

> User should be able to log in using email and password.

## Design

The team decides how the requirement will be implemented.

This can include:

- application architecture
- database design
- API design
- security
- infrastructure

## Development

Developers write and commit code.

This is where Git becomes important.

## Testing

Testing may include:

- unit tests
- integration tests
- functional tests
- security tests
- performance tests

## Deployment

The application is moved into an environment.

Modern DevOps tries to make deployment repeatable and automated.

## Maintenance

After release:

- monitor
- patch
- troubleshoot
- improve
- scale
- release new versions

## Where DevOps fits

DevOps is not a single SDLC phase.

It connects activities across the entire lifecycle.

```text
Plan → Code → Build → Test → Release → Deploy → Operate → Monitor
  ↑                                                        ↓
  └──────────────────── feedback ─────────────────────────┘
```

## Interview question

### Is DevOps a phase of SDLC?

No.

DevOps is a set of practices and culture applied across the software lifecycle.

## Why this topic matters in the larger system

The purpose of this topic is to understand the engineering problem before memorizing a tool or command. A junior DevOps engineer should be able to explain the idea in plain language, connect it to the software lifecycle, and give a practical example.


## Practical exercise

Create a small Git repository and document one complete software-delivery flow. Start with a simple application, describe its development workflow, add a test step, package it, and write down how you would deploy, monitor and roll back the application. The objective is to connect the concepts rather than to build a large system.

## Deep explanation

SDLC is a lifecycle model, not one universal process. Different organizations use different names and ordering, but the fundamental questions remain similar:

- What problem are we solving?
- What should the system do?
- How should it be designed?
- How will we verify it?
- How will we release it?
- How will we operate and improve it?

### Where DevOps changes the lifecycle

Traditional diagrams can make the lifecycle look linear:

```text
Requirements → Design → Development → Testing → Deployment
```

Real systems behave more like a loop:

```text
Requirement
   ↓
Change
   ↓
Build/Test
   ↓
Release
   ↓
Production
   ↓
Telemetry + user feedback
   ↓
New requirement
```

DevOps practices shorten this feedback loop.

### Example

Suppose a login service has a bug.

Without good automation:

```text
Developer fixes bug
→ manual build
→ manual test
→ operations receives package
→ manual deployment
→ user reports another issue
```

With a mature delivery process:

```text
Developer commit
→ automated tests
→ image build
→ security checks
→ staging
→ health checks
→ production
→ monitoring
```

The second flow does not eliminate human judgment. It removes repetitive execution so people can focus on decisions.

## My Practical Reference

I want this note to be useful when I am actually working, not only when I am studying. I therefore keep a practical procedure here that I can return to later.

### What I do before changing anything

1. Identify the environment and target.
2. Check the current state.
3. Decide what result I expect.
4. Make the smallest controlled change.
5. Verify the result independently.

### Practical application

For a practical lab, I first write the expected workflow on paper, then perform it with the smallest possible example. I verify each stage before moving on. I intentionally introduce one failure and document the symptom, evidence, fix and verification.

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

For **Sdlc**, the workflow I want to remember is:

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

## Things I would check on a real system

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
devops, text, sdlc, tests, build, change, deployment, design, development, release, result, test
```

I am keeping these here because when I search the repository later, these are the words I am likely to remember.

### If I had to explain this to a teammate

I would start with the problem, not the tool:

> "The reason we use Sdlc here is because it gives us a repeatable way to handle sdlc. The practical part is that I can inspect the current state, make the intended change, verify it, and troubleshoot it without relying on manual guesswork."

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
