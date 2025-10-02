```bash
kubectl create deployment nginx --image=nginx:1.23 --replicas=2
```

```bash
kubectl expose deployment nginx --type=NodePort --port=8080 --target-port=80
```
- The Kubernetes API server handles the automatic assignment. When you create the NodePort service, the API server checks the available ports and allocates one that is not already in use by another service.

```bash
kubectl get svc nginx -o wide
```