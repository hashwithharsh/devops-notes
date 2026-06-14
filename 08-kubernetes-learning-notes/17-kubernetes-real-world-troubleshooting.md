# Kubernetes Troubleshooting Scenarios

These are the failure cases I want to be able to work through without opening a tutorial.

## Pod is Pending

```bash
kubectl describe pod <pod>
```

I look at Events.

Possible causes include insufficient resources, node selector mismatch, affinity, taints/tolerations and storage problems.

Then:

```bash
kubectl get nodes --show-labels
kubectl describe nodes
```

## CrashLoopBackOff

First:

```bash
kubectl logs <pod>
kubectl logs <pod> --previous
kubectl describe pod <pod>
```

I look for application startup errors, missing configuration, bad arguments, permissions, unavailable dependencies and probes that are killing a container.

## ImagePullBackOff

```bash
kubectl describe pod <pod>
```

I verify the image name/tag, registry access and imagePullSecrets if the registry is private.

## Service has no endpoints

```bash
kubectl get svc <service> -o yaml
kubectl get endpointslices
kubectl get pods --show-labels
```

The first thing I compare is the Service selector with the Pod labels.

## Ingress returns 502

I move inward:

```text
Ingress
 ↓
Service
 ↓
EndpointSlice
 ↓
Pod
 ↓
application listener
```

If the Service has no endpoints, the Ingress is not the first place I need to fix.

## RBAC denied

I can test authorization directly:

```bash
kubectl auth can-i get pods   --as=system:serviceaccount:<namespace>:<serviceaccount>
```

## My Kubernetes rule

`kubectl get` tells me what exists.

`kubectl describe` often tells me what Kubernetes thinks is happening.

`kubectl logs` tells me what the application/container is saying.

Events tell me what the cluster recently tried and why it failed.

I normally need all of these together.
