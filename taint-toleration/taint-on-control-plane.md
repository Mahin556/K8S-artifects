## 🖥️ Default Behavior of Control-Plane Nodes

* By default, **control-plane (master) nodes** are **tainted** so that **regular workloads do not run on them**.
* This is because the control-plane is meant to run critical system components (like `kube-apiserver`, `etcd`, `controller-manager`) and should not be overloaded by user workloads.

### Default Taint on Control-Plane Node

When you check taints:

```sh
kubectl describe node <control-plane-node> | grep Taints
```

You’ll usually see something like:

```
Taints: node-role.kubernetes.io/control-plane:NoSchedule
```

(or in older versions)

```
Taints: node-role.kubernetes.io/master:NoSchedule
```

This means:

* **NoSchedule** → Regular Pods cannot be scheduled here unless they have a matching toleration.

---

## 🧩 Running Pods on the Control-Plane Node

If you want to allow Pods to run on the control-plane node, you have **two options**:

### Option 1: Add Toleration to the Pod

Pod spec with toleration:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-on-control-plane
spec:
  tolerations:
  - key: "node-role.kubernetes.io/control-plane"
    operator: "Exists"
    effect: "NoSchedule"
  containers:
  - name: nginx
    image: nginx
```

* `operator: Exists` means: *as long as the taint key exists, I tolerate it.*
* This allows the Pod to be scheduled on the control-plane.

---

### Option 2: Remove the Taint (Not Recommended for Production)

If you want all Pods to be allowed on the control-plane (useful for **single-node clusters like Minikube or kubeadm lab setups**):

```sh
kubectl taint nodes <control-plane-node> node-role.kubernetes.io/control-plane:NoSchedule-
```

* The `-` at the end **removes the taint**.
* Now, workloads can run on the control-plane like a normal worker node.

⚠️ But: In production, this is **not recommended**, since user workloads might consume CPU/memory needed for critical control-plane processes.

---

## 🚦 Example Demo

1. Check the taints:

   ```sh
   kubectl describe node <control-plane-node> | grep Taints
   ```

2. Try running a Pod **without toleration**:

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

   → Pod will stay in `Pending` state, because it cannot tolerate the taint.

3. Run the same Pod **with toleration**:

   ```yaml
   tolerations:
   - key: "node-role.kubernetes.io/control-plane"
     operator: "Exists"
     effect: "NoSchedule"
   ```

   → Pod will successfully run on the control-plane node.

