# 🎯 Taints, Tolerations, and Affinity Together in Kubernetes

## 1. Quick Recap

* **Taints (on nodes)**: repel pods unless they have a matching **toleration**.

  * Example: `kubectl taint nodes node1 dedicated=finance:NoSchedule`
* **Tolerations (on pods)**: allow pods to be scheduled on tainted nodes.

  * Example:

    ```yaml
    tolerations:
    - key: "dedicated"
      operator: "Equal"
      value: "finance"
      effect: "NoSchedule"
    ```
* **Affinity / Anti-Affinity**: choose/prefer certain nodes (based on labels) or co-locate/separate pods.

💡 So:

* **Taints/Tolerations** = “Who *can* go where” (hard gate).
* **Affinity** = “Who *should* go where” (selector/placement logic).

---

## 2. Why Combine Them?

Because they solve **different scheduling problems**:

| Feature                  | Role                                               |
| ------------------------ | -------------------------------------------------- |
| Taint                    | Blocks pods from running on nodes unless tolerated |
| Toleration               | Lets pods bypass that block                        |
| NodeAffinity             | Picks *which* node (labels)                        |
| PodAffinity/AntiAffinity | Picks *with/away from* which pods                  |

👉 Together, they give **hard isolation + intelligent placement**.

---

## 3. YAML Example – Taints + Tolerations + NodeAffinity

### Node setup

```bash
kubectl taint nodes node1 dedicated=finance:NoSchedule
kubectl label nodes node1 team=finance disktype=ssd
```

### Pod spec

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: finance-app
spec:
  tolerations:
  - key: "dedicated"
    operator: "Equal"
    value: "finance"
    effect: "NoSchedule"
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: team
            operator: In
            values:
            - finance
          - key: disktype
            operator: In
            values:
            - ssd
  containers:
  - name: app
    image: myfinance:1.0
```

🔹 How it works:

* Taint prevents general pods from scheduling on `node1`.
* Toleration allows this pod to land there.
* NodeAffinity ensures pod **must land on nodes labeled `team=finance, disktype=ssd`**.

---

## 4. Example – PodAntiAffinity + Taints

Say you want **DB replicas** spread across nodes, but only on “db-dedicated” nodes.

```bash
kubectl taint nodes node2 dedicated=db:NoSchedule
kubectl taint nodes node3 dedicated=db:NoSchedule
kubectl label nodes node2 db=true
kubectl label nodes node3 db=true
```

### StatefulSet with Anti-Affinity

```yaml
affinity:
  podAntiAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
    - labelSelector:
        matchLabels:
          app: database
      topologyKey: kubernetes.io/hostname
tolerations:
- key: "dedicated"
  operator: "Equal"
  value: "db"
  effect: "NoSchedule"
```

🔹 Effect:

* Only **db pods** can tolerate the taint.
* Node labels + anti-affinity ensure **replicas don’t land on the same node**.

---

## 5. Example – PodAffinity + Tolerations

Suppose you want **logging agents** (sidecar-like pods) to run on the same nodes as apps, but apps are tainted.

```bash
kubectl taint nodes node4 dedicated=app:NoSchedule
kubectl label nodes node4 app=true
```

### Logging Agent Pod

```yaml
affinity:
  podAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
    - labelSelector:
        matchLabels:
          app: myapp
      topologyKey: kubernetes.io/hostname
tolerations:
- key: "dedicated"
  operator: "Equal"
  value: "app"
  effect: "NoSchedule"
```

🔹 Effect:

* Pod **must run on the same node as `app=myapp` pod**.
* Toleration allows it to bypass the taint.

---

## 6. Best Practices

* Use **taints** for **hard isolation** (finance, db, GPU, prod).
* Add **tolerations** only for pods that should run on those nodes.
* Use **affinity/anti-affinity** for finer placement rules.
* Keep **soft (preferred) rules** where possible → prevents pods stuck in **Pending**.
* For multi-zone HA → prefer `topologyKey=topology.kubernetes.io/zone`.
* Combine with **PodDisruptionBudgets (PDBs)** for HA guarantees.
