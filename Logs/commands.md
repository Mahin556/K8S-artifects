```
kubectl logs <pod>
kubectl logs -f <pod>                     # Follow logs
kubectl logs -c <container> <pod>         # Specific container
kubectl logs --tail=50 <pod>              # Last 50 lines
kubectl logs --since=6h <pod>             # Last 6 hours
kubectl logs --previous <pod>             # Previous crash logs
kubectl logs <pod> > pod.log              # Save to file
```
