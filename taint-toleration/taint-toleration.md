

## 🌐 What are Taints and Tolerations?

* **Taints** are applied on **Nodes**. They tell Kubernetes: *“Don’t schedule Pods here unless they can tolerate this taint.”*
* **Tolerations** are applied on **Pods**. They tell Kubernetes: *“I can tolerate this taint, so I’m allowed to run on such nodes.”*

🔑 Together, taints and tolerations **control which Pods can run on which Nodes**.

---

![Alt Text](https://github.com/Mahin556/K8S-artifects/tree/main/images/taint-toleration1.png)

---

## 🖥️ Why are Taints & Tolerations Needed?

* To **dedicate specific nodes** for certain workloads (e.g., GPU workloads, monitoring agents).
* To **prevent workloads from being scheduled** on sensitive nodes (e.g., master/control-plane nodes).
* To **isolate environments** (e.g., production vs dev workloads).
* To **handle special hardware** (e.g., only GPU Pods should run on GPU nodes).

---

* **Taint Structure**
  • A taint has three parts:

  ```
  key=value:effect
  ```

  • **Key** → Identifier (e.g., `dedicated`)
  • **Value** → Optional string (e.g., `gpu`)
  • **Effect** → Defines how the taint affects scheduling.

---

* **Effects of Taints**
  • `NoSchedule` → Pods without matching toleration will **not** be scheduled on the node.
  • `PreferNoSchedule` → Scheduler will **try to avoid** placing Pods without toleration on the node, but it’s not strict.
  • `NoExecute` → New Pods without toleration are not scheduled, and existing Pods without toleration are **evicted**.

---

* **Toleration Structure**
  • A toleration looks like this:

  ```yaml
  tolerations:
  - key: "dedicated"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
  ```

  • Fields:

  * **key** → Must match taint key.
  * **operator** → Can be `Equal` (default) or `Exists`.
  * **value** → Compared to taint value (required with `Equal`).
  * **effect** → Must match taint effect.
  * **tolerationSeconds** → Used with `NoExecute` effect, specifies how long the Pod can remain before eviction.

---

* **How They Work Together**
  • If a node has a taint and a Pod has the corresponding toleration, then the Pod **may** be scheduled on that node.
  • Toleration does not guarantee scheduling; it only allows it. The scheduler still considers other constraints (resources, affinity, etc.).
  • If a Pod has no matching toleration, it will not land on that tainted node (depending on effect).

---

* **Examples**

  1. **Apply a Taint to a Node**

     ```bash
     kubectl taint nodes node1 dedicated=gpu:NoSchedule
     ```

     This means only Pods tolerating `dedicated=gpu:NoSchedule` can be scheduled on `node1`.

  2. **Pod with Toleration**

     ```yaml
     apiVersion: v1
     kind: Pod
     metadata:
       name: gpu-pod
     spec:
       tolerations:
       - key: "dedicated"
         operator: "Equal"
         value: "gpu"
         effect: "NoSchedule"
       containers:
       - name: nginx
         image: nginx
     ```

  3. **Toleration with `Exists` Operator**

     ```yaml
     tolerations:
     - key: "dedicated"
       operator: "Exists"
       effect: "NoSchedule"
     ```

     This tolerates any value for `dedicated` key.

  4. **Eviction with `NoExecute`**

     ```bash
     kubectl taint nodes node1 special=true:NoExecute
     ```

     Pods without matching toleration will be evicted immediately.

     Pod toleration example:

     ```yaml
     tolerations:
     - key: "special"
       operator: "Equal"
       value: "true"
       effect: "NoExecute"
       tolerationSeconds: 3600
     ```

     → This Pod can stay on the node for **1 hour** before eviction.

---

* **Use Cases**
  • **Dedicated Nodes** → Example: GPU nodes, database nodes, logging nodes.
  • **Special Hardware** → Ensuring only Pods requiring that hardware run there.
  • **Node Maintenance** → Temporarily taint nodes to prevent new Pods from scheduling.
  • **Isolating Workloads** → Run production and development workloads separately.

---

## ⚙️ Adding & Removing Taints

* Add a taint:

  ```sh
  kubectl taint nodes <node-name> key=value:NoSchedule
  ```

  Example:

  ```sh
  kubectl taint nodes node1 gpu=true:NoSchedule
  ```

  → Only Pods with toleration `gpu=true` can run on `node1`.

* Remove a taint:

  ```sh
  kubectl taint nodes <node-name> key=value:NoSchedule-
  ```

---

## 🚀 Example: Taint & Toleration in Action

### Step 1: Taint a node

```sh
kubectl taint nodes worker1 dedicated=database:NoSchedule
```

→ Worker1 is reserved for database Pods only.

### Step 2: Pod without toleration

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-pod
spec:
  containers:
  - name: nginx
    image: nginx
```

→ This Pod will **not** run on `worker1`.

### Step 3: Pod with toleration

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: db-pod
spec:
  tolerations:
  - key: "dedicated"
    operator: "Equal"
    value: "database"
    effect: "NoSchedule"
  containers:
  - name: mysql
    image: mysql
```

→ This Pod **will** be scheduled on `worker1`.

---

## 🧹 Example with **NoExecute** (Evicting Pods)

1. Taint a node:

   ```sh
   kubectl taint nodes worker2 critical=true:NoExecute
   ```

2. Any Pod **without toleration** for `critical=true:NoExecute` will be **evicted immediately**.

3. Pod with toleration:

   ```yaml
   tolerations:
   - key: "critical"
     operator: "Equal"
     value: "true"
     effect: "NoExecute"
     tolerationSeconds: 60
   ```

   → Pod will **stay for 60s** before eviction.

---

## 📊 Real-Life Use Cases

1. **Dedicated Nodes**:
   Reserve some nodes for special workloads (e.g., GPU for ML jobs).

   ```sh
   kubectl taint nodes gpu-node gpu=true:NoSchedule
   ```

   Pods must have toleration to use GPU node.

2. **Critical System Pods**:
   Control plane nodes are tainted with:

   ```
   node-role.kubernetes.io/control-plane=:NoSchedule
   ```

   → Normal Pods won’t run here, only toleration-enabled system Pods.

3. **Evicting Pods**:
   Use `NoExecute` for nodes that go unhealthy to evict Pods gracefully.


* If pod not able to schedule on node(taint prevent it), we can see it in `kubectl describe <pod>`.


```bash
[vagrant@vbox ~]$ kubectl describe node mycluster1-control-plane | grep -i taints

Taints:             node-role.kubernetes.io/control-plane:NoSchedule
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: nginx  
  name: nginx   
spec:
  containers:   
  - image: nginx
    name: nginx 
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
  nodeName: "mycluster1-control-plane"
  tolerations:
  - key: "node-role.kubernetes.io/control-plane"
    operator: "Exists"
    effect: "NoSchedule"

status: {}
```