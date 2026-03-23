# Linux Permissions and umask

## Permission model

For a file:

```text
-rwxr-x---
```

The three permission groups are:

```text
owner | group | others
```

## Numeric permissions

```text
r = 4
w = 2
x = 1
```

Example:

```bash
chmod 750 script.sh
```

means:

```text
owner  = rwx = 7
group  = r-x = 5
others = --- = 0
```

## Ownership

```bash
chown user:group file
```

## umask

umask removes permissions from the default permission set.

A common simplified model:

```text
777 - umask = directory permission
666 - umask = file permission
```

For example:

```text
umask 022
```

commonly results in:

```text
directory → 755
file       → 644
```

The exact result also depends on the application's default creation mode.

## Troubleshooting permission denied

Check:

```bash
ls -l file
id
namei -l /path/to/file
```

Remember that every parent directory in a path also needs appropriate execute/search permission.

## Why this topic matters in the larger system

Linux is the operating-system foundation behind a large part of cloud and DevOps infrastructure. The important skill is not remembering hundreds of commands; it is being able to reason from a symptom to the relevant subsystem and then verify the diagnosis.


## Practical exercise

Use a disposable Linux VM. Create a user, configure SSH access, attach or create a filesystem, mount it persistently, install a service, configure it with systemd, open a required network port, and troubleshoot at least one intentionally introduced failure. Record the commands and, more importantly, the reasoning used to find the failure.


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

### Practical lab: understand permissions by changing one variable at a time

I create a test directory:

```bash
mkdir -p ~/perm-lab
cd ~/perm-lab
touch secret.txt
chmod 640 secret.txt
ls -l secret.txt
```

I create a group and test group access:

```bash
sudo groupadd appteam
sudo usermod -aG appteam "$USER"
sudo chgrp appteam secret.txt
chmod 640 secret.txt
```

For a directory, I remember that `x` means the ability to traverse it. That is why directory permissions behave differently from file permissions.

I test numeric and symbolic changes:

```bash
chmod 600 secret.txt
chmod g+r secret.txt
```

Then I check the default permission mask:

```bash
umask
```

**Troubleshooting approach:** when a process gets "Permission denied", I check the full path, ownership, mode bits, parent-directory traversal permissions, ACLs if used, and security controls such as SELinux before randomly changing permissions to `777`.

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

For **Permissions And Umask**, the workflow I want to remember is:

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

## My working understanding

### Why I am keeping this note

I am keeping this note mainly because I know I will forget some of the exact commands later. The idea is to understand the layer first and then use the command as a way to inspect that layer.

### Where I would use it

I reproduce it on a disposable Linux VM so I can make the mistake safely.

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
hostnamectl
uname -r
df -Th
free -h
ip addr
ip route
ss -lntup
systemctl status <service>
journalctl -u <service>

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

The mistake I want to avoid is jumping straight to a fix. For example, opening a firewall port when the application is not even listening does not solve the real problem.

Another thing I want to avoid is copying a command from a random blog without understanding what resource it is changing. A command can be syntactically correct and still be completely wrong for the environment.

### Keywords from this note

These are the terms I should recognize when I come back to the note:

```text
permissions, text, permission, umask, bash, file, secret.txt, directory, test, check, chmod, group
```

I am keeping these here because when I search the repository later, these are the words I am likely to remember.

### If I had to explain this to a teammate

I would start with the problem, not the tool:

> "The reason we use Permissions And Umask here is because it gives us a repeatable way to handle permissions and umask. The practical part is that I can inspect the current state, make the intended change, verify it, and troubleshoot it without relying on manual guesswork."

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
