## ⚙️ 1. **What Is Pod Priority in Kubernetes?**

* **Pod Priority** determines **which Pod is more important** when resources (like CPU/memory) become scarce on a node or in the cluster.
* When the cluster runs out of resources:

  * **Higher-priority Pods** are scheduled first.
  * **Lower-priority Pods** may get **preempted (terminated)** to make room.

So, priority helps Kubernetes decide:

* Which Pods to **schedule first**, and
* Which Pods to **evict** during **resource pressure**.

---

## 🧱 2. **PriorityClass – How Priority Is Defined**

* Priority is **not** directly set in the Pod — instead, it is defined in a **PriorityClass object**.
* A `PriorityClass` is a **cluster-level resource** that defines:

  * The **name** of the class.
  * The **numerical priority value**.
  * Whether it is the **default** for all Pods that don’t specify one.
  * An optional **description**.

---

### 🔹 Example – PriorityClass Definition

```yaml
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: high-priority
value: 1000
globalDefault: false
description: "This class is for high priority workloads."
```

**Explanation:**

* `value`: The priority number (integer).

  * Higher number = higher priority.
* `globalDefault`:

  * If `true`, Pods without a priority class get this one automatically.
* `description`: Human-readable explanation.

---

## 📦 3. **Using PriorityClass in a Pod**

You assign the class name to a Pod via the `priorityClassName` field in its spec.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: critical-app
spec:
  priorityClassName: high-priority
  containers:
    - name: busy
      image: busybox
      command: ["sh", "-c", "sleep 3600"]
```

When created, the Pod automatically gets:

* The **priority value** defined in the `PriorityClass` object.
* The **scheduler** will use this value when making scheduling or eviction decisions.

---

## 📈 4. **Priority Values Range**

* Kubernetes priority is just an **integer value**, typically between:

  * **0 to 1000000000 (1 billion)**
* There’s no fixed limit, but higher values mean higher priority.

| Priority                 | Meaning                                 |
| ------------------------ | --------------------------------------- |
| Low (e.g., 0–100)        | Background or batch workloads           |
| Medium (e.g., 500–1000)  | Normal applications                     |
| High (e.g., 1000–100000) | Critical system or production workloads |

---

## ⚔️ 5. **Pod Preemption – What Happens When Resources Are Full**

When a new Pod **cannot be scheduled** due to lack of resources:

1. The scheduler looks for **nodes** where it *could* run if some lower-priority Pods were removed.
2. If found:

   * The **lower-priority Pods are preempted** (killed).
   * The **higher-priority Pod is scheduled** in their place.

This process is called **Preemption**.

---

### 🔁 Example of Preemption Scenario

* Node has limited CPU/memory.
* Running Pods:

  * Pod A → Priority 100
  * Pod B → Priority 200
* New Pod C → Priority 1000

If no space for Pod C:

* Kubernetes **evicts Pod A or B** (the lowest-priority one) to make room.
* Pod C gets scheduled.

---

## ⚙️ 6. **How Preemption Works (Internally)**

1. **Scheduler** tries to schedule a Pod.
2. If **no suitable node** is found:

   * It looks for **preemption opportunities**.
3. Scheduler identifies:

   * Which **Pods can be removed** to free resources.
   * Which **node would fit** the pending Pod after preemption.
4. Scheduler **marks victims** for preemption (lower-priority Pods).
5. **Preemption occurs**:

   * Victim Pods receive `TerminationGracePeriodSeconds` to shut down.
   * The high-priority Pod is then scheduled.

---

## 🧩 7. **Preemption and PriorityClass Settings**

Each `PriorityClass` can also affect how preemption is handled.

There’s a special field in Pod spec:

```yaml
preemptionPolicy: Never
```

By default, it is:

```yaml
preemptionPolicy: PreemptLowerPriority
```

**Options:**

* `PreemptLowerPriority` → Default; allows preemption.
* `Never` → Pod will **not preempt** lower-priority Pods even if it can’t be scheduled.

---

### 🔹 Example with `preemptionPolicy: Never`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: no-preempt-pod
spec:
  priorityClassName: high-priority
  preemptionPolicy: Never
  containers:
  - name: busy
    image: busybox
    command: ["sh", "-c", "sleep 3600"]
```

This Pod will **not evict** other Pods to make room — it will simply stay pending until space is available.

---

## 🧾 8. **System Reserved Priority Classes**

Kubernetes has **built-in PriorityClasses** for critical system components:

| Name                      | Value      | Purpose                                                 |
| ------------------------- | ---------- | ------------------------------------------------------- |
| `system-cluster-critical` | 2000000000 | Cluster-level critical components (e.g. kube-dns)       |
| `system-node-critical`    | 2000001000 | Node-level critical daemons (e.g. kubelet, node agents) |

These ensure critical system services **never get preempted** by user Pods.

---

## 🧠 9. **How to View Priority and Preemption Status**

* To list all PriorityClasses:

  ```bash
  kubectl get priorityclasses
  ```

* To see Pod priority and preemption settings:

  ```bash
  kubectl get pod mypod -o yaml | grep -E 'priority|preempt'
  ```

* To check which Pods were preempted:

  ```bash
  kubectl describe pod <victim-pod-name>
  ```

  You’ll see messages like:

  ```
  Preempted by pod <high-priority-pod>
  ```

---

## 📊 10. **Example: Priority and Preemption in Action**

**Step 1 – Create PriorityClasses**

```yaml
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: low-priority
value: 100
globalDefault: false
---
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: high-priority
value: 1000
globalDefault: false
```

**Step 2 – Create Pods**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: low-pod
spec:
  priorityClassName: low-priority
  containers:
  - name: low
    image: busybox
    command: ["sh", "-c", "sleep 3600"]
---
apiVersion: v1
kind: Pod
metadata:
  name: high-pod
spec:
  priorityClassName: high-priority
  containers:
  - name: high
    image: busybox
    command: ["sh", "-c", "sleep 3600"]
```

**Step 3 – Observe Behavior**

* If the node is full:

  * The scheduler **preempts `low-pod`**.
  * Frees resources.
  * Schedules `high-pod`.

---

## 🧭 11. **Best Practices**

* Use **higher priority** for production and system-critical workloads.
* Use **lower priority** for test, batch, or non-essential jobs.
* Avoid setting **all Pods to high priority**, or preemption becomes meaningless.
* Use `preemptionPolicy: Never` for sensitive Pods that must not be terminated automatically.

---

## ✅ 12. **Summary Table**

| Concept               | Definition                                           | Example                                           |
| --------------------- | ---------------------------------------------------- | ------------------------------------------------- |
| **PriorityClass**     | Defines priority value                               | `value: 1000`                                     |
| **priorityClassName** | Used in Pod spec to assign priority                  | `priorityClassName: high-priority`                |
| **Preemption**        | Evicting lower-priority Pods to schedule higher ones | Happens automatically                             |
| **preemptionPolicy**  | Controls whether a Pod can preempt others            | `Never` or `PreemptLowerPriority`                 |
| **Built-in Classes**  | For system-critical Pods                             | `system-node-critical`, `system-cluster-critical` |

### References:
- https://devopscube.com/pod-priorityclass-preemption/
