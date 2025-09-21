```
kubectl get deployment
kubectl describe deployment <name>
kubectl create deployment <name> --image=nginx
kubectl delete deployment <name>
kubectl edit deployment <name>
kubectl rollout status deployment/<name>
kubectl set image deployment/<name> <container>=nginx:latest
kubectl rollout undo deployment/<name>
kubectl replace --force -f file.yaml
```
### References
- https://spacelift.io/blog/kubernetes-cheat-sheet
