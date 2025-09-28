### 1. Why Affinity is Needed

* Sometimes we want to **control where pods are scheduled** beyond just taints/tolerations.
* Use cases:

  * Ensure **related pods run on the same node** (for caching, data locality).
  * Ensure **critical pods are spread across nodes** for high availability.
  * Example: Place a frontend pod and its backend pod on the same node, or ensure two replicas of a database never run on the same node.

---

### 2. Types of Affinity

* **Node Affinity** → Schedule pods based on **node labels**.
* **Pod Affinity** → Schedule pods close to **other pods**.
* **Pod Anti-Affinity** → Prevent pods from being scheduled on the **same node** as others.

---

### 3. Node Affinity

* Uses **nodeSelectorTerms** with matchExpressions.
* Example: Pod runs only on nodes with label `disktype=ssd`.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: node-affinity-pod
spec:
  containers:
  - name: nginx
    image: nginx
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: disktype
            operator: In
            values:
            - ssd
```

---

### 4. Pod Affinity

* Places a pod **on the same node (or topology domain)** as another pod with specific labels.
* Example: Schedule frontend pod on the same node as backend pod.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-affinity-pod
spec:
  containers:
  - name: nginx
    image: nginx
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: app
            operator: In
            values:
            - backend
        topologyKey: "kubernetes.io/hostname"
```

✅ Here:

* `labelSelector` → finds backend pods.
* `topologyKey` → tells Kubernetes to match on node (`hostname`).

---

### 5. Pod Anti-Affinity

* Ensures pods **don’t run on the same node**.
* Example: Spread multiple replicas of a pod across different nodes.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-anti-affinity-pod
spec:
  containers:
  - name: nginx
    image: nginx
  affinity:
    podAntiAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: app
            operator: In
            values:
            - frontend
        topologyKey: "kubernetes.io/hostname"
```

✅ Here:

* Ensures that if one pod with label `app=frontend` is on a node, another `frontend` pod won’t be scheduled on the same node.

---

### 6. Real World Uses

* **Node Affinity**: Database pod runs only on high-performance SSD nodes.
* **Pod Affinity**: Cache pod runs close to the app pod for faster communication.
* **Pod Anti-Affinity**: Replicas of backend pods spread across nodes → no single node failure brings down all replicas.

---

### 7. Key Points

* **requiredDuringSchedulingIgnoredDuringExecution** → strict rules (must follow).
* **preferredDuringSchedulingIgnoredDuringExecution** → soft rules (best effort).
* **topologyKey** can be:

  * `kubernetes.io/hostname` → per node.
  * `failure-domain.beta.kubernetes.io/zone` → per zone (multi-AZ cluster).

