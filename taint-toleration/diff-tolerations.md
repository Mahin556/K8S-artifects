# **Taints and Tolerations in Kubernetes**

* **Taints** are applied to **nodes**. They *repel* Pods unless the Pods have a matching toleration.
* **Tolerations** are applied to **Pods**. They let Pods be scheduled on nodes with matching taints.

This way, Kubernetes controls which Pods can (or cannot) run on specific nodes.

---

## **1. Taint Basics**

A taint has three parts:

```
key=value:effect
```

* **key** → label key
* **value** → label value
* **effect** → what happens if Pod doesn’t tolerate the taint

**Effects:**

* `NoSchedule` → Pod won’t be scheduled on node.
* `PreferNoSchedule` → Try to avoid scheduling, but not guaranteed.
* `NoExecute` → New Pods won’t schedule, and existing Pods are evicted.

---

## **2. Toleration Syntax**

```yaml
tolerations:
  - key: "mysize"
    operator: "Equal"
    value: "large"
    effect: "NoSchedule"
```

This tolerates a taint with `mysize=large:NoSchedule`.

---

## **3. If Effect is Empty**

```yaml
tolerations:
  - key: "mysize"
    operator: "Equal"
    value: "large"
    effect: ""
```

* If `effect` is empty → Pod tolerates **all effects** (`NoSchedule`, `PreferNoSchedule`, `NoExecute`).

---

## **4. Using `Exists` Operator**

```yaml
tolerations:
  - key: "mysize"
    operator: "Exists"
    effect: "NoSchedule"
```

* Pod tolerates *any value* of key `mysize` if effect is `NoSchedule`.

---

## **5. If Only `key` with `Exists`**

```yaml
tolerations:
  - key: "mysize"
    operator: "Exists"
```

* Pod tolerates **any taint** with key `mysize`, regardless of value or effect.

---

## **6. If Key is Empty with `Exists`**

```yaml
tolerations:
  - operator: "Exists"
```

* Pod tolerates **every taint on the node**.

---

## **7. NoExecute with TolerationSeconds**

```yaml
tolerations:
  - key: "mysize"
    operator: "Equal"
    value: "large"
    effect: "NoExecute"
    tolerationSeconds: 60
```

* Pod can stay on a tainted node for 60 seconds, then gets evicted.

---

## **8. Commands from Your Screenshot**

```bash
kubectl taint node worker01 mysize=large:NoSchedule
kubectl taint node worker01 mysize:NoSchedule-
```

* First adds a taint.
* Second removes the taint.

---

## **Summary of Your Notes**

* **If `effect` is empty** → tolerates all effects.
* **If only `Exists` without key** → tolerates every taint.
* **`tolerationSeconds` with `NoExecute`** → allows temporary stay before eviction.
* **kubectl taint node … key=value:effect** → add/remove taints to control scheduling.
