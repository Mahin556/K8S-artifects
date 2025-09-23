```
kubectl run nginx --image=nginx

kubectl run nginx --image=nginx --dry-run=client -oyaml > pod.yaml

kubectl run nginx --image=nginx --dry-run=client -ojson > pod.json

kubectl get pod

kubectl get pod -owide

kubectl describe pod <name>

kubectl delete pod <name>

kubectl exec -it <pod> -- /bin/sh

kubectl top pod

kubectl port-forward <pod> 8080:80

kubectl label pod <pod> team=dev

kubectl annotate pod <pod> key=value

kubectl get po -l=app.kubernetes.io/name=hello
```

### References
- https://spacelift.io/blog/kubernetes-cheat-sheet
