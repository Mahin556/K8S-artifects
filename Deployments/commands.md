```bash
kubectl get deployment
kubectl describe deployment <name>
kubectl create deployment <name> --image=nginx
kubectl delete deployment <name>
kubectl edit deployment <name>
kubectl rollout status deployment/<name>
kubectl set image deployment/<name> <container>=nginx:latest
kubectl rollout undo deployment/<name>
kubectl replace --force -f file.yaml

kubectl set image deployment/tomcat-deployment tomcat-container=tomcat:6.0
kubectl rollout status deployment/tomcat-deployment
kubectl rollout undo deployment/tomcat-deployment --to-revision=2
kubectl rollout history deployment/tomcat-deployment
```

```bash
#--record logs the command for auditing and rollout history.
kubectl apply -f Deployment.yaml --record
# or
kubectl create -f Deployment.yaml --record

kubectl rollout history deploy tomcat-deployment

deployment.apps/tomcat-deployment 
REVISION  CHANGE-CAUSE
1         kubectl apply --filename=demo.yml --record=true
```

### References
- https://spacelift.io/blog/kubernetes-cheat-sheet
- https://www.tutorialspoint.com/kubernetes/kubernetes_deployments.htm
