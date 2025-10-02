
* Services enable **communication between different components** within and outside the application.
* They **connect applications together** or allow users to access them.
* Services are Kubernetes objects, just like Pods, Deployments, Statefullsets, RC or ReplicaSets.
* A Service is a REST object (just like Pod, Deployment, etc).
* You define it in YAML and submit it to the API server.
* Service selects pods based on labels.

* Pods are ephemeral (they can be killed, restarted, rescheduled on different nodes). Their IP addresses change dynamically.
* Services solve this problem by:
  * Giving a stable IP address and a DNS name.
  * Acting as a load balancer for the pods it routes traffic to.
  * Ensuring that communication between components (frontend, backend, DB) is consistent.
* Think of a Service as a virtual IP + load balancer for a group of pods.

* Example: An application may have:
  * Front-end pods (serve users)
  * Back-end pods (process data)
  * Pods connecting to an external database
* **Services handle connectivity** between these pods:
  * Front-end ↔ Back-end
  * Front-end ↔ Users
  * Back-end ↔ External data source

---

### **2. Types of Kubernetes Services**

1. **NodePort**
   * Makes a Pod accessible on a port of the Kubernetes node.
   * Acts as a **bridge** to access Pods from outside the cluster.

2. **ClusterIP** (default)
   * Creates a **virtual IP inside the cluster**.
   * Enables communication between services (e.g., web server ↔ database server).

3. **LoadBalancer**
   * Provisions a **cloud provider load balancer** for your application.
   * Distributes traffic across multiple nodes/pods.

---

### **4. NodePort Ports Explained**

* **TargetPort**: Port on the Pod (e.g., `80`)
* **Port**: Port exposed by the Service inside the cluster
* **NodePort**: Port on the Node for external access (default range: 30000–32767)

---

### **5. Example NodePort Service Definition**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-service
spec:
  type: NodePort
  selector:
    app: my-webapp
    type: webserver
  ports:
    - port: 80           # Required
      targetPort: 80     # Defaults to 'port' if not specified
      nodePort: 32000    # Automatically allocated if not specified
```

**Notes:**

* Multiple ports can be defined in a single Service.
* **Selectors** link the Service to Pods using **labels**.

---

### **6. Create and View NodePort Service**

**Create Service:**

```bash
kubectl create -f service-definition.yaml
```

**Check Service:**

```bash
kubectl get services
```

---

### **7. NodePort Service in Production**

* Usually multiple Pods with the same labels for **high availability**.
* Service automatically selects all matching Pods as **endpoints**.
* Acts as a **built-in load balancer**:

  * Distributes traffic across all matching Pods
  * Updates automatically when Pods are added or removed

![Alt Text](https://github.com/Mahin556/K8S-artifects/blob/main/images/svc1.png)


### References:
- https://technos.medium.com/kubernetes-services-for-absolute-beginners-nodeport-139b7060fe3
- https://www.geeksforgeeks.org/devops/kubernetes-cluster-ip-vs-node-port/ *
- https://www.geeksforgeeks.org/devops/kubernetes-services/ *
