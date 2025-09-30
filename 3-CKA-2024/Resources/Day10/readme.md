# Day 10/40 - Kubernetes Namespace Explained - CKA Full Course 2024


## Check out the video below for Day10 👇

[![Day10/40 - Kubernetes Namespace Explained - CKA Full Course 2024](https://img.youtube.com/vi/yVLXIydlU_0/sddefault.jpg)](https://youtu.be/yVLXIydlU_0)

### What is a Namespace in Kubernetes

- Provides isolation of resources(for organization, security)
- Avoid accidental deletion/modification
- Separated by resource type or environment or domain and so on
- Resources can access each other in the same namespace with their first name by the DNS name for other namespaces
- Resource in the different namespace can access each other by there FQDN
    ```bash
    #Format:- <pod-name>.<namespace>.pod.cluster.local
    kubectl get pod nginx-7bb7cd8db5-abcde -n dev -o wide #---> nginx-7bb7cd8db5-abcde.dev.pod.cluster.local
    
    #<service-name>.<namespace>.svc.cluster.local
    kubectl get svc my-service -n dev #---> my-service.dev.svc.cluster.local

    #Headless Service (Pod DNS records)
    #If service is headless (clusterIP: None), then each Pod behind it gets its own DNS entry: <pod-name>.<service-name>.<namespace>.svc.cluster.local
    kubectl get svc my-headless-svc -n dev -o yaml #---> db-0.my-headless-svc.dev.svc.cluster.local

    kubectl exec -it nginx -- cat /etc/resolve 
    ```
- In a same namespace two same type resource can not have same name but in seperate namespace 2 same type resource can have same name.
- By default all the resource created in `default` namespace
- Ip address can be accesiable on entire cluster but the name is only accesiable within a namespace unless it is FQDN
- there are some namespaced object and some are clusterwide:
    ```bash
    kubectl api-resources | grep -i true
    kubectl api-resources --namespaced=true
    kubectl api-resources --namespaced=true -o name
    kubectl api-resources --namespaced=true | awk 'NR>1 {print $1}' #Get only the "NAME" column (resource names)
    kubectl api-resources --namespaced=true | awk 'NR>1 {print $2}' #Get only the "KIND" column
    kubectl api-resources --namespaced=true | awk 'NR>1 {print $1, "-", $2}' #Get both NAME and KIND neatly
    ```

* Default namespaces
```bash
controlplane:~$ kubectl get ns
NAME                 STATUS   AGE
default              Active   10d
kube-node-lease      Active   10d
kube-public          Active   10d
kube-system          Active   10d
local-path-storage   Active   10d
```

```bash
kubectl get all --namespace=kube-system
```
```bash
NAME                                          READY   STATUS    RESTARTS      AGE
pod/calico-kube-controllers-fdf5f5495-vxpw5   1/1     Running   2 (62m ago)   10d
pod/canal-4xxhw                               2/2     Running   2 (62m ago)   10d
pod/canal-wlt99                               2/2     Running   2 (62m ago)   10d
pod/coredns-6ff97d97f9-4wljj                  1/1     Running   1 (62m ago)   10d
pod/coredns-6ff97d97f9-wmpcb                  1/1     Running   1 (62m ago)   10d
pod/etcd-controlplane                         1/1     Running   3 (62m ago)   10d
pod/kube-apiserver-controlplane               1/1     Running   3 (62m ago)   10d
pod/kube-controller-manager-controlplane      1/1     Running   2 (62m ago)   10d
pod/kube-proxy-d8lhg                          1/1     Running   1 (62m ago)   10d
pod/kube-proxy-txl4k                          1/1     Running   2 (62m ago)   10d
pod/kube-scheduler-controlplane               1/1     Running   2 (62m ago)   10d

NAME               TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)                  AGE
service/kube-dns   ClusterIP   10.96.0.10   <none>        53/UDP,53/TCP,9153/TCP   10d

NAME                        DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
daemonset.apps/canal        2         2         2       2            2           kubernetes.io/os=linux   10d
daemonset.apps/kube-proxy   2         2         2       2            2           kubernetes.io/os=linux   10d

NAME                                      READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/calico-kube-controllers   1/1     1            1           10d
deployment.apps/coredns                   2/2     2            2           10d

NAME                                                DESIRED   CURRENT   READY   AGE
replicaset.apps/calico-kube-controllers-fdf5f5495   1         1         1       10d
replicaset.apps/coredns-674b8bbfcf                  0         0         0       10d
replicaset.apps/coredns-6ff97d97f9                  2         2         2       10d
```

```bash
kubectl create namespace demo
kubectl create ns demo
kubectl run nginx --image=nginx -n demo
kubectl create deploy demo -n demo
kubectl get pods -n demo
kubectl get deploy -n demo
```

```yaml
apiVersion: v1
kind: Namespace
metadata:
  labels:
    kubernetes.io/metadata.name: demo
  name: demo
```

![image](https://github.com/piyushsachdeva/CKA-2024/assets/40286378/d9ae95d5-7224-4d5b-b260-ed09fc53c6fd)



