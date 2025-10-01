# Day 14/40 - Taints and Tolerations in Kubernetes 📘🚀

## Check out the video below for Day14 👇

[![Day14/40 - Taints and Tolerations in Kubernetes](https://img.youtube.com/vi/nwoS2tK2s6Q/sddefault.jpg)](https://youtu.be/nwoS2tK2s6Q)

# Taints and Tolerations in Kubernetes 🚧📜

In this guide, we'll explore taints and tolerations in Kubernetes, essential tools for managing where pods can be scheduled in your cluster.

---

## Taints: Putting Up Fences 🚫

Think of taints as "only you are allowed" signs on your Kubernetes nodes. A taint marks a node with a specific characteristic, such as `"gpu=true"`. By default, pods cannot be scheduled on tainted nodes unless they have a special permission called toleration. When a toleration on a pod matches with the taint on the node then only that pod will be scheduled on that node.
- Can set it for CPU intensive nodes
- effect ---> scheduling type(NoScheldue,  NoExecute, preferNoScheldue)

```bash
kubectl taint node <node_name> key=value:effect
kubectl taint node <node_name> --help/-h
kubectl describe node <node-name> | grep -i Taints
kubectl get node <node-name> -o jsonpath='{.spec.taints}'
kubectl get nodes -o custom-columns=NAME:.metadata.name,TAINTS:.spec.taints #Get All Nodes with Their Taints
---

## Tolerations: Permission Slips for Pods ✅

Toleration allows a pod to say, "Hey, I can handle that taint. Schedule me anyway!" You define tolerations in the pod specification to let them bypass the taints.

---

## Taints & Tolerations in Action 🎬

Here’s a breakdown of the commands to manage taints and tolerations:

### Tainting a Node:

```bash
kubectl taint nodes node1 key=gpu:NoSchedule
```

This command taints node1 with the key "gpu" and the effect "NoSchedule." Pods without a toleration for this taint won't be scheduled there.

To remove the taint , you add - at the end of the command , like below.

```bash
kubectl taint nodes node1 key=gpu:NoSchedule-
```

### Adding toleration to the pod:

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: redis
  name: redis
spec:
  containers:
  - image: redis
    name: redis
  tolerations:
  - key: "gpu"
    operator: "Equal"
    value: "true"
    effect: "NoSchedule"
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: toleration-pod
spec:
  tolerations:
  - key: "dedicated"
    operator: "Equal"
    value: "experiment"
    effect: "NoSchedule"
  containers:
  - name: nginx
    image: nginx
```

* Tolerate any taint with effect NoSchedule
```yaml
tolerations:
- operator: "Exists"
  effect: "NoSchedule"
```

* Tolerate a taint with key only
```yaml
tolerations:
- key: "dedicated"
  operator: "Exists"
  effect: "NoSchedule"
```

* Tolerate NoExecute (eviction control)
```yaml
tolerations:
- key: "special"
  operator: "Equal"
  value: "true"
  effect: "NoExecute"
  tolerationSeconds: 3600
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
  tolerations:
  - key: "gpu"
    operator: "Equal"
    value: "false"
    effect: "NoSchedule"
status: {}
```

>Note: This pod specification defines a toleration for the "gpu" taint with the effect "NoSchedule." This allows the pod to be scheduled on tainted nodes.

### Labels vs Taints/Tolerations

Labels group nodes based on size, type,env, etc. Unlike taints, labels don't directly affect scheduling but are useful for organizing resources.

### Limitations to Remember 🚧

Taints and tolerations are powerful tools, but they have limitations. They cannot handle complex expressions like "AND" or "OR." 
So, what do we use in that case? We use a combination of Taints, tolerance, and Node affinity, which we will discuss in the next video.

### NodeSelector

```bash
kubectl label nodes node1 disktype=ssd
kubectl get pods -o wide
kubectl describe pod ssd-pod | grep -i Node:
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: ssd-pod
spec:
  containers:
  - name: nginx
    image: nginx
  nodeSelector:
    disktype: ssd
```
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deploy
spec:
  replicas: 2
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
      nodeSelector:
        disktype: ssd
```
