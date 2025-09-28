### Update resource limits:

```bash
kubectl set resources deployment/nginx-deployment -c=nginx --limits=cpu=200m,memory=512Mi
```

**Output:**

```
deployment.apps/nginx-deployment resource requirements updated
```

Perfect 👌 — let’s go **deep into Kubernetes Resource Requests and Limits**, since they’re central to scheduling, performance, and stability.

---

# ⚡ Kubernetes Resource Requests and Limits

## 1. What are Requests and Limits?

In Kubernetes, every container in a Pod can specify:

* **Resource Request**
  👉 The **minimum guaranteed resources** a container needs.

  * The scheduler uses requests to decide **which node can host the Pod**.
  * If a node doesn’t have enough free requested resources, the Pod won’t schedule there.

* **Resource Limit**
  👉 The **maximum resources** a container is allowed to consume.

  * Enforced by the **kubelet** and container runtime (cgroups).
  * If exceeded, behavior depends on resource type (CPU vs Memory).

---

## 2. Supported Resource Types

Kubernetes supports multiple resource types:

* **CPU** (measured in *cores* or *millicores*)

  * `1 = 1 vCPU core`
  * `1000m = 1 core`
  * `500m = 0.5 core`

* **Memory** (measured in bytes)

  * Units: `Ki`, `Mi`, `Gi`, `Ti` (binary), or `K`, `M`, `G`, `T` (decimal)
  * Example: `128Mi`, `1Gi`

* **Ephemeral Storage** (`ephemeral-storage`)

  * Temporary storage on node disk.

* **Extended Resources** (e.g., GPUs, FPGAs, custom devices)

  * Example: `nvidia.com/gpu: 1`

# 🔹 Memory Units in Kubernetes

Kubernetes allows specifying memory requests/limits in **binary (IEC)** or **decimal (SI)** units.

## **Binary Units (Recommended)**

* Uses **powers of 2**.
* `Ki` = 2¹⁰ = 1,024 bytes
* `Mi` = 2²⁰ = 1,048,576 bytes
* `Gi` = 2³⁰ = 1,073,741,824 bytes

👉 Example:

```yaml
resources:
  requests:
    memory: "200Mi"
```

= **200 × 1,048,576 = 209,715,200 bytes**

---

## **Decimal Units (Allowed but less precise)**

* Uses **powers of 10**.
* `K` = 10³ = 1,000 bytes
* `M` = 10⁶ = 1,000,000 bytes
* `G` = 10⁹ = 1,000,000,000 bytes

👉 Example:

```yaml
resources:
  requests:
    memory: "200M"
```

= **200 × 1,000,000 = 200,000,000 bytes**

---

## 🔹 Key Difference

| Unit    | Bytes per unit | 200 units =       |
| ------- | -------------- | ----------------- |
| `200Mi` | 1,048,576      | 209,715,200 bytes |
| `200M`  | 1,000,000      | 200,000,000 bytes |

👉 `200Mi` is about **4.8% larger** than `200M`.
👉 Best practice: **always use Mi, Gi** (binary units) for memory in Kubernetes.


---

## 3. Behavior: CPU vs Memory

### CPU

* **Request** → Scheduler guarantees CPU cycles.
* **Limit** → If container uses more than limit, CPU is **throttled** (not killed).

### Memory

* **Request** → Scheduler guarantees RAM.
* **Limit** → If container exceeds memory limit, it is **OOMKilled** (terminated).

---

## 4. YAML Syntax

### Example: Pod with Requests and Limits

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: resource-demo
spec:
  containers:
  - name: app
    image: busybox
    command: ["sh", "-c", "sleep 3600"]
    resources:
      requests:
        memory: "256Mi"
        cpu: "500m"
      limits:
        memory: "512Mi"
        cpu: "1"
```

🔹 Meaning:

* Scheduler places pod only on nodes with at least **0.5 CPU + 256Mi RAM** available.
* Pod can burst up to **1 CPU + 512Mi RAM**.
* If it tries to use more → CPU throttled, Memory → OOMKilled.

---

## 5. Omitting Requests and Limits

* If **no request/limit is set**, behavior depends on namespace **LimitRange** or **ResourceQuota**:

  * Default may be applied.
  * Otherwise, Pod runs with **no guaranteed resources**, competing with others (BestEffort QoS).

---

## 6. QoS (Quality of Service) Classes

Kubernetes assigns Pods a QoS class based on requests/limits:

| QoS Class      | Condition                                                               | Behavior                      |
| -------------- | ----------------------------------------------------------------------- | ----------------------------- |
| **Guaranteed** | Every container has `requests == limits` for CPU & Memory               | Highest priority for eviction |
| **Burstable**  | At least one container has request < limit (or only some resources set) | Middle priority               |
| **BestEffort** | No requests/limits set                                                  | Lowest priority, killed first |

---

## 7. Commands to Inspect Resource Usage

Get Pod requests/limits:

```bash
kubectl get pod resource-demo -o jsonpath='{.spec.containers[*].resources}'
```

Check node allocatable vs requests:

```bash
kubectl describe node <node-name>
```

See live usage (metrics-server required):

```bash
kubectl top pod
kubectl top node
```

---

## 8. Resource Quotas and LimitRanges

### ResourceQuota (namespace-level budget)

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: dev
spec:
  hard:
    requests.cpu: "4"
    requests.memory: "8Gi"
    limits.cpu: "10"
    limits.memory: "16Gi"
```

🔹 Restricts total CPU/memory requests & limits in a namespace.

---

### LimitRange (default requests/limits for Pods)

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: default-limits
  namespace: dev
spec:
  limits:
  - default:
      cpu: "500m"
      memory: "1Gi"
    defaultRequest:
      cpu: "200m"
      memory: "512Mi"
    type: Container
```

🔹 Ensures Pods in `dev` namespace get defaults if not specified.

---

## 9. Best Practices

✅ Always set **requests** for critical workloads → ensures they get scheduled properly.
✅ Always set **limits** to prevent “noisy neighbor” problems.
✅ For latency-sensitive apps → set `requests == limits` (Guaranteed QoS).
✅ Use **ResourceQuota + LimitRange** at namespace level to enforce fairness.
✅ Monitor with `kubectl top` + Prometheus + Grafana.
✅ For autoscaling → configure **HPA/VPA** (Horizontal/Vertical Pod Autoscaler).

---

## 10. Real-World Scenarios

* **Web App**: CPU bursts needed → small `request`, bigger `limit`.
* **Batch Job**: May use max available → set high `limit`, moderate `request`.
* **Database**: Needs stability → set `request == limit`.
* **Dev workloads**: Small requests, soft limits → prevents cluster starvation.

---

⚡ In short:

* **Requests = minimum guaranteed**
* **Limits = maximum allowed**
* Together, they control **scheduling, performance, and cluster stability**.


### References
- https://kubernetes.io/docs/concepts/workloads/controllers/deployment/