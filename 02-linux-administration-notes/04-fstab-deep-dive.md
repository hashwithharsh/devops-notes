# /etc/fstab Deep Dive

`/etc/fstab` defines filesystems that Linux can mount automatically.

## Six fields

```text
device  mount_point  filesystem  options  dump  pass
```

Example:

```text
UUID=xxxx  /data  xfs  defaults,nofail  0  0
```

## Why UUID?

Device names such as `/dev/sdb1` can change depending on device discovery order.

UUID identifies the filesystem more consistently.

Check it:

```bash
lsblk -f
blkid
```

## Safe workflow

Before editing:

```bash
sudo cp /etc/fstab /etc/fstab.backup
```

Edit:

```bash
sudo vi /etc/fstab
```

Then test:

```bash
sudo systemctl daemon-reload
sudo mount -a
```

If `mount -a` reports an error, fix the entry before rebooting.

## nofail

For non-critical storage:

```text
nofail
```

can allow the system to boot even when that mount is unavailable.

Do not blindly add `nofail` to critical root/boot filesystems.

## Production rule

**Never reboot a production Linux system immediately after changing fstab without testing the configuration.**

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

### Practical lab: persistent mount using UUID

I first find the UUID:

```bash
lsblk -f
sudo blkid
```

Then I create the mount point:

```bash
sudo mkdir -p /data
```

I add a line similar to:

```text
UUID=<my-uuid> /data xfs defaults,nofail 0 0
```

**Important:** I test the entry before rebooting:

```bash
sudo mount -a
findmnt /data
df -Th /data
```

If `mount -a` returns an error, I fix it before rebooting. This is a safer habit than testing `/etc/fstab` by restarting a server and discovering that boot is affected.

To troubleshoot an existing entry:

```bash
findmnt /data
systemctl status local-fs.target
journalctl -b -p err
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

For **Fstab Deep Dive**, the workflow I want to remember is:

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
mount, bash, fstab, sudo, before, data, text, uuid, change, nofail, test, verify
```

I am keeping these here because when I search the repository later, these are the words I am likely to remember.

### If I had to explain this to a teammate

I would start with the problem, not the tool:

> "The reason we use Fstab Deep Dive here is because it gives us a repeatable way to handle fstab deep dive. The practical part is that I can inspect the current state, make the intended change, verify it, and troubleshoot it without relying on manual guesswork."

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
