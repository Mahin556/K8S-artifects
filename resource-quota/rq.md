# **ResourceQuota in Kubernetes**

A **ResourceQuota** restricts the resource usage in a **namespace**.
It can limit:

1. **Object count quotas** (e.g., number of Pods, Services, ConfigMaps).
2. **Compute resource quotas** (e.g., CPU, memory requests/limits).

---

## **Example from your screenshot**

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: myquota
spec:
  hard:
    pods: "10"                # Object-based quota (max 10 Pods in namespace)
    requests.cpu: "0.5"       # Total CPU requests allowed (500 millicores)
    requests.memory: 500Mi    # Total memory requests allowed
    limits.cpu: "1"           # Total CPU limits allowed (1 core)
    limits.memory: 1Gi        # Total memory limits allowed
```

---

## **Explanation**

* **pods: 10**
  Max 10 Pods can exist in this namespace.

* **requests.cpu: 0.5**
  All Pods combined can request only **0.5 CPU** (500 millicores).

* **requests.memory: 500Mi**
  Total memory requests for all Pods ≤ **500MiB**.

* **limits.cpu: 1**
  Total CPU limit for all Pods ≤ **1 CPU core**.

* **limits.memory: 1Gi**
  Total memory limit for all Pods ≤ **1 GiB**.

---

## **Key Notes**

* You can define **only object quotas**, **only compute quotas**, or **both**.
* ResourceQuota applies **per namespace**, not cluster-wide.
* ConfigMap, Secrets, PersistentVolumeClaims can also be restricted.
* The variable names (like `requests.cpu`, `limits.memory`) **must match Kubernetes’ expected keys**.

---

## **Other Examples**

### 1. **Limit ConfigMaps and PVCs**

```yaml
spec:
  hard:
    configmaps: "20"
    persistentvolumeclaims: "5"
```

### 2. **Restrict LoadBalancers**

```yaml
spec:
  hard:
    services.loadbalancers: "2"
```

### 3. **Restrict Secrets**

```yaml
spec:
  hard:
    secrets: "50"
```
