# Aws Cloud Notes

**Author:** Harsh Yadav

These are my AWS notes, with a focus on architecture, CLI usage, IAM and practical troubleshooting.

I am keeping this as a long-term study/reference repo. The useful part for me is the combination of explanation, practical commands, small labs and the troubleshooting notes I tend to forget.

I don't expect every note to have exactly the same structure. I write more detail for topics that I actually need to revisit often.


## My placement reference

## How I think about AWS

I try to trace a request instead of thinking about services independently.

For most application problems I ask about three things:

```text
Identity
Network
Resource
```

If I know who is making the request, what network path it takes and which resource it is trying to reach, AWS troubleshooting becomes much less random.

## How I study this repo

I study AWS by connecting services into architectures instead of memorizing service definitions separately.

My usual order is:

```text
AWS basics / IAM
      ↓
VPC / networking
      ↓
EC2 / storage
      ↓
S3
      ↓
load balancing / scaling
      ↓
databases
      ↓
monitoring / logging
      ↓
AWS CLI / automation
```

For practical work I use a test account/resources and verify my identity and Region before making changes.

I normally draw the request path first, then ask:

```text
Who is making the request?
What resource is involved?
What network path does it take?
Which policy/security control can block it?
```

For interviews, I revise the architecture and troubleshooting flow rather than trying to memorize every AWS option.
