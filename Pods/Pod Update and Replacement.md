
# **1. Pod Update and Replacement in Kubernetes**

### **a) Updating Pods**

* You **should not update a Pod directly** (since Pods are immutable).
* Instead, you update the **controller** (Deployment/ReplicaSet/DaemonSet).
* Kubernetes replaces old Pods with new ones in a **rolling update** fashion:

  * Creates new Pod (with updated config/image).
  * Gradually terminates old Pods.
  * Ensures no downtime.

Example: Update Nginx image in Deployment

```bash
kubectl set image deployment/nginx-deploy nginx=nginx:1.25
```

This triggers a rolling update.

---

### **b) Pod Replacement**

* Pods are **ephemeral**. If:

  * A Pod **crashes**
  * A Node **fails**
  * Or you **delete the Pod**

👉 The controller (like a Deployment or ReplicaSet) **automatically creates a replacement Pod** with the same spec but a new IP.

This gives **self-healing** capability.

---

### **Car Analogy 🚗**

* **Update (rolling upgrade):** like upgrading your engine software while the car is still running.
* **Replacement (self-healing):** like swapping a flat tire with a spare while the car keeps moving.

### References
- https://www.geeksforgeeks.org/devops/kubernetes-pods/
