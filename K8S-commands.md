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
kubectl explain <resource_type> --recursive ## Show all the option available to you to create a yaml
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
kubectl apply -f <manifest_file> -n <namespace> --dry-run
kubectl apply -f <manifest_file> -n <namespace> --server-dry-run
kubectl apply -f <manifest_file> -o yaml > pod.yaml
kubectl apply -f <manifest_file> -o json > pod.json
kubectl apply -f <manifest_file> --record
kubectl apply -f .
```
### Connectivity
```
netcat -l -p <port> #To open port in container
telnet <ip> <port>  #Check if specific server is listining on certain port or not
```


### Kubectl diff
```
kubectl diff -f <yaml>
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
kubectl expose pod <pod_name> --name=<svc_name> --port=<port> --target-port=<port>
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
kubectl get roles
kubectl get rolesbinding
kubectl get serviceaccount
kubectl get namespaces/ns

kubectl get pods --all-namespaces
# or
kubectl get pods -A

kubectl get pods all --all-namespaces

kubectl get pods --selector tier=frontend  # --selector == -l
kubectl get pods -l tier=frontend --show-labels


kubectl get deploy -o wide
kubectl get nodes -o wide
kubectl get pods <pods_name>... -o wide
kubectl get pod <pods_name> --show-labels
kubectl get deployment/deploy --show-labels
kubectl get service/svc --show-labels
kubectl get rc --show-labels
kubectl get rs --show-labels
kubectl get pods --show-labels
kubectl get pods <pods_name> -l key=value ---> selector
kubectl get nodes --kubeconfig ~/.kube/admin.conf
kubectl get nodes -o yaml > node.yaml
kubectl get nodes -o yaml | grep -i cidr
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
kubectl apply -f . #to delete all the resources defined in manifest in current directory
kubectl delete pod <pod_name>....
kubectl delete deploy <deploy_name>
kubectl delete rc <rc_name>....
kubectl delete rs <rs_name>....
kubectl delete ns <ns_name>....
kubectl delete pods <pod1_name> <pod2_name> -n <namespace>
kubectl delete pods -l key=value
kubectl delete pods,services -l key=value
kubectl delete pods --all
kubectl delete rc <rc_name> --cascade=false #only delete rc and pods
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
Kubectl label deploy deploy_name key=value
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
kubectl rollout undo deployment/nginx --to-revision=1 #rollback to specific version
kubectl rollout pause deloyment <deploy_name>
kubectl rollout resume deloyment <deploy_name>
```

### Kubectl logs
```
kubectl logs <pod_name>
kubectl logs pod <pod_name> -c init-container
kubectl logs deployment/nginx --all-pods=true
kubectl logs job/hello
kubectl logs -l key=value
kubectl logs -l key=value -c my-container
kubectl logs -l -f key=value --all-container
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

kubectl config view -o jsonpath='{.users[?(@.name == "e2e")].user.password}'
kubectl config view --raw -o jsonpath='{.users[?(@.name == "e2e")].user.client-certificate-data}' | base64 -d
kubectl config set-context --current --namespace=ggckad-s2 #Permanently save a namespace for the current context

## Create and use a new context with a specific user and namespace
kubectl config set-context gce --user=cluster-admin --namespace=foo \
  && kubectl config use-context gce   



kubectl config view
kubectl config view --raw

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

kubectl config set-context --current --namespace=test

```

### Port forwarding
```
kubectl port-forward service/nginx 80:80 --address 192.168.29.101
kubectl port-forward service/nginx 80:80
```

### Kubectl auth
```
kubectl auth whoami
kubectl auth can-i get pods
kubectl auth can-i get <resource_type> -n <namespace>
kubectl auth can-i get <resource_type> -n <namespace> --as=<user_or_ServiceAccount>
```
### Kubectl drain
```
kubectl drain tomcat --ignore-daemonsets --delete-emptydir-data --force
  --ignore-daemonsets → lets the drain continue even if DaemonSet-managed pods exist.
  --delete-emptydir-data → deletes pods using emptyDir volumes (otherwise it refuses).
  --force → evicts pods not backed by a controller (like bare static pods).
  Caution: Using --force can cause data loss or downtime (especially with --delete-emptydir-data). Make sure it’s safe before running in production.
```
### Kube cordon/uncordon
```
kubectl cordon tomcat #If you just want to mark node unschedulable (without evicting pods):
kubectl uncordon tomcat
```

# Kubernetes Kubectl Cheat Sheet

## 🔹 Pod-related Commands

### Get pods by status
```bash
kubectl get pods --field-selector=status.phase=Running
```
➡️ Lists all **running pods** in the current namespace.

### Get container IDs of init containers
```bash
kubectl get pods --all-namespaces -o jsonpath='{range .items[*].status.initContainerStatuses[*]}{.containerID}{"\n"}{end}' | cut -d/ -f3
```
➡️ Extracts all **container IDs** of init containers (useful for cleanup).

### Delete all pods/services in a namespace
```bash
kubectl -n my-ns delete pod,svc --all
```

### Run a single pod
```bash
kubectl run nginx --image=nginx -n mynamespace
```
➡️ Creates an **nginx pod** in namespace `mynamespace`.

### Pod metrics
```bash
kubectl top pod
```
➡️ Shows CPU/memory usage for pods.

### Copy files between pod and local machine
```bash
kubectl cp /tmp/foo_dir my-pod:/tmp/bar_dir
kubectl cp my-namespace/my-pod:/tmp/foo /tmp/bar
```
➡️ Copy files **to and from** pods.

### Exec into pods
```bash
kubectl exec -it myapp-deploy-54987bc965-bkrzj -- printenv
kubectl exec -it myapp-deploy-54987bc965-bkrzj -- env
```
➡️ Run commands inside a container (`printenv`, `env`, bash, etc.).

---

## 🔹 Node & Scheduling

### Get taints on nodes
```bash
kubectl describe node <node-name>
kubectl get nodes -o custom-columns=NAME:.metadata.name,TAINTS:.spec.taints
kubectl get nodes <node-name> -o jsonpath='{.spec.taints}'
```

### Add taint to a node
```bash
kubectl taint nodes <node-name> GPU=true:NoSchedule
```

### Remove taint
```bash
kubectl taint nodes <node-name> key=value:NoSchedule-
```

### Get labels
```bash
kubectl describe node worker02 | grep -i label
```

### Remove a label
```bash
kubectl label node node-01 purpose-
```

---

## 🔹 Autoscaling (HPA)

### Create HPA
```bash
kubectl autoscale deployment my-deploy --cpu-percent=80 --min=1 --max=10
kubectl autoscale deployment php-apache --cpu-percent=50 --min=1 --max=10
```

### List HPAs
```bash
kubectl get hpa
kubectl get hpa -n <namespace>
```

### Edit HPA
```bash
kubectl edit hpa <hpa-name>
```

### Patch HPA (e.g., change replicas)
```bash
kubectl patch hpa <hpa-name> -p '{"spec":{"maxReplicas":15}}'
```

### Export HPA YAML
```bash
kubectl get hpa <hpa-name> -o yaml > hpa.yaml
```

### Check metrics-server
```bash
kubectl get deployment metrics-server -n kube-system
kubectl logs deployment/metrics-server -n kube-system
```

---

✅ This cheat sheet covers **pods, nodes, taints, labels, and HPA (Horizontal Pod Autoscaler)** — useful for daily Kubernetes admin and troubleshooting tasks.

