# Stash, Tags and Cherry-Pick

## Stash

Temporarily save working changes:

```bash
git stash
git stash list
git stash pop
```

## Tags

Mark release points:

```bash
git tag v1.0.0
git push origin v1.0.0
```

## Cherry-pick

Apply one commit to another branch:

```bash
git cherry-pick <commit>
```

Useful when one specific fix is needed without merging an entire feature branch.

## Warning

Cherry-pick creates a new commit. The resulting history is not identical to simply moving the original commit.

## Why this topic matters in the larger system

Git is the history and collaboration layer for DevOps work. Strong Git knowledge means understanding repository state, references, commits and recovery—not just memorizing push and pull commands.


## Practical exercise

Create a repository with `main` and two feature branches. Make conflicting changes, resolve a conflict, create a bad commit, recover it with reflog, and practice both merge and rebase. Do this in a disposable repository until the history is easy to visualize.


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

### Practical lab: save unfinished work and move one commit

For unfinished work:

```bash
git stash push -m "wip: login changes"
git status
git stash list
```

Bring it back:

```bash
git stash pop
```

For a release marker:

```bash
git tag -a v1.0.0 -m "release v1.0.0"
git tag
```

For moving one specific commit onto another branch:

```bash
git cherry-pick <commit>
```

If a cherry-pick conflicts:

```bash
git status
# fix conflict
git add <file>
git cherry-pick --continue
```

Abort if necessary:

```bash
git cherry-pick --abort
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

For **Stash Tags Cherry Pick**, the workflow I want to remember is:

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

I am treating Git as a model of repository state, not just a list of commands. If I understand working tree, index, commits and references, most Git commands become easier to reason about.

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
git status
git diff
git diff --cached
git log --oneline --graph --decorate --all
git reflog
git branch -vv

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

The biggest mistake is running a destructive command without first looking at the current state. Before reset, rebase or cleanup I check status and history, and I remember that reflog can be extremely useful for recovering local work.

Another thing I want to avoid is copying a command from a random blog without understanding what resource it is changing. A command can be syntactically correct and still be completely wrong for the environment.

### Keywords from this note

These are the terms I should recognize when I come back to the note:

```text
bash, stash, cherry-pick, commit, changes, change, result, status, v1.0.0, verify, before, branch
```

I am keeping these here because when I search the repository later, these are the words I am likely to remember.

### If I had to explain this to a teammate

I would start with the problem, not the tool:

> "The reason we use Stash Tags Cherry Pick here is because it gives us a repeatable way to handle stash tags cherry pick. The practical part is that I can inspect the current state, make the intended change, verify it, and troubleshoot it without relying on manual guesswork."

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
