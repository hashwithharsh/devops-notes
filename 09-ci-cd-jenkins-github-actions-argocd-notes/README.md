# Ci Cd Jenkins Github Actions Argocd Notes

**Author:** Harsh Yadav

My CI/CD and GitOps notes covering Jenkins, GitHub Actions, Docker delivery and Argo CD.

I am keeping this as a long-term study/reference repo. The useful part for me is the combination of explanation, practical commands, small labs and the troubleshooting notes I tend to forget.

I don't expect every note to have exactly the same structure. I write more detail for topics that I actually need to revisit often.


## My placement reference

## What I want from a good pipeline

I want to be able to answer four questions:

```text
What code produced this artifact?
What checks passed before it was released?
What environment received it?
How do I verify or roll it back?
```

If a pipeline cannot answer those questions, adding more stages does not automatically make it a better pipeline.

## How I study this repo

I study CI/CD by building the same application delivery flow with different tools.

```text
GitHub
   ↓
CI
   ↓
tests
   ↓
Docker build
   ↓
security/quality checks
   ↓
container registry
   ↓
deployment
   ↓
Kubernetes
   ↓
verification
```

I first understand the Jenkins version, then reproduce the same concepts with GitHub Actions. After that I study Argo CD and GitOps as a different way of handling the deployment side.

For every pipeline I want to know:

```text
What triggered it?
What artifact was produced?
Where was it stored?
What credentials were needed?
What deployment changed?
How was success verified?
How would I roll it back?
```

For interview preparation, I focus on explaining the entire flow rather than memorizing Jenkinsfile or workflow YAML line by line.
