- Abstraction over a containers
- Run actual workload.
- Containers have shared network namespace(default) and pid name space(not default).
- All containers within that Pod share this IP address and port space, meaning they can communicate with each other using localhost
- 2 types of pod
  1. single container pod
  2. multi container pod
- Pods are the smallest deployable units in Kubernetes.
  Each Pod:
    - Contains 1 or more containers (usually 1).
    - Shares network namespace (IP, port space).
    - Can share storage volumes.
- All containers within a single Pod are guaranteed to be co-located on the same worker node.
- Containers within a Pod can share storage volumes, allowing them to have a common filesystem and exchange data seamlessly.
- unmanaged pods(orphan pods):- not managed by any high level object
- managed pods:- managed by any high level object called replicasets.
- Most comman terms associated with pods.
  - rollout(to new version)
  - replicas
  - pod failover
  - pod replacement

```
kubectl run <name of pod> --image=<name of the image from registry>
```
```
kubectl run tomcat --image = tomcat:8.0
```



### Pod Operating System
- A Pod in Kubernetes does not have its own OS; instead, it runs one or more containers, and each container’s OS is defined by the base image of the container.
- Example:
  - If the container image is based on Ubuntu, the pod runs processes inside Ubuntu userspace.
  - If based on Alpine, then Alpine is the OS inside the container.
- Pods rely on the Node’s kernel (Linux kernel of the worker node).
- That means containers share the kernel of the host machine but provide isolated userspaces depending on their image.
- Key takeaway:
  - Pod OS = container image’s userspace (Ubuntu, Alpine, Debian, CentOS, etc.).
  - Kernel = always the host node’s kernel.

### Controllers for Managing Pods
- ReplicationController (RC) :- Older object (legacy), Ensures a specified number of Pod replicas are running, Superseded by ReplicaSet.
- ReplicaSet (RS):- Ensures a fixed number of identical Pods are running, Handles scaling Pods up/down, Rarely used directly—usually managed by Deployments.
- Deployment:- Most common way to run applications, Manages ReplicaSets → which manage Pods, Provides(Rolling updates, Rollbacks, Scaling).
- DaemonSet:- Ensures one Pod per node (e.g., logging agent, monitoring agent, kube-proxy).

### Why Not Create Pods Directly?
- Direct Pods are not self-healing.
- If a Pod crashes or a Node fails → Pod disappears.
- controllers (higher-level objects) ensure Pods are automatically recreated and scaled.

### References
- https://www.tutorialspoint.com/kubernetes/kubernetes_pod.htm
