### **ClusterIP (Service IP)**

* In Kubernetes, **Pods have dynamic IPs** that can change when:

  * A pod restarts
  * A new pod is created
* Because Pod IPs are not stable, we use a **Service** to provide a stable way to communicate with Pods.
* A **ClusterIP Service** exposes a stable **virtual IP (ClusterIP)** inside the cluster.
* It allows different components (like frontend and backend) to **communicate with each other** reliably.
* You can access backend Pods using:

  * The **ClusterIP address**
  * The **Service name** (DNS resolves to ClusterIP)
* Ports exposed through ClusterIP are typically **HTTP (80)** or **HTTPS (443)** ports.
* **ClusterIP is internal-only** (cannot be accessed directly from outside the cluster).
* Also called **Service IP**.

![Alt Text](https://github.com/Mahin556/K8S-artifects/blob/main/images/cip1.png)

---

### **Endpoints**

* An **Endpoint** is the actual **Pod IP + Port** where the Service traffic is redirected.
* Kubernetes automatically maintains these Endpoints:

  * When a new Pod matching the Service selector is created → its IP is added as an Endpoint.
  * When a Pod is deleted or becomes unavailable → its IP is removed.
* This makes Services dynamic and ensures load balancing across all available Pods.

---

### **How It Works**

1. **Service YAML** (example):

   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: cluster-svc
     labels:
       env: demo
   spec:
     type: ClusterIP
     ports:
       - port: 80
         targetPort: 80
     selector:
       env: demo
   ```

   * `type: ClusterIP` → creates an internal Service IP.
   * `port` → Service port exposed.
   * `targetPort` → Actual Pod container port.
   * `selector` → Matches Pods with label `env=demo`.

2. **Service created**:

   ```bash
   kubectl get svc
   ```

   Example output:

   ```
   NAME          TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    
   cluster-svc   ClusterIP   10.96.166.236   <none>        80/TCP
   kubernetes    ClusterIP   10.96.0.1       <none>        443/TCP
   ```

3. **Service details**:

   ```bash
   kubectl describe svc cluster-svc
   ```

   Key fields:

   * **ClusterIP** → Virtual service IP (e.g., `10.96.166.236`).
   * **Ports** → Service port and target port mapping.
   * **Endpoints** → Actual Pod IPs (`10.244.x.x:80`).

4. **Endpoints object**:

   ```bash
   kubectl get ep
   ```

   Example output:

   ```
   NAME          ENDPOINTS                               AGE
   cluster-svc   10.244.1.3:80,10.244.2.2:80,10.244.2.3:80   117s
   ```

   * Shows Pod IPs where Service traffic is forwarded.
   * Keeps updating automatically with Pod lifecycle changes.