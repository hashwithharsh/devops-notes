# Docker Compose

Compose describes multi-container applications.

Example:

```yaml
services:
  web:
    build: .
    ports:
      - "8080:5000"
    depends_on:
      - redis

  redis:
    image: redis:7-alpine
```

Run:

```bash
docker compose up -d
docker compose ps
docker compose logs
docker compose down
```

## Why Compose?

Instead of remembering a long sequence of `docker run` commands, configuration becomes version-controlled.

## Important

Compose is excellent for local/dev multi-container environments.

It is not itself a replacement for Kubernetes in large-scale orchestration scenarios.

## Why this topic matters in the larger system

Docker is easier to troubleshoot when containers are understood as isolated Linux processes with filesystem, network and resource boundaries. The Docker CLI is only the interface; the important skill is understanding the resulting process, image and network state.


## Practical exercise

Containerize a small application. Build it, run it with environment variables, publish a port, mount persistent data, connect it to another container on a user-defined network, inspect it, intentionally break the configuration, and troubleshoot the failure from logs and metadata.


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

### Practical lab: run a small two-service application

A Compose file can describe:

```text
app
db
network
volumes
environment
```

Start:

```bash
docker compose up -d
```

Inspect:

```bash
docker compose ps
docker compose logs -f
```

Stop:

```bash
docker compose down
```

If persistent volumes are intentionally disposable:

```bash
docker compose down -v
```

I am careful with `-v` because it can remove data I wanted to keep.

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

For **Compose**, the workflow I want to remember is:

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

I am learning Docker from the inside out: image, container process, filesystem, network and resource limits. The CLI is only the interface I use to inspect those things.

### Where I would use it

I reproduce it with a disposable container and inspect the actual container state.

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
docker ps -a
docker images
docker inspect <container>
docker logs <container>
docker exec -it <container> sh
docker network ls

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

I do not assume that a container being 'Up' means the application is healthy. A process can be running while the application is misconfigured or unreachable.

Another thing I want to avoid is copying a command from a random blog without understanding what resource it is changing. A command can be syntactically correct and still be completely wrong for the environment.

### Keywords from this note

These are the terms I should recognize when I come back to the note:

```text
docker, compose, network, bash, inspect, logs, change, result, verify, configuration, down, environment
```

I am keeping these here because when I search the repository later, these are the words I am likely to remember.

### If I had to explain this to a teammate

I would start with the problem, not the tool:

> "The reason we use Compose here is because it gives us a repeatable way to handle compose. The practical part is that I can inspect the current state, make the intended change, verify it, and troubleshoot it without relying on manual guesswork."

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
