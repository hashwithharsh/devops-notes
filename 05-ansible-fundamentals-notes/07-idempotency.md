# Ansible Idempotency

Idempotency means that applying the desired state repeatedly should not keep making unnecessary changes.

Example:

```yaml
ansible.builtin.package:
  name: nginx
  state: present
```

First run:

```text
changed=1
```

Later run:

```text
changed=0
```

assuming the system is already in the desired state.

## Why it matters

Without idempotency, automation can cause:

- repeated restarts
- unnecessary changes
- unpredictable results
- deployment instability

## Good automation

Describe the desired state rather than writing a sequence of commands that always execute blindly.

## Why this topic matters in the larger system

Ansible becomes valuable when repetitive Linux configuration is converted into predictable, reviewable and idempotent automation. The goal is to describe desired state rather than write a long list of fragile shell commands.


## Practical exercise

Create two disposable Linux VMs. Use inventory and a playbook to create users, install packages, deploy a configuration template, start a service and verify it. Run the playbook twice and investigate why the second run should report little or no change.

## Deep explanation

Idempotency is one of the strongest reasons to use Ansible modules instead of raw shell commands.

Consider:

```yaml
ansible.builtin.service:
  name: nginx
  state: started
```

The task expresses a desired state.

If nginx is already running, Ansible does not need to restart it.

Contrast this with:

```yaml
ansible.builtin.shell: systemctl restart nginx
```

That command executes every time.

Repeated execution can cause unnecessary disruption.

### Idempotency test

A useful test for an Ansible role is:

```text
First run  → expected changes
Second run → no unnecessary changes
```

If the second run keeps reporting changes, investigate why.

Possible causes:

- command always reports changed
- file content is unstable
- timestamp changes
- generated output is non-deterministic
- incorrect module choice

Idempotency is a property of the automation design, not a word to add to the README.

## My Practical Reference

I want this note to be useful when I am actually working, not only when I am studying. I therefore keep a practical procedure here that I can return to later.

### What I do before changing anything

1. Identify the environment and target.
2. Check the current state.
3. Decide what result I expect.
4. Make the smallest controlled change.
5. Verify the result independently.

### Practical application

I test against disposable Linux hosts. I first run `ansible all -i inventory.ini -m ping`, then perform the smallest task that demonstrates the concept and run it twice to check idempotency. For failures I trace inventory → SSH → privilege escalation → task arguments → target state.

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

For **Idempotency**, the workflow I want to remember is:

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

The part of Ansible I want to remember is the desired-state idea. I am not trying to write a shell script in YAML. I am describing what the target should look like.

### Where I would use it

I test it against a disposable target and run the playbook twice.

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
ansible all -i inventory.ini -m ping
ansible-inventory -i inventory.ini --graph
ansible-playbook -i inventory.ini site.yml --check
ansible-playbook -i inventory.ini site.yml -vv

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

If I find myself putting a lot of shell commands into an Ansible playbook, I stop and check whether an Ansible module already represents the desired state better.

Another thing I want to avoid is copying a command from a random blog without understanding what resource it is changing. A command can be syntactically correct and still be completely wrong for the environment.

### Keywords from this note

These are the terms I should recognize when I come back to the note:

```text
state, changes, idempotency, ansible, desired, text, first, nginx, result, second, state., task
```

I am keeping these here because when I search the repository later, these are the words I am likely to remember.

### If I had to explain this to a teammate

I would start with the problem, not the tool:

> "The reason we use Idempotency here is because it gives us a repeatable way to handle idempotency. The practical part is that I can inspect the current state, make the intended change, verify it, and troubleshoot it without relying on manual guesswork."

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
