# Terraform CLI Workflow

## Format

```bash
terraform fmt
```

## Initialize

```bash
terraform init
```

Downloads providers/modules and prepares the working directory.

## Validate

```bash
terraform validate
```

Checks configuration syntax and internal consistency.

## Plan

```bash
terraform plan
```

Shows proposed changes.

## Apply

```bash
terraform apply
```

Creates/changes infrastructure.

## Destroy

```bash
terraform destroy
```

Removes managed resources.

## Professional workflow

```text
fmt
 ↓
validate
 ↓
plan
 ↓
review
 ↓
apply
 ↓
verify
```

Do not skip plan review for important environments.

## Why this topic matters in the larger system

Terraform is most useful when infrastructure becomes repeatable and reviewable. The critical concepts are desired state, dependency graphs, state, plans, lifecycle and safe collaboration—not simply writing resource blocks.


## Practical exercise

Build a small disposable environment such as a network plus one compute or storage resource. Run init, format, validate and plan. Change one property, inspect the new plan, apply it, inspect state, then destroy it. Repeat using a remote backend when you understand the local workflow.

## Deep explanation

The Terraform CLI workflow creates a safety boundary between writing configuration and changing infrastructure.

### fmt

Makes configuration consistent.

### validate

Checks whether the configuration is structurally valid.

### plan

Shows the proposed infrastructure change.

### apply

Performs the change.

A useful production workflow is:

```text
Developer change
 ↓
fmt
 ↓
validate
 ↓
plan
 ↓
review
 ↓
apply
 ↓
verification
```

### Why plan matters

Suppose you change one security-group rule and Terraform proposes replacement of an entire instance.

That should trigger investigation before apply.

A good Terraform engineer asks:

> Why is Terraform proposing this action?

rather than:

> How do I make the plan go away?

The plan is one of the most valuable safety features in the workflow.

## My Practical Reference

I want this note to be useful when I am actually working, not only when I am studying. I therefore keep a practical procedure here that I can return to later.

### What I do before changing anything

1. Identify the environment and target.
2. Check the current state.
3. Decide what result I expect.
4. Make the smallest controlled change.
5. Verify the result independently.

### Practical application

I test in a disposable Terraform directory and use `terraform fmt`, `terraform validate` and `terraform plan` before applying. I read the plan instead of blindly accepting it, verify the real infrastructure after changes, and destroy disposable resources afterward.

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

For **Terraform Cli Workflow**, the workflow I want to remember is:

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

The thing I want to keep clear in my head is that Terraform is not a script that runs top-to-bottom. It builds a plan from configuration, state and provider information and then tries to make reality match the desired configuration.

### Where I would use it

I use a small disposable configuration and read the plan before apply.

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
terraform fmt -check
terraform validate
terraform plan
terraform state list
terraform show
terraform output

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

The biggest beginner mistake is seeing a plan and clicking apply without asking why a resource is being replaced. Replacement can be much more significant than an in-place update.

Another thing I want to avoid is copying a command from a random blog without understanding what resource it is changing. A command can be syntactically correct and still be completely wrong for the environment.

### Keywords from this note

These are the terms I should recognize when I come back to the note:

```text
terraform, plan, apply, change, validate, bash, workflow, configuration, destroy, state, changes, disposable
```

I am keeping these here because when I search the repository later, these are the words I am likely to remember.

### If I had to explain this to a teammate

I would start with the problem, not the tool:

> "The reason we use Terraform Cli Workflow here is because it gives us a repeatable way to handle terraform cli workflow. The practical part is that I can inspect the current state, make the intended change, verify it, and troubleshoot it without relying on manual guesswork."

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
