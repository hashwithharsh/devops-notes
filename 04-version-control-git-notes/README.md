# Version Control Git Notes

**Author:** Harsh Yadav

My Git notebook for everyday workflow, branches, collaboration, history and recovery.

I am keeping this as a long-term study/reference repo. The useful part for me is the combination of explanation, practical commands, small labs and the troubleshooting notes I tend to forget.

I don't expect every note to have exactly the same structure. I write more detail for topics that I actually need to revisit often.


## My placement reference

## My Git rule

Before changing history, I inspect history.

```bash
git status
git log --oneline --graph --decorate --all
git reflog
```

I don't want to memorize recovery commands without understanding what I am recovering. The goal is to know where the branch/reference moved and what commit I actually want to point it at.

## How I study this repo

I study Git by reproducing operations in a disposable repository.

My progression is:

```text
basic workflow
   ↓
commits / staging
   ↓
branches
   ↓
merge / rebase
   ↓
remote collaboration
   ↓
tags
   ↓
stash
   ↓
history / reflog
   ↓
recovery
```

For every operation I check:

```bash
git status
git log --oneline --graph --decorate --all
```

I also practice the failure cases. Merge conflicts, accidental resets and incorrect commits are much easier to remember after I have actually recovered from them.

For interview preparation, I focus especially on the difference between the working tree, index, commits and references, because that makes the commands easier to explain instead of just memorizing them.
