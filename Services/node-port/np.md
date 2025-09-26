# NodePort with Multiple Pods on Different Nodes

* To make your pods accesiable to external user nodeport service can be used.
* Can load balance a traffic between a multiple pods spread accross multiple nodes.
---

## 🔹 Communication Flow (External → Internal)

1. **User Accesses NodePort**

   * A user sends a request to the **Node IP** at the **NodePort** (e.g., `http://<NodeIP>:30001`).
   * The NodePort (e.g., `30001`) is opened on **every node** in the cluster.
   * Port range for **NodePort(30000-32767)**.

2. **NodePort Service**

   * Listens on `30001` across all cluster nodes.
   * Forwards the request to a Kubernetes **Service**.

3. **Kubernetes Service**

   * Acts as a **load balancer**.
   * Listens on `ClusterIP:80` (internal) and routes traffic to one of the backend Pods.

4. **Pod Level (NGINX Containers)**

   * The request is forwarded to one of the Pods running Nginx (on port 80).
   * The Pod can be on **any node**, routing is handled via **kube-proxy**.

5. **Response**

   * The chosen Pod processes the request and sends the response back to the user via the same route.

---

## 🔹 Internal DNS Resolution (Cluster Communication)

* Kubernetes uses **CoreDNS** for service discovery.
* Pods can reach the service using its **name**:

  ```
  http://<service-name>
  e.g., http://nginx-service
  ```
* CoreDNS resolves the service name to its **ClusterIP**, which then routes traffic to the correct Pod.

---

# 📝 YAML Manifests

### 1. **Deployment** (nginx with 3 replicas)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deploy
  labels:
    env: demo
spec:
  replicas: 3
  selector:
    matchLabels:
      env: demo
  template:
    metadata:
      labels:
        env: demo
    spec:
      containers:
        - name: nginx
          image: nginx:latest
          ports:
            - containerPort: 80
```

---

### 2. **Service** (NodePort type)

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nodeport-svc
  labels:
    env: demo
spec:
  type: NodePort
  selector:
    env: demo
  ports:
    - port: 80         # Service port (ClusterIP port)
      targetPort: 80   # Container port (inside Pod)
      nodePort: 30001  # NodePort exposed externally
```

---

# 🔹 How It Works

* **Users** → `http://<NodeIP>:30001`
* **NodePort** forwards request to → `Service (ClusterIP:80)`
* **Service** load-balances across → `nginx Pods` (replicas on multiple nodes)
* **Pod** responds → goes back through service → NodePort → User

---

# ✅ Key Takeaways

* **Deployment** ensures 3 replicas of `nginx` are always running.
* **NodePort Service** makes the Pods accessible externally on **every node’s IP at port 30001**.
* **Service + kube-proxy** load-balance the traffic across all Pods, no matter which node they are running on.
* **CoreDNS** allows Pods to communicate internally by service name (DNS).

