# Kubernetes Services

Pods are replaceable and their IPs can change.

A Service provides a stable endpoint.

## Common types

### ClusterIP

Internal cluster access.

### NodePort

Exposes a service through a port on cluster nodes.

### LoadBalancer

Requests an external load-balancing integration when supported by the environment.

## Selector

A Service selects Pods using labels.

Example:

```yaml
selector:
  app: web
```

If the selector does not match Pod labels, the Service can exist while sending traffic to no endpoints.

## Troubleshooting

```bash
kubectl get svc
kubectl describe svc web
kubectl get endpoints
kubectl get endpointslices
```

## Why this topic matters in the larger system

Kubernetes is a reconciliation system. Strong understanding comes from knowing desired state, controllers, scheduling, networking, storage, security and health checks, then using events and object descriptions to explain failures.


## Practical exercise

Deploy a small application with a Deployment and Service. Add configuration, a readiness probe and resource requests. Then deliberately introduce a bad image, wrong selector, failing probe and insufficient scheduling requirement. Diagnose each failure using events, describe output and logs.


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

### Practical lab: expose a Deployment

Create:

```bash
kubectl expose deployment web --port=80 --target-port=80
kubectl get svc
```

Inspect:

```bash
kubectl describe svc web
kubectl get endpointslices
```

If the Service has no endpoints, I immediately check labels:

```bash
kubectl get pods --show-labels
kubectl get svc web -o yaml
```

The selector must match the Pod labels.

This is one of the most useful Kubernetes troubleshooting checks I can remember.

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

For **Services**, the workflow I want to remember is:

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

## Things I would check on a real system

### Why I am keeping this note

The mental model I am keeping is reconciliation. I declare a desired state and Kubernetes controllers continuously try to make the actual cluster state match it.

### Where I would use it

I use a disposable namespace/local cluster and inspect events and object state.

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
kubectl get nodes
kubectl get pods -A
kubectl describe pod <pod>
kubectl logs <pod>
kubectl get svc
kubectl get endpointslices
kubectl get events --sort-by=.lastTimestamp

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

I want to stop treating `CrashLoopBackOff`, `Pending`, or `ImagePullBackOff` as diagnoses. They are useful symptoms. The actual cause normally appears in events, describe output, logs or a dependency.

Another thing I want to avoid is copying a command from a random blog without understanding what resource it is changing. A command can be syntactically correct and still be completely wrong for the environment.

### Keywords from this note

These are the terms I should recognize when I come back to the note:

```text
kubectl, selector, service, bash, change, kubernetes, result, state, troubleshooting, verify, check, cluster
```

I am keeping these here because when I search the repository later, these are the words I am likely to remember.

### If I had to explain this to a teammate

I would start with the problem, not the tool:

> "The reason we use Services here is because it gives us a repeatable way to handle services. The practical part is that I can inspect the current state, make the intended change, verify it, and troubleshoot it without relying on manual guesswork."

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
