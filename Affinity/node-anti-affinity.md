# 🚫 Node Anti-Affinity in Kubernetes

## 1. What is Node Anti-Affinity?

* **Node Anti-Affinity** lets you **prevent pods from being scheduled on certain nodes**, based on node **labels**.
* Example:

  * "Do not schedule this pod on nodes labeled `env=dev`."
  * "Avoid GPU nodes for lightweight workloads."

It uses the same **affinity syntax**, but with **negative operators** (`NotIn`, `DoesNotExist`).

---

## 2. How It Differs

| Feature   | Node Affinity           | Node Anti-Affinity        |
| --------- | ----------------------- | ------------------------- |
| Purpose   | Attract pods to nodes   | Repel pods from nodes     |
| Example   | "Run only on SSD nodes" | "Do not run on HDD nodes" |
| Operators | In, Exists, etc.        | NotIn, DoesNotExist       |

---

## 3. Types (same as Node Affinity)

* **requiredDuringSchedulingIgnoredDuringExecution** → Hard rule

  * Pod won’t be scheduled at all if the anti-affinity condition matches.
* **preferredDuringSchedulingIgnoredDuringExecution** → Soft rule

  * Scheduler avoids nodes if possible, but will place if no alternatives.

---

## 4. Node Anti-Affinity Operators

* **NotIn** → Label value must not be in the list
* **DoesNotExist** → Label key must not exist

---

## 5. YAML Examples

### A) Hard Node Anti-Affinity

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-node-anti-affinity
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: disktype
            operator: NotIn
            values:
            - hdd
  containers:
  - name: nginx
    image: nginx
```

🔹 This pod will **never** schedule on nodes labeled `disktype=hdd`.

---

### B) Soft Node Anti-Affinity

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-soft-node-anti-affinity
spec:
  affinity:
    nodeAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 100
        preference:
          matchExpressions:
          - key: env
            operator: NotIn
            values:
            - dev
  containers:
  - name: busybox
    image: busybox
    command: ["sleep", "3600"]
```

🔹 Pod will **prefer avoiding nodes labeled `env=dev`**, but still run there if no other option.

---

### C) Using `DoesNotExist`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-no-gpu
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: gpu
            operator: DoesNotExist
  containers:
  - name: app
    image: myapp:1.0
```

🔹 Pod only schedules on nodes **without any `gpu` label**.

---

## 6. Commands for Node Anti-Affinity

Label nodes:

```bash
kubectl label nodes node1 disktype=hdd
kubectl label nodes node2 env=dev
```

Check where pod scheduled:

```bash
kubectl get pod pod-node-anti-affinity -o wide
```

Debug pending pod:

```bash
kubectl describe pod pod-node-anti-affinity
```

---

## 7. Real Use Cases

✅ Keep lightweight apps away from expensive GPU nodes
✅ Ensure dev/test workloads don’t land on prod nodes
✅ Avoid running stateful apps on HDD-backed nodes
✅ Separate compliance workloads from general-purpose nodes

---

## 8. Best Practices

* Use **soft anti-affinity** where possible → prevents scheduling failures.
* Combine with **taints & tolerations** for stricter isolation.
* Use **meaningful node labels** (`gpu=true`, `env=prod`, `storage=hdd`).
* Test carefully → too many anti-affinity rules can make pods **unschedulable**.
