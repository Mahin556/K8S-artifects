# **Accessing Applications in a Pod**

Once you have a running Pod, like our `web-server-pod` with Nginx, you can access it in two main ways: **temporarily via port-forwarding** or **permanently via a Service**. Here we focus on **port-forwarding**.

---

## **1. Using kubectl port-forward**

### Command:

```bash
kubectl port-forward pod/web-server-pod 8080:80
```

### Explanation:

* **`8080:80`** → Maps local port `8080` to Pod’s container port `80`.
* **kubectl** creates a **tunnel** from your local machine → cluster node → Pod.
* This allows you to access the Pod **without exposing it externally**.

---

### **Step-by-Step**

1. Run the command in your terminal.
2. Leave the terminal open while you test access.
3. Open a browser and go to:

   ```
   http://localhost:8080
   ```

   You should see the **Nginx homepage** served by the Pod.
4. To stop port forwarding, press **CTRL + C**.

---

## **2. What Happens Internally**

1. **kubectl binds local port** → 8080 in this example.
2. **kubectl communicates with the Kubernetes API server**.
3. API server forwards traffic → the **node** running the Pod → the Pod’s container port 80.
4. All communication is **over a single HTTP/HTTPS connection** (tunnel).

> ⚠️ Note: `kubectl port-forward` is **primarily for debugging or testing**, not production access.

---

## **3. Recommended Production Access**

For **real applications**, you should expose your Pod via a **Kubernetes Service**:

* **ClusterIP Service** → Internal access in cluster.
* **NodePort Service** → Access via node IP + port.
* **LoadBalancer Service** → External access through cloud provider’s load balancer.

This ensures **stable IP/hostname** and **scalable access** to your Pods.
