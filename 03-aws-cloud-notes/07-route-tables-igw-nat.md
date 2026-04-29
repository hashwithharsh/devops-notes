# Route Tables, Internet Gateway and NAT

## Route table

Controls where traffic is sent.

Example:

```text
0.0.0.0/0 → Internet Gateway
```

## Internet Gateway

Provides internet connectivity for appropriately routed public resources.

## NAT Gateway

Allows private-subnet resources to initiate outbound connections through a public path without directly exposing those private resources to unsolicited inbound internet traffic through the NAT path.

## Typical architecture

```text
Internet
   ↓
Internet Gateway
   ↓
Public Subnet
   ↓
NAT Gateway
   ↓
Private Subnet
   ↓
Application
```

## Troubleshooting

If a private EC2 instance cannot download updates, check:

1. subnet route table
2. NAT path
3. NAT placement/configuration
4. security group
5. NACL
6. DNS
7. target endpoint

## Why this topic matters in the larger system

AWS knowledge is useful for DevOps only when services are understood as parts of an architecture. Learn the responsibility of each service, how services interact, what the security boundary is, and how to troubleshoot the path between them.


## Practical exercise

Build a small AWS environment only when you understand the cost and cleanup implications. Document the architecture, IAM permissions, network path, monitoring, and teardown procedure. Use Terraform for repeatability and AWS CLI commands for inspection.

## Deep explanation

AWS networking problems are often caused by a missing hop in the path.

Consider a private EC2 instance downloading a package:

```text
EC2
 ↓
Private subnet route table
 ↓
NAT Gateway
 ↓
Public subnet route
 ↓
Internet Gateway
 ↓
Internet
```

If any required component is missing, traffic can fail.

### Common misconception

A NAT Gateway does not make a private subnet public.

It provides a path for supported outbound connections from private resources.

### Route table thinking

A route is essentially:

```text
destination → target
```

For example:

```text
0.0.0.0/0 → NAT Gateway
```

means traffic destined for addresses outside the local network should use that target.

### Troubleshooting method

Do not start by changing security groups.

Trace the packet path from source to destination and verify each hop.

## My Practical Reference

I want this note to be useful when I am actually working, not only when I am studying. I therefore keep a practical procedure here that I can return to later.

### What I do before changing anything

1. Identify the environment and target.
2. Check the current state.
3. Decide what result I expect.
4. Make the smallest controlled change.
5. Verify the result independently.

### Practical application

### Practical: troubleshoot private-subnet internet access

I check the route table associated with the private subnet and verify that the default route points to the intended NAT gateway.

Then I check the NAT gateway's subnet and its public routing.

The conceptual path is:

```text
private instance
→ private route table
→ NAT Gateway
→ public subnet route
→ Internet Gateway
→ internet
```

If the private instance cannot download packages, I verify each hop before changing security groups.

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

For **Route Tables Igw Nat**, the workflow I want to remember is:

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

I am trying to learn AWS as an infrastructure system rather than as a list of service definitions. Whenever I read a service, I ask where it sits in the request path and what IAM/network boundary controls it.

### Where I would use it

I use a test AWS resource and confirm account/Region before changing anything.

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
aws sts get-caller-identity
aws configure list
aws ec2 describe-instances
aws ec2 describe-security-groups
aws ec2 describe-route-tables
aws logs describe-log-groups

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

One mistake I want to avoid is solving every problem by giving more permissions or making a resource public. I should first identify the exact action, identity, resource and network path.

Another thing I want to avoid is copying a command from a random blog without understanding what resource it is changing. A command can be syntactically correct and still be completely wrong for the environment.

### Keywords from this note

These are the terms I should recognize when I come back to the note:

```text
gateway, internet, route, private, subnet, text, path, public, table, check, verify, does
```

I am keeping these here because when I search the repository later, these are the words I am likely to remember.

### If I had to explain this to a teammate

I would start with the problem, not the tool:

> "The reason we use Route Tables Igw Nat here is because it gives us a repeatable way to handle route tables igw nat. The practical part is that I can inspect the current state, make the intended change, verify it, and troubleshoot it without relying on manual guesswork."

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
