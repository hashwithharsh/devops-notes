# Linux Administration Notes

**Author:** Harsh Yadav

This is my Linux reference for administration, networking, storage, services, Bash and troubleshooting.

I am keeping this as a long-term study/reference repo. The useful part for me is the combination of explanation, practical commands, small labs and the troubleshooting notes I tend to forget.

I don't expect every note to have exactly the same structure. I write more detail for topics that I actually need to revisit often.


## My placement reference

## My Linux habit

When I am troubleshooting a Linux machine, I want to collect evidence before changing anything.

```text
What changed?
   ↓
What is the exact symptom?
   ↓
Which layer could cause it?
   ↓
What command proves or disproves that?
   ↓
What is the smallest safe fix?
   ↓
How do I verify it?
```

I am trying to build this habit because memorizing `systemctl`, `ss`, `df` and `journalctl` is less useful if I don't know which question each command answers.

## How I study this repo

I study Linux by doing, not by trying to memorize a command list.

My normal progression is:

```text
Linux basics
   ↓
files / permissions / users
   ↓
processes / services / systemd
   ↓
packages
   ↓
storage
   ↓
networking
   ↓
Bash scripting
   ↓
troubleshooting
```

For each topic I try the commands on a disposable Linux VM. I check the current state first, make a small change, and verify it.

For troubleshooting topics, I deliberately create a small failure when it is safe to do so. Then I work from symptom → evidence → root cause → fix → verification.

When revising for interviews, I focus on the commands I would actually use and, more importantly, what question each command answers.
