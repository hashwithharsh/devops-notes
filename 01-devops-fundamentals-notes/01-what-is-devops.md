# What Is DevOps?

## First understand the problem

Imagine a development team finishes an application.

The developers say:

> "It works on my machine."

Operations receives the application and discovers that:

- dependencies are different
- configuration is different
- ports are different
- permissions are different
- deployment steps are manual
- nobody has a reliable rollback process

This is one of the problems DevOps tries to solve.

## Definition

DevOps is a combination of culture, practices, automation, collaboration, measurement and operational responsibility used to improve the software delivery lifecycle.

It is not simply:

- Jenkins
- Docker
- Kubernetes
- Terraform
- AWS

Those are tools.

## DevOps lifecycle

```text
Plan
  ↓
Code
  ↓
Build
  ↓
Test
  ↓
Release
  ↓
Deploy
  ↓
Operate
  ↓
Monitor
  ↓
Feedback
  └──────────────→ Plan
```

The important idea is the feedback loop.

## Main goals

### Faster delivery

Automation reduces manual work.

### Consistency

The same process should produce the same result.

### Reliability

Changes should be tested and deployed in controlled ways.

### Fast feedback

A developer should know quickly when a change breaks something.

### Collaboration

Development and operations should not behave like isolated departments.

## DevOps engineer mindset

Do not ask only:

> "Which command should I run?"

Ask:

> "What system state am I trying to achieve, and how can I make that state repeatable?"

That mindset becomes extremely important when you move from manual Linux administration into Terraform, Ansible, containers and Kubernetes.

## Interview answer

A strong junior-level answer:

> DevOps is a culture and engineering approach that combines development and operations through collaboration, automation, continuous feedback and reliable delivery practices. Tools such as Git, CI/CD, Docker, Kubernetes, Terraform and cloud platforms help implement those practices.

## Remember

**DevOps = people + process + technology + feedback**

Not:

**DevOps = Jenkins**

## Why this topic matters in the larger system

The purpose of this topic is to understand the engineering problem before memorizing a tool or command. A junior DevOps engineer should be able to explain the idea in plain language, connect it to the software lifecycle, and give a practical example.


## Practical exercise

Create a small Git repository and document one complete software-delivery flow. Start with a simple application, describe its development workflow, add a test step, package it, and write down how you would deploy, monitor and roll back the application. The objective is to connect the concepts rather than to build a large system.

## Deep explanation

DevOps is best understood as a system for reducing the friction between changing software and operating software. Development produces change; operations provides the environment in which that change must behave reliably. DevOps practices connect those activities with version control, automation, testing, observability and feedback.

A useful distinction is between **responsibility** and **specialization**. A company can still have developers, platform engineers, SREs and operations specialists. DevOps does not require every person to perform every job. Instead, it encourages teams to share ownership of delivery and reliability outcomes.

A practical DevOps loop is:

```text
Plan → Code → Review → Build → Test → Package → Deploy → Observe → Learn
  ↑                                                            |
  └────────────────────── feedback ───────────────────────────┘
```

The loop becomes valuable when every transition has a reliable mechanism. Git records the change, CI verifies it, an artifact registry stores the built output, deployment automation moves that exact artifact, and monitoring provides evidence about the result.

### What DevOps is not

DevOps is not a job title made from a list of products. Someone who knows Jenkins commands but cannot explain a failed deployment is not demonstrating strong DevOps fundamentals. Similarly, knowing Terraform syntax without understanding state and change management is incomplete.

### A useful interview structure

When asked what DevOps means, answer in four parts:

1. It is a culture and engineering approach.
2. It improves collaboration between development and operations.
3. It uses automation, version control, testing and feedback.
4. The result is more reliable and repeatable software delivery.

That answer is stronger than listing tools.

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

For **What Is Devops**, the workflow I want to remember is:

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

## My field notes

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
devops, change, automation, development, feedback, operations, result, answer, state, terraform, collaboration, delivery
```

I am keeping these here because when I search the repository later, these are the words I am likely to remember.

### If I had to explain this to a teammate

I would start with the problem, not the tool:

> "The reason we use What Is Devops here is because it gives us a repeatable way to handle what is devops. The practical part is that I can inspect the current state, make the intended change, verify it, and troubleshoot it without relying on manual guesswork."

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
