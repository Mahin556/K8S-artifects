### Update resource limits:

```bash
kubectl set resources deployment/nginx-deployment -c=nginx --limits=cpu=200m,memory=512Mi
```

**Output:**

```
deployment.apps/nginx-deployment resource requirements updated
```

### References
- https://kubernetes.io/docs/concepts/workloads/controllers/deployment/