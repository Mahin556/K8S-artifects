```
kubectl get node
kubectl describe node <name>
kubectl top node <name>
kubectl taint node <name> key=value:NoSchedule
kubectl cordon <name>       # Unschedulable
kubectl uncordon <name>     # Schedulable
kubectl drain <name>        # Evict pods (maintenance)
kubectl label node <name> env=prod
kubectl annotate node <name> key=value
kubectl delete node <name>
```
