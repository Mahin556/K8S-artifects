## Zero-Downtime deployment
### Why ReplicaSet can’t do zero-downtime updates
- A ReplicaSet only ensures N Pods with matching labels are running.
    - If you change the image in the ReplicaSet spec:
    - It does not update existing Pods.
    - Only new Pods (after old ones die or are deleted) will use the new image.
    - To roll out the new version, you’d have to manually delete Pods one by one → very risky and not automated.

- This is why with just a ReplicaSet:
    - Updates = disruptive unless carefully managed.
    - No built-in rolling strategy (replace old Pods gradually while keeping service running).

### Why Deployments are needed
- A Deployment is a higher-level controller that manages ReplicaSets.
    - When you change a Deployment (e.g., update the image), Kubernetes:
    - Creates a new ReplicaSet with the updated Pod spec.
    - Gradually scales up the new ReplicaSet and scales down the old one.
    - Ensures at least some Pods are always available → zero downtime.
    - Supports rollbacks with kubectl rollout undo.

```
kubectl create deployment hello --image=busybox -- sleep 3600
```
```
kubectl set image deployment/hello busybox=busybox:1.31.1
```
```
kubectl rollout status deployment/hello
```
```
kubectl rollout undo deployment/hello
```
