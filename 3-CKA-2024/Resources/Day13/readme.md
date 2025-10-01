# Day 13/40 - static pods, manual scheduling, labels, and selectors in Kubernetes 📘🚀

## Check out the video below for Day13 👇

[![Day12/40 - Kubernetes Daemonset Explained - Daemonsets, Job and Cronjob in Kubernetes](https://img.youtube.com/vi/6eGf7_VSbrQ/sddefault.jpg)](https://youtu.be/6eGf7_VSbrQ)


Welcome to the quick reference guide for our Kubernetes Labels, Selectors, and Static Pods video! This guide will be handy as you dive deeper into Kubernetes, especially if you're starting. Let's explore these concepts in detail:

---

## 📌 Labels and Selectors in Kubernetes

### Labels 🏷️
Labels are key-value pairs attached to Kubernetes objects like pods, services, and deployments. They help organize and group resources based on criteria that make sense to you.

**Examples of Labels:**
- `environment: production`
- `type: backend`
- `tier: frontend`
- `application: my-app`

### Selectors 🔍
Selectors filter Kubernetes objects based on their labels. This is incredibly useful for querying and managing a subset of objects that meet specific criteria.

**Common Usage:**
- **Pods**: `kubectl get pods --selector app=my-app`
- **Deployments**: Used to filter the pods managed by the deployment.
- **Services**: Filter the pods to which the service routes traffic.

### Labels vs. Namespaces 🌍
- **Labels**: Organize resources within the same or across namespaces.
- **Namespaces**: Provide a way to isolate resources from each other within a cluster.

### Annotations 📝
Annotations are similar to labels but attach non-identifying metadata to objects. For example, recording the release version of an application for information purposes or last applied configuration details etc.

```bash
kubectl get pods --show-labels

kubectl get nodes --show-labels

kubectl get pod mypod --show-labels

kubectl get pods -l app=nginx

kubectl get svc -l env=prod

kubectl get pods -l app=nginx,env=prod

kubectl get pods -l 'env in (prod,staging)'

kubectl get pods -l 'env notin (dev)'

kubectl label pod mypod app=nginx

kubectl label node mynode region=us-east

kubectl label pod mypod app-

kubectl label pod mypod app=apache --overwrite

kubectl describe pod mypod | grep Labels

kubectl delete pod -l app=nginx

kubectl delete svc -l env=dev

kubectl patch pod mypod -p '{"metadata":{"labels":{"team":"devops"}}}'
```


```bash
# Get pods with label app=nginx
kubectl get pods -l app=nginx

# Get pods with label env not equal to prod
kubectl get pods -l 'env!=prod'

# Get pods with env=prod or env=staging
kubectl get pods -l 'env in (prod,staging)'

# Get pods with env not equal to dev
kubectl get pods -l 'env notin (dev)'

# Get pods that have the label `app` (any value)
kubectl get pods -l 'app'

# Get pods that do not have the label `tier`
kubectl get pods -l '!tier'

kubectl describe svc nginx-svc | grep Selector
```


```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
```
- The selector.matchLabels ensures this RS manages only Pods with app=nginx.


```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-svc
spec:
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
```
- This Service will route traffic to all Pods with app=nginx.


```yaml
selector:
  matchExpressions:
  - { key: env, operator: In, values: [prod, staging] }
  - { key: tier, operator: NotIn, values: [debug] }
```
- Matches Pods where env is prod or staging, but not where tier=debug.

```bash
# Add annotation
kubectl annotate pod nginx-pod owner=devops-team

# Update annotation (overwrite)
kubectl annotate pod nginx-pod owner=platform-team --overwrite

# Remove annotation
kubectl annotate pod nginx-pod owner-

kubectl describe pod nginx-pod | grep Annotations -A 5
kubectl get pod nginx-pod -o jsonpath='{.metadata.annotations}'
```

* Pod with annotations
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
  annotations:
    description: "This pod runs nginx web server"
    contact: "devops-team@example.com"
spec:
  containers:
  - name: nginx
    image: nginx
```

* Real-world example with Ingress(Tells the NGINX ingress controller how to handle paths)
```yaml
metadata:
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
```


## 🛠️ Static Pods

Static Pods are special types of pods managed directly by the `kubelet` on each node rather than through the Kubernetes API server.

### Key Characteristics of Static Pods:
- **Not Managed by the Scheduler**: Unlike deployments or replicasets, the Kubernetes scheduler does not manage static pods.
- **Defined on the Node**: Configuration files for static pods are placed directly on the node's file system, and the `kubelet` watches these files.
- **Some examples of static pods are:** ApiServer, Kube-scheduler, controller-manager, ETCD etc
  
### Managing Static Pods:
1. **SSH into the Node**: You will gain access to the node where the static pod is defined.(Mostly the control plane node)
2. **Modify the YAML File**: Edit or create the YAML configuration file for the static pod.
3. **Remove the Scheduler YAML**: To stop the pod, you must remove or modify the corresponding file directly on the node.
4. **Default location**": is usually `/etc/kubernetes/manifests/`; you can place the pod YAML in the directory, and Kubelet will pick it for scheduling.

- all the control plane component is running as a static pod
static pod naming:- `<pod-name>-<node-name>`

## 🧭 Manual Pod Scheduling

Manual scheduling in Kubernetes involves assigning a pod to a specific node rather than letting the scheduler decide.

### Key Points:
- **`nodeName` Field**: Use this field in the pod specification to specify the node where the pod should run.
- **No Scheduler Involvement**: When `nodeName` is specified, the scheduler bypasses the pod, and it’s directly assigned to the given node.

```bash
kubectl get nodes -owide
```

### Example Configuration:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: manual-scheduled-pod
spec:
  nodeName: worker-node-1
  containers:
  - name: nginx
    image: nginx
```
- Pod is scheduled even if schedular is not running

>Note: Kubernetes will place the pod on worker-node-1 with the above configuration.

```bash
kubectl get pods -n kube-system | grep -i scheduler

kube-scheduler-controlplane               1/1     Running   2 (47m ago)   11d
```

```bash
ps -ef | grep -i kubelet

root        7782    5693  1 05:32 ?        00:00:14 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf --config=/var/lib/kubelet/config.yaml --node-ip=172.18.0.2 --node-labels= --pod-infra-container-image=registry.k8s.io/pause:3.10.1 --provider-id=kind://docker/mycluster1/mycluster1-worker --runtime-cgroups=/system.slice/containerd.service
root        7902    5694  2 05:32 ?        00:00:23 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf --config=/var/lib/kubelet/config.yaml --node-ip=172.18.0.4 --node-labels= --pod-infra-container-image=registry.k8s.io/pause:3.10.1 --provider-id=kind://docker/mycluster1/mycluster1-worker2 --runtime-cgroups=/system.slice/containerd.service
root        7903    5692  3 05:32 ?        00:00:30 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf --config=/var/lib/kubelet/config.yaml --node-ip=172.18.0.3 --node-labels= --pod-infra-container-image=registry.k8s.io/pause:3.10.1 --provider-id=kind://docker/mycluster1/mycluster1-control-plane --runtime-cgroups=/system.slice/containerd.service
root        8243    8020  5 05:32 ?        00:00:41 kube-apiserver --advertise-address=172.18.0.3 --allow-privileged=true --authorization-mode=Node,RBAC --client-ca-file=/etc/kubernetes/pki/ca.crt --enable-admission-plugins=NodeRestriction --enable-bootstrap-token-auth=true --etcd-cafile=/etc/kubernetes/pki/etcd/ca.crt --etcd-certfile=/etc/kubernetes/pki/apiserver-etcd-client.crt --etcd-keyfile=/etc/kubernetes/pki/apiserver-etcd-client.key --etcd-servers=https://127.0.0.1:2379 --kubelet-client-certificate=/etc/kubernetes/pki/apiserver-kubelet-client.crt --kubelet-client-key=/etc/kubernetes/pki/apiserver-kubelet-client.key --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname --proxy-client-cert-file=/etc/kubernetes/pki/front-proxy-client.crt --proxy-client-key-file=/etc/kubernetes/pki/front-proxy-client.key --requestheader-allowed-names=front-proxy-client --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.crt --requestheader-extra-headers-prefix=X-Remote-Extra- --requestheader-group-headers=X-Remote-Group --requestheader-username-headers=X-Remote-User --runtime-config= --secure-port=6443 --service-account-issuer=https://kubernetes.default.svc.cluster.local --service-account-key-file=/etc/kubernetes/pki/sa.pub --service-account-signing-key-file=/etc/kubernetes/pki/sa.key --service-cluster-ip-range=10.96.0.0/16 --tls-cert-file=/etc/kubernetes/pki/apiserver.crt --tls-private-key-file=/etc/kubernetes/pki/apiserver.key
vagrant    11172   10883  0 05:46 pts/2    00:00:00 grep --color=auto -i kubelet
```

```bash
docker exec -it 5a4daa5fc374 bash

cat /var/lib/kubelet/config.yaml | grep -i staticpodpath
```

```bash
ls /etc/kubernetes/manifests/

etcd.yaml  kube-apiserver.yaml  kube-controller-manager.yaml  kube-scheduler.yaml
```

```bash
root@mycluster1-control-plane:/# vim /var/lib/kubelet/config.yaml

apiVersion: kubelet.config.k8s.io/v1beta1
authentication:
  anonymous:
    enabled: false
  webhook:
    cacheTTL: 0s
    enabled: true
  x509:
    clientCAFile: /etc/kubernetes/pki/ca.crt
authorization:
  mode: Webhook
  webhook:
    cacheAuthorizedTTL: 0s
    cacheUnauthorizedTTL: 0s
cgroupDriver: systemd
cgroupRoot: /kubelet
clusterDNS:
- 10.96.0.10
clusterDomain: cluster.local
containerRuntimeEndpoint: unix:///run/containerd/containerd.sock
cpuManagerReconcilePeriod: 0s
crashLoopBackOff: {}
evictionHard:
  imagefs.available: 0%
  nodefs.available: 0%
  nodefs.inodesFree: 0%
evictionPressureTransitionPeriod: 0s
failSwapOn: false
fileCheckFrequency: 0s
healthzBindAddress: 127.0.0.1
healthzPort: 10248
httpCheckFrequency: 0s
imageGCHighThresholdPercent: 100
imageMaximumGCAge: 0s
imageMinimumGCAge: 0s
kind: KubeletConfiguration
logging:
  flushFrequency: 0
  options:
    json:
      infoBufferSize: "0"
    text:
      infoBufferSize: "0"
  verbosity: 0
memorySwap: {}
nodeStatusReportFrequency: 0s
nodeStatusUpdateFrequency: 0s
rotateCertificates: true
runtimeRequestTimeout: 0s
shutdownGracePeriod: 0s
shutdownGracePeriodCriticalPods: 0s
staticPodPath: /etc/kubernetes/manifests
streamingConnectionIdleTimeout: 0s
syncFrequency: 0s
volumeStatsAggPeriod: 0s
```