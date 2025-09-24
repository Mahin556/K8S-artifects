## **1. Why Delete Pods from a Node?**

Deleting pods from a node is commonly needed for:

* **Troubleshooting:** Free up a node to test scheduling or resource issues.
* **Maintenance:** Clear a node before upgrades or repairs.
* **Manual scaling:** Redistribute workloads to other nodes.

---

## **2. Delete All Pods From a Node**

### **Step 1: List Nodes and Pods**

```bash
# Get nodes with details
kubectl get nodes -o wide

# Get pods with node assignments
kubectl get pods -o wide
```

### **Step 2: Drain the Node**

The `kubectl drain` command safely evicts pods from a node and reschedules them on other nodes.

```bash
kubectl drain <node-name>
```

#### **Handling Errors**

* DaemonSet-managed pods:

```bash
--ignore-daemonsets
```

* Pods using local storage (emptyDir):

```bash
--delete-emptydir-data
```

**Example:**

```bash
kubectl drain aks-node-001 --ignore-daemonsets --delete-emptydir-data
```

---

### **Step 3: Cordon the Node (Optional)**

Prevent new pods from scheduling on the node:

```bash
kubectl cordon <node-name>
```

### **Step 4: Uncordon the Node**

After maintenance, allow pods to be scheduled again:

```bash
kubectl uncordon <node-name>
```

Status changes from `SchedulingDisabled` → `Ready`.

---

## **3. Delete a Single Pod**

* Simple deletion:

```bash
kubectl delete pod <pod-name>
```

* Force immediate deletion:

```bash
kubectl delete pod <pod-name> --force --grace-period=0
```

* If the pod is stuck:

```bash
kubectl patch pod <pod-name> -p '{"metadata":{"finalizers":null}}'
```

> Tip: To ensure the pod doesn’t return to the same node, cordon the node first.

---

## **4. Handle Pod Disruption Budgets (PDB)**

* Error: `Cannot evict pod as it would violate the pod’s disruption budget`.
* PDB ensures minimum availability of pods during voluntary disruptions.

```bash
# List all PDBs
kubectl get poddisruptionbudget -A

# Delete a PDB if necessary
kubectl delete poddisruptionbudget <pdb-name>
```

---

## **5. Scaling Pods Before Deletion**

* Maintain application availability:

```bash
# Scale up before deletion
kubectl scale deployment <deployment-name> --replicas=<new-number>

# Delete pod
kubectl delete pod <pod-name>

# Scale back down
kubectl scale deployment <deployment-name> --replicas=<original-number>
```

---

## **6. Delete Completed Pods**

* Pods with `Succeeded` or `Failed` status can be cleaned up:

```bash
# List completed pods in a namespace
kubectl get pods --namespace <ns> --field-selector=status.phase=Succeeded,status.phase=Failed

# Delete them
kubectl delete pods --namespace <ns> --field-selector=status.phase=Succeeded,status.phase=Failed
```

* For all namespaces:

```bash
kubectl delete pods --all-namespaces --field-selector=status.phase=Succeeded,status.phase=Failed
```

> Tip: Use `--dry-run=client -o yaml` to preview before deleting in bulk.

---

## **Key Points**

* **`kubectl delete pod`** removes pods but may trigger recreation if managed by a Deployment or StatefulSet.
* **`kubectl drain`** is safer for nodes—evicts pods while preserving service availability.
* Consider **Pod Disruption Budgets** and **scaling strategies** to prevent downtime.
* Force deletion is a last resort, primarily for stuck pods.

### References:
- https://spacelift.io/blog/kubectl-delete-pod