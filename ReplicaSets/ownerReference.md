- In the pod that is managed by replicasets
```
kubectl get po hello-dwx89 -o yaml | grep -A5 ownerReferences
```
```
---
ownerReferences:
  - apiVersion: apps/v1
    blockOwnerDeletion: true
    controller: true
    kind: ReplicaSet
    name: hello
```

### References
- https://learncloudnative.com/blog/2021-07-10-pods-replicasets