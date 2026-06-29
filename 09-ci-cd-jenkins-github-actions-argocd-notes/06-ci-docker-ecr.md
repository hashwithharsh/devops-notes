# CI: Docker Build and Registry

A common container CI pipeline:

```text
Git Push
 ↓
Tests
 ↓
Docker Build
 ↓
Image Scan
 ↓
Tag
 ↓
Push Registry
```

## Example

```bash
docker build -t myapp:${GIT_SHA} .
docker run --rm myapp:${GIT_SHA} pytest
```

Then push to a registry such as ECR.

## Tagging

Useful tags include:

```text
commit SHA
release version
```

A unique commit-based tag allows you to identify exactly which source produced the image.

## Security

Do not expose registry credentials in logs.

Use the CI platform's secret/identity mechanism or cloud-native workload identity where supported.

## Why this topic matters in the larger system

CI/CD is about creating a trustworthy path from source code to a tested and observable release. Jenkins and GitHub Actions can automate CI, while Argo CD can implement GitOps-based Kubernetes delivery.


## Practical exercise

Create a small pipeline that checks out code, runs tests, builds a Docker image and publishes it. Then separate deployment from CI using a GitOps repository and Argo CD. Introduce a broken test, broken image tag and invalid Kubernetes manifest and practice finding the first failing layer.


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

### Practical lab: build and publish a container

My CI flow is:

```text
checkout
→ docker build
→ tag with commit SHA
→ authenticate to registry
→ push
```

I prefer an immutable identifier such as the Git SHA:

```text
myapp:9f3c2a1
```

rather than relying only on `latest`.

Before deployment I verify that the exact image exists in the registry. If a deployment is broken, I can identify exactly which source commit produced the image.

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

For **Ci Docker Ecr**, the workflow I want to remember is:

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

I am keeping CI/CD notes around the delivery flow rather than around one specific product. Jenkins and GitHub Actions are implementations of automation; GitOps adds another way of separating build and deployment responsibilities.

### Where I would use it

I create a disposable repository and reproduce the exact state before trying the fix.

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
git rev-parse HEAD
docker image inspect <image>
kubectl rollout status deployment/<name>
kubectl get pods
kubectl logs <pod>

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

A pipeline that is green only because it has very few checks is not necessarily a good pipeline. I want every important stage to have a clear success condition and useful failure output.

Another thing I want to avoid is copying a command from a random blog without understanding what resource it is changing. A command can be syntactically correct and still be completely wrong for the environment.

### Keywords from this note

These are the terms I should recognize when I come back to the note:

```text
docker, text, build, registry, verify, change, commit, identify, image, push, result, before
```

I am keeping these here because when I search the repository later, these are the words I am likely to remember.

### If I had to explain this to a teammate

I would start with the problem, not the tool:

> "The reason we use Ci Docker Ecr here is because it gives us a repeatable way to handle ci docker ecr. The practical part is that I can inspect the current state, make the intended change, verify it, and troubleshoot it without relying on manual guesswork."

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
