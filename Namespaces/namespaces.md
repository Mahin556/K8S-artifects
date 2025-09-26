## **Kubernetes Namespaces**

### 🔹 What is a Namespace?

* A **namespace** in Kubernetes is a logical partition inside a cluster.
* It allows you to group and isolate resources such as Pods, Services, and Deployments.
* Namespaces help organize large clusters by dividing workloads and users.
* provide organization, security and isolation.

---

### 🔹 Default Behavior

* If **no namespace** is specified during resource creation, the resource is automatically placed in the **`default`** namespace.
* Kubernetes also **creates some namespaces by default**, for example:

  * **`kube-system`** → Contains control plane components (scheduler, controller-manager, DNS, etc.).
  * **`kube-public`** → Accessible to all users, contains publicly readable data.
  * **`kube-node-lease`** → Tracks node heartbeats for availability.
  * **`local-path-store`** -> For storage related resource(based on setup) 

---

### 🔹 Why Use Namespaces?

1. **Environment Separation**

   * Create separate namespaces for environments like **development**, **testing**, and **production**.
   * Prevents accidental modifications (e.g., deleting production resources while testing).

2. **Access Control**

   * Works well with **RBAC (Role-Based Access Control)**.
   * You can define fine-grained permissions per namespace (e.g., developers have access to `dev` namespace but not `prod`).

3. **Resource Management**

   * You can apply **resource quotas** and **limits** per namespace.
   * Helps ensure fair resource usage between teams or environments.

4. **Security and Isolation**

   * Provides a boundary for workloads, reducing the risk of misconfigurations affecting unrelated applications.

---

### 🔹 Communication Between Namespaces

* **Within the same namespace**: Services and Pods communicate directly using their names.
* **Across namespaces**: You must use the **fully qualified domain name (FQDN)**:

  ```
  <service-name>.<namespace>.svc.cluster.local
  ```

  Example:

  ```
  backend-service.dev.svc.cluster.local
  ```

---

### 🔹 Benefits of Namespace-Based Isolation

* **Improved Security** → isolate workloads and control access.
* **Better Organization** → separate teams, environments, or applications.
* **Easier Resource Management** → apply quotas and policies at the namespace level.
* **Reduced Risk** → limits the scope of accidental deletions or misconfigurations.


### Declarative YAML Manifest
```yaml
apiVersion: v1
kind: Namespace
metadata:
    name: demo
```