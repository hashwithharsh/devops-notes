# CI/CD Interview Scenarios

## Jenkins vs GitHub Actions

Jenkins is a general automation server with a large plugin ecosystem and self-managed infrastructure.

GitHub Actions is tightly integrated with GitHub repositories and uses workflow files with hosted or self-hosted runners.

## Jenkins vs Argo CD

They solve different problems.

```text
Jenkins → CI/automation
Argo CD → Kubernetes GitOps delivery
```

They can be used together.

## Why GitOps?

Git provides:

- history
- review
- auditability
- rollback through desired-state changes

Argo CD adds reconciliation.

## Why not deploy directly from Jenkins?

You can, but GitOps separates CI from deployment and makes the cluster's desired state visible and reviewable in Git.

## Scenario

### Developer pushes code

Answer:

```text
GitHub
 ↓
CI
 ↓
Test
 ↓
Build
 ↓
Scan
 ↓
Push image
 ↓
Update GitOps manifest
 ↓
Argo CD
 ↓
Kubernetes
 ↓
Health verification
```

This is a strong architecture to explain in a junior DevOps interview.

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

I turn the topic into a scenario rather than memorizing an answer. Example workflow:

```text
state the symptom
→ collect evidence
→ identify likely layers
→ test the highest-probability hypothesis
→ fix safely
→ verify
→ explain prevention
```

I practice saying the answer out loud in 60–90 seconds, then expand it with commands when asked.

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

For **Ci Cd Interview Scenarios**, the workflow I want to remember is:

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
argo, jenkins, github, gitops, kubernetes, test, text, verify, change, evidence, explain, hypothesis
```

I am keeping these here because when I search the repository later, these are the words I am likely to remember.

### If I had to explain this to a teammate

I would start with the problem, not the tool:

> "The reason we use Ci Cd Interview Scenarios here is because it gives us a repeatable way to handle ci cd interview scenarios. The practical part is that I can inspect the current state, make the intended change, verify it, and troubleshoot it without relying on manual guesswork."

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
