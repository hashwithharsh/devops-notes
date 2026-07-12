# CI/CD Troubleshooting Playbook

When a pipeline fails, I first identify the stage instead of reading hundreds of log lines without a plan.

```text
checkout
→ test
→ build
→ scan
→ publish
→ deploy
→ verify
```

## Checkout failure

I check the repository/ref, credentials, permissions and the runner/agent's network access.

## Test failure

I reproduce the same test command locally when possible. A CI log is much more useful when I know exactly which command failed.

## Docker build failure

I look for the first failing Dockerfile layer rather than the final error summary.

## Push failure

I verify:

```text
registry
credentials
repository
image tag
permissions
account/region
```

For ECR I also verify that authentication is being performed against the intended account and Region.

## Deployment succeeded but the app is broken

I don't call that a successful release.

For Kubernetes:

```bash
kubectl rollout status deployment/<name>
kubectl get pods
kubectl logs <pod>
```

Then I run a smoke test.

## Argo CD OutOfSync

I ask:

```text
What Git revision is being watched?
What path is rendered?
Which resource differs?
Is auto-sync enabled?
Which cluster/namespace is targeted?
```

## Image traceability

I want this relationship to be clear:

```text
Git commit
 ↓
image tag/digest
 ↓
deployment/GitOps change
 ↓
Argo CD
 ↓
running Pod
```

That lets me answer "what version is running?" without guessing.

A green CI job is not the same thing as a healthy production application. Verification is part of delivery.
