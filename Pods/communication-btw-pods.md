
# **1. How Do Kubernetes Pods Communicate With Each Other?**

### **a) Inside the Same Pod (Multi-container Pod)**

* Containers in the **same Pod** share:

  * **Network namespace** → same IP address and port space.
  * **Volumes** → shared storage.
* Communication is done via **`localhost`** (loopback).

  * Example: `container A` can call `http://localhost:8080` to talk to `container B`.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: demo-pod
  namespace: default
  labels:
    app: nginx
spec:
  initContainers:
    - name: init-con-1
      image: busybox
      command:
      - "/bin/sh"
      - "-c"
      - "sleep 20"
  containers:
    - name: con1
      image: nginx
    - name: con2
      image: curlimages/curl:8.16.0
      command: ["/bin/sh","-c","sleep 3600"]
```
- https://hub.docker.com/r/curlimages/curl

---

### **b) Pod-to-Pod in the Same Cluster**

* Each Pod gets a **unique cluster-private IP** from Kubernetes.
* Pods can talk to each other **directly using these IPs** (no NAT inside cluster).
* Problem: Pod IPs are **ephemeral** (change if pod restarts).

👉 **Solution**: Use **Services** (ClusterIP, NodePort, LoadBalancer).

* A **Service** provides a **stable DNS name** (e.g., `myapp-svc.default.svc.cluster.local`) and load balances across Pods.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: demo-pod1
  namespace: default
  labels:
    app: nginx
spec:
  initContainers:
    - name: init-con-1
      image: busybox
      command:
      - "/bin/sh"
      - "-c"
      - "sleep 20"
  containers:
    - name: con1
      image: nginx

---

apiVersion: v1
kind: Service
metadata:
  name: myapp
spec:
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80


---

apiVersion: v1
kind: Pod
metadata:
  name: demo-pod2
  namespace: default
  labels:
    app: curl
spec:
  initContainers:
    - name: init-con-2
      image: busybox
      command:
      - "/bin/sh"
      - "-c"
      - "sleep 20"
  containers:
    - name: con2
      image: curlimages/curl:8.16.0
      command: ["/bin/sh","-c","sleep 3600"]
```

---

### **c) Pod-to-Pod Across Namespaces**

* Pods can reach others using **fully qualified DNS names**:

  ```
  <service-name>.<namespace>.svc.cluster.local
  ```
* Example: `webapp.frontend.svc.cluster.local`

---

### **d) Pod-to-External World**

* By default, Pods can **initiate outbound connections** (to the Internet) via the cluster’s NAT gateway.
* To allow **incoming traffic** from outside → use:

  * **NodePort Service**
  * **LoadBalancer Service**
  * **Ingress Controller** (for HTTP/HTTPS routing).

---

🔑 **Summary:**

* **Same Pod** → `localhost`
* **Same Cluster Pod** → Pod IP or Service
* **Different Namespace** → FQDN Service DNS
* **Outside World** → NodePort / LoadBalancer / Ingress

### References
- https://www.geeksforgeeks.org/devops/kubernetes-pods/
