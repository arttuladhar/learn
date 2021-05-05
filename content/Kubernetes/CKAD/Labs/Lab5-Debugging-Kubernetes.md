---
title: Lab05 - Debugging Kubernetes
---

### Identify the Problem

```
kubectl get pods --all-namespaces
```

### Get the Broken Pods's Container Logs

```
kubectl logs cart-ws -n candy-store
```

### Fix the Problem

```yml

# Save the Pod Descriptor of Broken Pod
kubectl get pod <pod name> -n <namespace> -o yaml --export > broken-pod.yml

# Delete Broken Pod
kubectl delete pod <pod name> -n <namespace>

# Recreate Broken Pod
kubectl apply -f broken-pod.yml -n <namespace>

# Verify Fix
kubectl get pod <pod name> -n <namespace>
```

