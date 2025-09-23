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

kubectl create -f pod.yaml

kubectl run mypod --image=nginx --restart=Never

kubectl get pods -A/--all-namespaces

kubectl delete -f pod.yaml

kubectl delete pod <pod-name>

kubectl delete pods --all -A

kubectl exec -it <pod-name> -- bash
kubectl exec -it <pod-name> -- sh

kubectl cp ./localfile <pod-name>:/app/remote-file
kubectl cp <pod-name>:/app/remote-file ./localfile

kubectl port-forward <pod-name> 8080:80

kubectl logs <pod-name>

kubectl logs <pod-name> -c <container-name>

kubectl logs -f <pod-name>

kubectl debug -it <pod-name> --image=busybox

kubectl get pods -w

kubectl get events --sort-by=.metadata.creationTimestamp
```

### References
- https://spacelift.io/blog/kubernetes-cheat-sheet
