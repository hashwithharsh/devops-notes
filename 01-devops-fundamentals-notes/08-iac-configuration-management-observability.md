# IaC, Configuration Management and Observability

These concepts are related but solve different problems.

## Infrastructure as Code

Terraform is commonly used to describe infrastructure.

Example:

```text
VPC
Subnet
Security Group
EC2
Load Balancer
```

The infrastructure definition is stored in Git.

## Configuration management

Ansible is commonly used to configure operating systems and applications.

Example:

```text
Install nginx
Create user
Copy config
Start service
```

## Containers

Docker packages applications and dependencies into images.

## Orchestration

Kubernetes manages container workloads at cluster scale.

## Observability

Monitoring and logging help answer:

> What is happening inside the system?

Typical signals:

- metrics
- logs
- traces

## Putting it together

```text
Terraform → Infrastructure
Ansible   → Configuration
Docker    → Application packaging
Kubernetes → Workload orchestration
CI/CD     → Delivery
Monitoring → Feedback
```

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

### Practical: separate the three responsibilities

I use a small example:

```text
Terraform
→ creates EC2/network

Ansible
→ configures nginx/users/packages

CloudWatch/Prometheus/Grafana
→ tells me whether the system behaves correctly
```

This prevents me from expecting one tool to solve every infrastructure problem.

A useful workflow is:

```text
provision → configure → deploy → observe → troubleshoot
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

For **Iac Configuration Management Observability**, the workflow I want to remember is:

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
text, change, configuration, result, terraform, ansible, explain, infrastructure, verify, answer, application, before
```

I am keeping these here because when I search the repository later, these are the words I am likely to remember.

### If I had to explain this to a teammate

I would start with the problem, not the tool:

> "The reason we use Iac Configuration Management Observability here is because it gives us a repeatable way to handle iac configuration management observability. The practical part is that I can inspect the current state, make the intended change, verify it, and troubleshoot it without relying on manual guesswork."

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
