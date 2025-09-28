# 🚫 Pod Anti-Affinity in Kubernetes

## 1. What is Pod Anti-Affinity?

* **Pod Anti-Affinity** ensures that pods **do not get scheduled on the same node (or zone, or region)** as other pods with specific labels.
* It’s useful for **spreading workloads** to improve availability and fault tolerance.

📌 Example:

* Don’t schedule two replicas of the same app on the same node → avoids single-node failure taking down the app.

---

## 2. How It Works

* Matches **pod labels** (not node labels).
* Uses a **topologyKey** (scope of separation):

  * `kubernetes.io/hostname` → prevent scheduling on same node
  * `topology.kubernetes.io/zone` → prevent scheduling in same zone
  * `topology.kubernetes.io/region` → prevent scheduling in same region

---

## 3. Types

Like Pod Affinity:

* **requiredDuringSchedulingIgnoredDuringExecution** → Hard rule

  * If no valid node exists, pod stays Pending.
* **preferredDuringSchedulingIgnoredDuringExecution** → Soft rule

  * Scheduler avoids co-location if possible, but may still place pods together.

---

## 4. YAML Examples

### A) Hard Pod Anti-Affinity (no two pods on same node)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: app
                operator: In
                values:
                - web
            topologyKey: kubernetes.io/hostname
      containers:
      - name: nginx
        image: nginx
```

🔹 Ensures no two `web` pods run on the same **node**.

---

### B) Soft Pod Anti-Affinity (prefer spreading across zones)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api
spec:
  replicas: 3
  selector:
    matchLabels:
      app: api
  template:
    metadata:
      labels:
        app: api
    spec:
      affinity:
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
          - weight: 100
            podAffinityTerm:
              labelSelector:
                matchExpressions:
                - key: app
                  operator: In
                  values:
                  - api
              topologyKey: topology.kubernetes.io/zone
      containers:
      - name: api-container
        image: myapi:1.0
```

🔹 Pods will **prefer spreading across different zones**, but still run in same zone if needed.

---

### C) Mixed: Hard + Soft Rules

```yaml
affinity:
  podAntiAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
    - labelSelector:
        matchLabels:
          app: db
      topologyKey: kubernetes.io/hostname
    preferredDuringSchedulingIgnoredDuringExecution:
    - weight: 50
      podAffinityTerm:
        labelSelector:
          matchLabels:
            app: db
        topologyKey: topology.kubernetes.io/zone
```

🔹 Pods **must not run on the same node**, and should also **spread across zones** if possible.

---

## 5. Real-World Use Cases

✅ High availability: Ensure replicas of an app don’t all run on the same node
✅ Fault tolerance: Spread across zones/regions to handle outages
✅ Stateful apps (DBs, brokers): Keep replicas apart to avoid data loss
✅ Large workloads: Prevent resource contention by separating pods

---

## 6. Best Practices

* For HA deployments, combine **pod anti-affinity** with **replicas > 1**.
* Use **soft rules** when clusters are small (strict rules can cause scheduling failures).
* Use **topology spread constraints** (newer alternative) for easier spreading across zones/nodes.
* Check pod scheduling with:

  ```bash
  kubectl describe pod <pod-name>
  ```

---

## 7. Pod Affinity vs Pod Anti-Affinity

| Feature  | Pod Affinity                              | Pod Anti-Affinity                  |
| -------- | ----------------------------------------- | ---------------------------------- |
| Purpose  | Co-locate pods                            | Spread pods apart                  |
| Use case | Frontend near backend                     | DB replicas on different nodes     |
| Risk     | Too much co-location → reduced resilience | Too much separation → Pending pods |
