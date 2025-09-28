# 🤝 Pod Affinity in Kubernetes

## 1. What is Pod Affinity?

* **Pod Affinity** is a scheduling rule that says:
  👉 “Place this pod *close to* (on the same node, zone, rack, etc.) other pods that have specific labels.”
* It’s used to **co-locate pods** for performance, latency, or data-sharing reasons.

📌 Example:

* Run a **frontend pod** on the same node/zone as the **backend pod** to reduce network latency.

---

## 2. How Pod Affinity Works

* Unlike **Node Affinity** (which matches against **node labels**),
  **Pod Affinity** matches against **labels on other pods** and considers **topology domains**.
* Topology domains are defined by **node labels** like:

  * `kubernetes.io/hostname` (same node)
  * `topology.kubernetes.io/zone` (same zone)
  * `topology.kubernetes.io/region` (same region)
  * custom node labels

---

## 3. Types of Pod Affinity

* **requiredDuringSchedulingIgnoredDuringExecution**

  * Hard rule: pod won’t schedule unless co-location requirement is met.
* **preferredDuringSchedulingIgnoredDuringExecution**

  * Soft rule: scheduler *tries* to co-locate pods, but will still schedule elsewhere if not possible.

---

## 4. YAML Structure

Pod affinity lives under:

```yaml
spec:
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution: [...]
      preferredDuringSchedulingIgnoredDuringExecution: [...]
```

Each rule uses:

* **labelSelector** → Match pods by labels
* **topologyKey** → Defines the topology scope (node, zone, region, etc.)

---

## 5. YAML Examples

### A) Hard Pod Affinity (same node as backend pods)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: frontend
spec:
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: app
            operator: In
            values:
            - backend
        topologyKey: kubernetes.io/hostname
  containers:
  - name: nginx
    image: nginx
```

🔹 Pod runs only on nodes that already have a pod with label `app=backend`.

---

### B) Soft Pod Affinity (prefer same zone as backend)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: analytics
spec:
  affinity:
    podAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 100
        podAffinityTerm:
          labelSelector:
            matchExpressions:
            - key: app
              operator: In
              values:
              - backend
          topologyKey: topology.kubernetes.io/zone
  containers:
  - name: busybox
    image: busybox
    command: ["sleep", "3600"]
```

🔹 Pod will prefer to run in the same **zone** as `app=backend`, but not mandatory.

---

### C) Complex Pod Affinity (match multiple labels)

```yaml
affinity:
  podAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
    - labelSelector:
        matchExpressions:
        - key: app
          operator: In
          values: ["backend"]
        - key: tier
          operator: In
          values: ["db"]
      topologyKey: topology.kubernetes.io/region
```

🔹 Pod runs in the same **region** as pods labeled `app=backend, tier=db`.

---

## 6. Commands for Pod Affinity

Check labels on existing pods:

```bash
kubectl get pods --show-labels
```

Apply affinity pod:

```bash
kubectl apply -f pod-affinity.yaml
```

See where it got scheduled:

```bash
kubectl get pod frontend -o wide
```

Debug pending pods:

```bash
kubectl describe pod frontend
```

---

## 7. Real Use Cases

✅ Place **frontend** close to **backend** for low latency
✅ Run **analytics workers** in same zone as **data nodes**
✅ Ensure **logging agents** run on the same node as the app pods
✅ Improve **cache hit rates** by co-locating with cache pods

---

## 8. Best Practices

* Use **soft (preferred) affinity** where possible to avoid scheduling failures.
* Use **topologyKey=zone** or **region** for HA (don’t force same node unless necessary).
* Combine with **Pod Anti-Affinity** to spread workloads while still co-locating groups.
* Be mindful: **too strict affinity rules = pods stuck in Pending**.
