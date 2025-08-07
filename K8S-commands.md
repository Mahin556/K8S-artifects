### To see all the api resources and there shortname, apiVersion, Kind
```
kubectl api-resources
kubectl api-resources | grep <resource_name>
kubectl explain <resource_name> ---> kubectl explain pod
kubectl explain <resource_name>.<fields> ---> kubectl explain pod.apiVersion
kubectl explain deploy
kubectl explain daemonsets
kubectl explain replicaset
kubectl explain service
```

### Implement manifest
```
kubectl create -f <manifest_file> ---> Create Resource
kubectl apply -f <manifest_file> ---> Create + Update Resource
kubectl apply -f <manifest_file> -o yaml > pod.yaml
kubectl apply -f <manifest_file> -o json > pod.json
```

### Kubectl get command
```
kubectl get pods/po
kubectl get pods/po <pod_name> -o yaml
kubectl get pods/po <pod_name> -o json
kubectl get deployment/deploy
kubectl get replicaset/rs
kubectl get daemonset/ds
kubectl get rc
kubectl get namespaces/ns
kubectl get nodes
kubectl get pods -o wide
kubectl get deploy -o wide
kubectl get nodes -o wide
kubectl get pods <pods_name>... -o wide
kubectl get pods <pods_name> --show-labels
kubectl get nodes --kubeconfig ~/.kube/admin.conf
```

### Kubectl describe
```
kubectl describe pod <pod_name>
kubectl describe deploy <deploy_name>
kubectl describe rc <rc_name>
kubectl describe rs <rs_name>
kubectl describe ns <ns_name>
kubectl describe node <node_name>
```

### Kubectl edit
```
kuebctl edit pod <pod_name> ---> To change pod config at run time
```

### Kubectl exec 
```
kubectl exec -it <pod_name> -- <command>
kubectl exec -it mypod -- bash
kubectl exec -it mypod -- sh
kubectl exec -it mypod -- /bin/bash -c ls
```

### Kubectl run
```
kubectl run <pod_name> --image=<image_name> --dry-run=client/server -o yaml
kubectl run <pod_name> --image=<image_name> --dry-run=client/server -o json
kubectl run <pod_name> --image=<image_name> --dry-run=client/server/none -o json > pod.json
```

