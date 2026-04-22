# Git Recovery Playbook

This is the page I want open when I think I have lost work.

## First rule

Don't panic and don't run more cleanup commands.

Check:

```bash
git status
git log --oneline --decorate --graph --all
git reflog
```

## Accidentally shared a bad commit

If the history is already shared, I normally prefer:

```bash
git revert <commit>
```

because it creates a new commit that reverses the old change.

## Accidentally reset a branch

Check:

```bash
git reflog
```

Find the old commit:

```bash
git show <hash>
```

Then create a safety branch:

```bash
git switch -c recovered-work <hash>
```

## Uncommitted work disappeared

This is different. Reflog mainly helps with commits and references; it is not a backup for arbitrary uncommitted changes.

That is why I should not use Git as a substitute for committing work I care about.

## Before destructive Git commands

```text
status
 ↓
log
 ↓
reflog if necessary
 ↓
understand the desired result
 ↓
run command
 ↓
verify
```

Git becomes much less scary when I slow down before changing history.
