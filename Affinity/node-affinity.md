# 🚀 Node Affinity in Kubernetes

## 1. What is NodeAffinity?

* **NodeAffinity** is a set of rules that allow you to control **which nodes a Pod can be scheduled on**, based on node **labels**.
* It’s part of the **PodSpec → affinity** field.
* Similar to `nodeSelector`, but **more expressive and flexible**.

---

## 2. Types of NodeAffinity

Node affinity rules come in **two flavors**:

### (A) **requiredDuringSchedulingIgnoredDuringExecution**

* **Hard requirement**: Pod will only be scheduled if the rules are satisfied.
* If no matching node exists → Pod remains in **Pending** state.
* Example: “Only run on nodes in `zone=us-east-1a`.”

---

### (B) **preferredDuringSchedulingIgnoredDuringExecution**

* **Soft requirement**: Scheduler will try to place the Pod on preferred nodes, but if unavailable, it will still run elsewhere.
* Example: “Prefer nodes with `ssd=true`, but run anywhere if none are available.”

---

### (C) Execution Phases

* `IgnoredDuringExecution`: Once a Pod is running, node affinity **does not force eviction** even if labels change.
  (There is no `RequiredDuringExecution` today.)

---

## 3. Node Affinity vs Alternatives

| Feature           | NodeSelector            | NodeAffinity                                      | Taints & Tolerations            |
| ----------------- | ----------------------- | ------------------------------------------------- | ------------------------------- |
| Expressiveness    | Simple (key=value only) | Complex (In, NotIn, Exists, DoesNotExist, Gt, Lt) | Simple key=value matching       |
| Hard / Soft rules | Only hard               | Both (required / preferred)                       | Only hard (must tolerate taint) |
| Use case          | Quick pinning           | Flexible scheduling                               | Node isolation / restrictions   |

---

## 4. Operators in NodeAffinity

Supported in `matchExpressions`:

* **In**: label value must be in given list.
* **NotIn**: label value must not be in given list.
* **Exists**: node must have the label key.
* **DoesNotExist**: node must not have the label key.
* **Gt**: node label value > given value.
* **Lt**: node label value < given value.

---

## 5. YAML Syntax

### Example: Required Node Affinity

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-required-affinity
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: disktype
            operator: In
            values:
            - ssd
  containers:
  - name: nginx
    image: nginx
```

🔹 Pod runs **only on nodes labeled**: `disktype=ssd`

---

### Example: Preferred Node Affinity

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-preferred-affinity
spec:
  affinity:
    nodeAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 80
        preference:
          matchExpressions:
          - key: zone
            operator: In
            values:
            - us-east-1a
      - weight: 20
        preference:
          matchExpressions:
          - key: high-memory
            operator: Exists
  containers:
  - name: busybox
    image: busybox
    command: ["sleep", "3600"]
```

🔹 Scheduler **prefers zone=us-east-1a** (weight 80), then `high-memory` (weight 20).

---

### Combined Example

You can **mix required + preferred** rules:

```yaml
affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
      - matchExpressions:
        - key: env
          operator: In
          values:
          - prod
    preferredDuringSchedulingIgnoredDuringExecution:
    - weight: 50
      preference:
        matchExpressions:
        - key: instance-type
          operator: In
          values:
          - m5.large
```

---

## 6. How to Add Labels to Nodes

Before using node affinity, ensure nodes have labels:

```bash
kubectl label nodes <node-name> disktype=ssd
kubectl label nodes <node-name> zone=us-east-1a
```

Check labels:

```bash
kubectl get nodes --show-labels
```

Remove label:

```bash
kubectl label nodes <node-name> disktype-
```

---

## 7. Debugging NodeAffinity Issues

* Pod stuck in **Pending**? Check why:

```bash
kubectl describe pod <pod-name>
```

Look under **Events** → will show why scheduler cannot place it.

---

## 8. Real Use Cases

✅ Run GPU workloads only on nodes with `accelerator=nvidia`
✅ Pin database pods to SSD-backed nodes (`disktype=ssd`)
✅ Keep dev workloads away from prod nodes (`env=dev`)
✅ Prefer cheap spot instances but fall back to on-demand

---

## 9. Best Practices

* Use **node affinity** instead of `nodeSelector` for flexibility.
* Combine with **podAffinity/podAntiAffinity** for topology-aware placement.
* Combine with **taints & tolerations** for strict isolation.
* Avoid over-restricting pods → may cause scheduling failures.
* Label nodes with **meaningful metadata** (`zone`, `instance-type`, `storage`, etc.).
