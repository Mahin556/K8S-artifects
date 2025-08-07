### Kubectl version
```
kubectl version
```

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

### Kubectl create and apply
```
kubectl create -f <manifest_file> ---> Create Resource
kubectl create deploy nginx-deploy  --image=nginx
kubectl create deploy nginx-deploy  --image=nginx --dry-run=client -o yaml > deploy.yaml
kubectl create ns <namespace_name>
kubectl apply -f <manifest_file> ---> Create + Update Resource
kubectl apply -f <manifest_file> -o yaml > pod.yaml
kubectl apply -f <manifest_file> -o json > pod.json
```

### Kubectl expose
```
kubectl expose deploy <deploy_name> --type=NodePort --port=80
```

### Kubectl run
```
kubectl run <pod_name> --image=<image_name>
kubectl run <pod_name> --image=<image_name> -n <namespace>
kubectl run <pod_name> --image=<image_name> --labels=app=nginx -o yaml > pod.yaml
Kubectl run pod_name --image=image_name --labels key=value,key=value...
kubectl run <pod_name> --image=<image_name> --labels=app=nginx -o yaml --dry-run=client > pod.yaml
```

### Kubectl get command
```
kubectl get pods/po
kubectl get nodes
kubectl get pods/po -n <namespace>
kubectl get pods/po <pod_name> -o yaml
kubectl get pods/po <pod_name> -o json
kubectl get deployment/deploy
kubectl get replicaset/rs
kubectl get daemonset/ds
kubectl get rc
kubectl get namespaces/ns
kubectl get pods -o wide
kubectl get deploy -o wide
kubectl get nodes -o wide
kubectl get pods <pods_name>... -o wide
kubectl get pods <pods_name> --show-labels
kubectl get pods <pods_name> -l key=value ---> selector
kubectl get nodes --kubeconfig ~/.kube/admin.conf
kubectl get all
kubectl get all -A/--all-namespaces
kubectl get all -n <namespace>
kubectl get svc
```

### Kubectl delete
```
kubectl delete -f manifest.yaml
kubectl delete pod <pod_name>....
kubectl delete deploy <deploy_name>
kubectl delete rc <rc_name>....
kubectl delete rs <rs_name>....
kubectl delete ns <ns_name>....
kubectl delete pods <pod1_name> <pod2_name> -n <namespace>
kubectl delete pods -l key=value
```

### Kubectl describe
```
kubectl describe pod <pod_name>
kubectl describe pod <pod_name> -n <namespace> | less
kubectl describe deploy <deploy_name>
kubectl describe rc <rc_name>
kubectl describe rs <rs_name>
kubectl describe ns <ns_name>
kubectl describe node <node_name>
kubectl describe pods -l key=value
```

### Kubectl edit
```
kuebctl edit pod <pod_name> ---> To change pod config at run time
```

### Kubectl exec 
```
kubectl exec -it <pod_name> -- <command>
kubectl exec -it <pod_name> -n <namespace> -- <command>
kubectl exec -it <pod_name> -n <namespace> -c <container_name> -- <command>
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

### Kubectl label
```
kubectl label nodes <node-name> app=frontend
kubectl label node my-worker-node node-role.kubernetes.io/worker="<label>" ---> To assign label to node
kubectl label nodes <node-name> <label-key>=<label-value> --overwrite
kubectl label nodes <node-name> <label-key>-
Kubectl label pods pod_name key=value,key=value...

```

### Kubectl scale
```
kubectl scale --replicas=4 rs/nginx
kubectl scale --replicas=4 deploy/nginx
kubectl scale --current-replicas=2 --replicas=4 deploy/nginx
```

### Kubectl set
```
kubectl set image deploy nginx nginx=nginx:1.9.1
kubectl set image pod nginx nginx=nginx:1.9.1
```

### Kubectl rollout
```
kubectl rollout history deploy nginx
kubectl rollout undo deploy nginx ---> allow go rollback to previous version of application or undo changes
```

### Kubectl logs
```
kubectl logs <pod_name>
kubectl logs deployment/nginx --all-pods=true
kubectl logs job/hello
```




