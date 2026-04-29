# VPC, CIDR and Subnets

A VPC is an isolated virtual network.

## CIDR

A CIDR defines an IP range.

Example:

```text
10.0.0.0/16
```

This provides a larger address range from which subnets can be created.

Example:

```text
VPC: 10.0.0.0/16

Public subnet:  10.0.1.0/24
Private subnet: 10.0.2.0/24
```

## Subnet

A subnet is an IP range inside the VPC and is associated with an Availability Zone.

## Design thinking

Before creating subnets, decide:

- address space
- number of AZs
- public/private requirements
- workload tiers
- future growth

Poor CIDR planning can make later network expansion difficult.

## Why this topic matters in the larger system

AWS knowledge is useful for DevOps only when services are understood as parts of an architecture. Learn the responsibility of each service, how services interact, what the security boundary is, and how to troubleshoot the path between them.


## Practical exercise

Build a small AWS environment only when you understand the cost and cleanup implications. Document the architecture, IAM permissions, network path, monitoring, and teardown procedure. Use Terraform for repeatability and AWS CLI commands for inspection.

## Deep explanation

VPC networking becomes easier when you stop thinking of it as a collection of AWS menu options.

Start with IP design.

Example:

```text
VPC 10.0.0.0/16
│
├── Public-A 10.0.1.0/24
├── Public-B 10.0.2.0/24
├── Private-A 10.0.11.0/24
└── Private-B 10.0.12.0/24
```

The public/private label is architectural, not a magic property attached to a subnet.

A subnet is normally considered public when its route table provides a path through an Internet Gateway and the resource has appropriate public addressing.

A private subnet does not mean "no internet ever." A private workload may need outbound internet access for package updates, in which case a NAT path may be used.

### Why multiple subnets?

Separate tiers for:

- load balancers
- application servers
- databases

can make security boundaries clearer.

### Design question

Do not ask only:

> Which subnet should I put this EC2 instance in?

Ask:

> Who should be able to initiate traffic to this workload, and where should that traffic be allowed to go?

That question naturally leads to routing and security design.

## My Practical Reference

I want this note to be useful when I am actually working, not only when I am studying. I therefore keep a practical procedure here that I can return to later.

### What I do before changing anything

1. Identify the environment and target.
2. Check the current state.
3. Decide what result I expect.
4. Make the smallest controlled change.
5. Verify the result independently.

### Practical application

### Practical lab: trace an EC2 network path

I inspect the instance subnet:

```bash
aws ec2 describe-instances   --instance-ids <id>   --query 'Reservations[0].Instances[0].{Subnet:SubnetId,VPC:VpcId,PrivateIP:PrivateIpAddress}'
```

Then I inspect the subnet and route table association.

The path I draw is:

```text
EC2 ENI
 ↓
Subnet
 ↓
Route table
 ↓
target (IGW/NAT/local/etc.)
```

For internet access I separately verify security-group rules and the presence of the appropriate routing components.

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

For **Vpc Cidr Subnets**, the workflow I want to remember is:

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
subnet, path, text, cidr, only, private, public, subnets, does, internet, network, result
```

I am keeping these here because when I search the repository later, these are the words I am likely to remember.

### If I had to explain this to a teammate

I would start with the problem, not the tool:

> "The reason we use Vpc Cidr Subnets here is because it gives us a repeatable way to handle vpc cidr subnets. The practical part is that I can inspect the current state, make the intended change, verify it, and troubleshoot it without relying on manual guesswork."

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
