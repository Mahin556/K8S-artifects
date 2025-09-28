
* A **DaemonSet** is a Kubernetes workload object that ensures a copy of a specific Pod runs on **all (or some) nodes** in a cluster.
* It is commonly used for **cluster-wide services** that need to run on every node, such as logging agents, monitoring agents, or networking daemons.
* Control plane component kube-proxy and CNI is run as daemonset.
---

### Key Features of DaemonSet

* Ensures that a Pod is scheduled on every Node (or only on nodes that match node selectors, taints, tolerations, or affinity rules).
* Automatically deploys a Pod **when a new node is added** to the cluster.
* Removes the Pod from a node when the node is deleted from the cluster.
* If a DaemonSet is deleted, the Pods it created are automatically cleaned up.

---

### Common Use Cases

* **Log Collection**: Run agents like Fluentd, Logstash, or Filebeat to collect logs from all nodes.
* **Monitoring**: Run Prometheus Node Exporter or Datadog agent on all nodes for metrics collection.
* **Networking**: Run CNI plugins or network services like Calico, Weave, or Cilium on all nodes.
* **Storage**: Run storage daemons such as Ceph or GlusterFS.

---

### How DaemonSet Works

* When you create a DaemonSet, Kubernetes scheduler ensures one Pod per node.
* Pods are evenly distributed — only one Pod per node unless configured otherwise.
* You can limit DaemonSet to specific nodes using:

  * **Node selectors**
  * **Node affinity/anti-affinity**
  * **Tolerations** for tainted nodes

---

### Example DaemonSet Manifest

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: log-agent
  namespace: kube-system
spec:
  selector:
    matchLabels:
      name: log-agent
  template:
    metadata:
      labels:
        name: log-agent
    spec:
      containers:
      - name: fluentd
        image: fluentd:latest
        resources:
          limits:
            memory: "200Mi"
            cpu: "200m"
        volumeMounts:
        - name: varlog
          mountPath: /var/log
      volumes:
      - name: varlog
        hostPath:
          path: /var/log
```

* In this example, `fluentd` is deployed on every node to collect logs from `/var/log`.

---

### Managing DaemonSets

* **Create**: `kubectl apply -f daemonset.yaml`
* **List**: `kubectl get daemonset -A`
* **Describe**: `kubectl describe daemonset <name>`
* **Delete**: `kubectl delete daemonset <name>`

---

### Updating a DaemonSet

* By default, Pods are replaced **one at a time** (rolling update).
* Strategy can be controlled with `updateStrategy`:

  * **RollingUpdate** (default): Replace Pods gradually.
  * **OnDelete**: New Pods are created only after you manually delete the old ones.

Example:

```yaml
updateStrategy:
  type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 1
```

---

### DaemonSet vs Deployment

* **Deployment**: Scales Pods arbitrarily across nodes, focuses on stateless apps.
* **DaemonSet**: Ensures **one Pod per node**, ideal for infrastructure-level services.
* **Deployment + HPA**: Used for scalable workloads (e.g., web apps).
* **DaemonSet**: Used for node-level background tasks.

### References
- https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/