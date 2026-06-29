# GitHub Actions Deep Dive

Workflow files are stored under:

```text
.github/workflows/
```

Example:

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Install dependencies
        run: pip install -r requirements.txt

      - name: Test
        run: pytest
```

## Concepts

### Event

Triggers the workflow.

### Job

A unit of execution.

### Step

A command or action within a job.

### Runner

Machine executing the job.

### Action

Reusable functionality.

### Secret

Sensitive configuration managed by GitHub.

## DevOps principle

Keep workflows readable.

If one workflow becomes hundreds of lines of difficult shell scripting, consider reusable actions, scripts or separate workflows.

## Why this topic matters in the larger system

CI/CD is about creating a trustworthy path from source code to a tested and observable release. Jenkins and GitHub Actions can automate CI, while Argo CD can implement GitOps-based Kubernetes delivery.


## Practical exercise

Create a small pipeline that checks out code, runs tests, builds a Docker image and publishes it. Then separate deployment from CI using a GitOps repository and Argo CD. Introduce a broken test, broken image tag and invalid Kubernetes manifest and practice finding the first failing layer.

## Deep explanation

A GitHub Actions workflow has several levels:

```text
Workflow
 ├── Event trigger
 ├── Job
 │    ├── Step
 │    ├── Step
 │    └── Step
 └── Job
```

Jobs may execute independently or depend on other jobs.

### Example architecture

```text
test
  ↓
build
  ↓
publish
```

The build job should not run if the required test job fails.

### Runner choice

Hosted runners are convenient because GitHub provides the execution environment.

Self-hosted runners provide more control but introduce operational responsibility:

- patching
- isolation
- credentials
- network access
- cleanup
- runner compromise

### Security

A CI runner is executing code from a repository. Treat it as a privileged execution environment.

Limit cloud permissions and avoid giving untrusted pull requests access to powerful production credentials.

## My Practical Reference

I want this note to be useful when I am actually working, not only when I am studying. I therefore keep a practical procedure here that I can return to later.

### What I do before changing anything

1. Identify the environment and target.
2. Check the current state.
3. Decide what result I expect.
4. Make the smallest controlled change.
5. Verify the result independently.

### Practical application

### Practical lab: create a simple GitHub Actions CI workflow

A basic workflow looks like:

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.12'

      - name: Install dependencies
        run: pip install -r requirements.txt

      - name: Run tests
        run: pytest
```

I first get tests working. Then I add image building, scanning and publishing one stage at a time.

This makes failures much easier to isolate than starting with a huge workflow.

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

For **Github Actions Deep Dive**, the workflow I want to remember is:

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
actions, github, name, test, workflow, text, install, result, runner, stage, step, change
```

I am keeping these here because when I search the repository later, these are the words I am likely to remember.

### If I had to explain this to a teammate

I would start with the problem, not the tool:

> "The reason we use Github Actions Deep Dive here is because it gives us a repeatable way to handle github actions deep dive. The practical part is that I can inspect the current state, make the intended change, verify it, and troubleshoot it without relying on manual guesswork."

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
