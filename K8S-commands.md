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
kubectl create deploy nginx-deploy  --image=nginx --port=<container_port>
kubectl create deploy nginx-deploy  --image=nginx -n <namespace>
kubectl create deploy nginx-deploy  --image=nginx --dry-run=client -o yaml > deploy.yaml
kubectl create deployment nginx --image nginx:latest --replicas 3
kubectl create ns <namespace_name>
kubectl apply -f <manifest_file> ---> Create + Update Resource
kubectl apply -f <manifest_file> -n <namespace>
kubectl apply -f <manifest_file> -o yaml > pod.yaml
kubectl apply -f <manifest_file> -o json > pod.json
```

### Kubectl annotate
```
kubectl annotate deployment nginx kubernetes.io/change-cause="Pick up patch version"
```

### Kubectl rolling-update
```
kubectl rolling-update old-rc new-rc --image=nginx:1.23.4
# It does not work with ReplicaSets or Deployments.
# The reason is that Deployments replaced the need for kubectl rolling-update, and they manage ReplicaSets internally with a built-in rolling update strategy.
```

### Kubectl expose
```
kubectl expose deploy <deploy_name> --type=NodePort --port=80
kubectl expose deploy <deploy_name> --name=<svc_name> --type=NodePort --port=80
kubectl expose deploy <deploy_name> --name=<svc_name> --type=NodePort --port=80 --target-port=80 ---> not select nodeport port, 
kubectl expose deploy <deploy_name> --name=<svc_name> --port=<port> -n <namespace>
```

### Kubectl create service
```
kubectl create service nodeport --tcp=8080:80 --node-port ---> dedicated to node port, can control nodeport port value, not have option to select the pods using selector
```
### Kubectl set
```
kubectl set selector service <service_name> <key>=<name>
```


### Kubectl run
```
kubectl run <pod_name> --image=<image_name>
kubectl run <pod_name> --image=<image_name> -n <namespace>
kubectl run <pod_name> --image=<image_name> --labels=app=nginx -o yaml > pod.yaml
Kubectl run pod_name --image=image_name --labels key=value,key=value...
kubectl run <pod_name> --image=<image_name> --labels=app=nginx -o yaml --dry-run=client > pod.yaml

```

### Kubectl taint
```
kubectl taint nodes <node_name> GPU=true:NoSchedule
kubectl taint nodes <node_name> GPU=true:NoSchedule- ---> To remove the taint
# We can't add toleration from the CLI
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
kubectl get daemonset/ds -n kube-system
kubectl get daemonset/ds -A
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
kubectl get all -A/--all-namespaces -o wide
kubectl get all -n <namespace> / --namespace <namespace>
kubectl get all -n default
kubectl get svc
kubectl get pods --watch/-w
kubectl get ep/endpoint 
kubectl get pods -v=1-9 ---> verbose info of api call
kubectl get svc -v=1-9
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
kubectl describe svc <svc_name>
kubectl describe node <node_name>
kubectl describe node master | grep -i taint
kubectl describe pods -l key=value
```

### Kubectl edit
```
kuebctl edit pod <pod_name> ---> To change pod config at run time
kuebctl edit svc <svc_name>
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
kubectl label pods --all env=prod

```

### Kubectl scale
```
kubectl scale --replicas=4 rs/nginx
kubectl scale --replicas=4 deploy/nginx
kubectl scale --replicas=4 deploy/nginx -n <namespace>
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
kubectl rollout status deployment/nginx
kubectl rollout undo deployment/nginx --to-revision=1
```

### Kubectl logs
```
kubectl logs <pod_name>
kubectl logs pod <pod_name> -c init-container
kubectl logs deployment/nginx --all-pods=true
kubectl logs job/hello
```

### kubectl top
```
kubectl top pods
kubectl top pod <pod_name>
```

### Kubectl config
```
kubectl config view -o jsonpath='{.users[].name}'         
# Display the first user

kubectl config view -o jsonpath='{.users[*].name}'        
# Get a list of all users

kubectl config get-contexts                               
# Display list of contexts

kubectl config get-contexts -o name                       
# Get all context names

kubectl config current-context                            
# Display the current context

kubectl config use-context my-cluster-name                
# Set the default context to 'my-cluster-name'

kubectl config set-cluster my-cluster-name                
# Set a cluster entry in the kubeconfig

```

### Port forwarding
```
kubectl port-forward service/nginx 80:80 --address 192.168.29.101
kubectl port-forward service/nginx 80:80
```
