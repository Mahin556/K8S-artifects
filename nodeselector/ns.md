## 🌐 What is nodeSelector?

* `nodeSelector` is the **simplest way to schedule Pods onto specific Nodes** in a Kubernetes cluster.
* It works by matching **labels on Nodes** with the values specified in the Pod definition.
* Think of it as: *“Run this Pod only on nodes that have this label.”*

---

## 🧩 How nodeSelector Works

1. You **label a node** with a key-value pair.
   Example:

   ```sh
   kubectl label nodes worker1 disktype=ssd
   ```

   → Now node `worker1` has label: `disktype=ssd`.

2. In the Pod spec, you add a `nodeSelector`:

   ```yaml
   nodeSelector:
     disktype: ssd
   ```

   → Pod will be scheduled **only on nodes that have this label**.

---

## 📝 Example: Pod with nodeSelector

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
  - name: nginx
    image: nginx
  nodeSelector:
    disktype: ssd
```

* This Pod will only run on nodes labeled `disktype=ssd`.
* If no such node exists, the Pod will stay in **Pending** state.

---

## 🔧 Managing Node Labels

* View labels of a node:

  ```sh
  kubectl get nodes --show-labels
  ```
* Add a label:

  ```sh
  kubectl label nodes <node-name> key=value
  ```
* Remove a label:

  ```sh
  kubectl label nodes <node-name> key-
  ```

---

## 📊 Real-Life Use Cases

* Run **database Pods** on nodes with SSDs:

  ```yaml
  nodeSelector:
    disktype: ssd
  ```
* Run **GPU workloads** only on GPU nodes:

  ```yaml
  nodeSelector:
    accelerator: nvidia
  ```
* Separate workloads by environment:

  ```yaml
  nodeSelector:
    env: production
  ```

---

## ⚠️ Limitations of nodeSelector

* Only supports **exact match** (`key=value`).
* No complex expressions (like `!=`, `in`, `notin`).
* For advanced scheduling → use **Node Affinity** (more flexible).

