- The kubectl CLI tool lets you interact with clusters: deploy applications, inspect resources, debug issues, and manage workloads.

```
alias k=kubectl             # Create a shortcut
kubectl get pods -o=json    # Output JSON
kubectl get pods -o=yaml    # Output YAML
kubectl get pods -o=wide    # Show extra info (like node)
kubectl get pods -n <ns>    # Use namespace
kubectl create -f file.yaml # Apply manifest
kubectl logs -l app=nginx   # Filter by label
kubectl -h                  # Help
```

### References
- https://spacelift.io/blog/kubernetes-cheat-sheet
