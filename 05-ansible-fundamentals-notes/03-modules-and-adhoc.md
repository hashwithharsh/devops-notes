# Ansible Modules and Ad-Hoc Commands

A module performs an operation.

Examples:

- package
- service
- copy
- file
- user
- command
- shell
- template

## Ad-hoc

```bash
ansible web -i inventory.ini -m ping
ansible web -i inventory.ini -m command -a "uptime"
```

Install package:

```bash
ansible web -i inventory.ini   -m package   -a "name=nginx state=present"   --become
```

## command vs shell

`command` executes a command without a shell.

`shell` invokes a shell and therefore supports shell behavior such as pipelines, but that also introduces more shell-related complexity.

Prefer purpose-built modules when one exists.

Instead of:

```bash
shell: systemctl start nginx
```

prefer:

```yaml
ansible.builtin.service:
  name: nginx
  state: started
```

## Why this topic matters in the larger system

Ansible becomes valuable when repetitive Linux configuration is converted into predictable, reviewable and idempotent automation. The goal is to describe desired state rather than write a long list of fragile shell commands.


## Practical exercise

Create two disposable Linux VMs. Use inventory and a playbook to create users, install packages, deploy a configuration template, start a service and verify it. Run the playbook twice and investigate why the second run should report little or no change.


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

### Practical lab: use ad-hoc commands before writing a playbook

Check uptime:

```bash
ansible all -i inventory.ini -m command -a 'uptime'
```

Install a package:

```bash
ansible web -i inventory.ini -m ansible.builtin.dnf -a 'name=nginx state=present' --become
```

Start a service:

```bash
ansible web -i inventory.ini -m ansible.builtin.service -a 'name=nginx state=started enabled=true' --become
```

I use ad-hoc commands to learn module behavior and diagnose a host. Once the operation becomes repeatable, I move it into a playbook.

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

For **Modules And Adhoc**, the workflow I want to remember is:

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
ansible, state, command, shell, bash, inventory.ini, nginx, verify, ad-hoc, change, name, package
```

I am keeping these here because when I search the repository later, these are the words I am likely to remember.

### If I had to explain this to a teammate

I would start with the problem, not the tool:

> "The reason we use Modules And Adhoc here is because it gives us a repeatable way to handle modules and adhoc. The practical part is that I can inspect the current state, make the intended change, verify it, and troubleshoot it without relying on manual guesswork."

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
