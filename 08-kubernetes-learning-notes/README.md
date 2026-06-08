# Kubernetes Learning Notes

**Author:** Harsh Yadav

My Kubernetes notes, especially the practical side of workloads, networking, storage, RBAC and troubleshooting.

I am keeping this as a long-term study/reference repo. The useful part for me is the combination of explanation, practical commands, small labs and the troubleshooting notes I tend to forget.

I don't expect every note to have exactly the same structure. I write more detail for topics that I actually need to revisit often.


## My placement reference

## My Kubernetes troubleshooting habit

I don't treat statuses such as `Pending`, `CrashLoopBackOff` or `ImagePullBackOff` as the actual diagnosis.

They are clues.

I normally combine:

```bash
kubectl get
kubectl describe
kubectl logs
kubectl get events
```

and then follow the dependency chain until I find the first broken part.

## How I study this repo

I learn Kubernetes progressively using a local/disposable cluster.

```text
cluster basics
   ↓
Pods
   ↓
Deployments
   ↓
Services
   ↓
ConfigMaps / Secrets
   ↓
probes / resources
   ↓
Ingress
   ↓
storage
   ↓
RBAC
   ↓
scheduling
   ↓
troubleshooting
```

For each practical I first check the current cluster:

```bash
kubectl get nodes
kubectl get pods -A
```

When something fails, I use:

```bash
kubectl get
kubectl describe
kubectl logs
kubectl get events
```

I do not treat `Pending`, `CrashLoopBackOff` or `ImagePullBackOff` as the root cause. I use them as clues and follow the dependency chain.

For interview revision, I concentrate on how controllers, Pods, Services, networking, storage, RBAC and scheduling fit together.
