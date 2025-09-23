
# **Kubernetes Pods – Key Points (In a Nutshell)**

1. **Smallest Deployable Unit**

   * Pods are the **atomic unit of deployment** in Kubernetes.
   * They represent one or more containers running together on a node.

2. **Ephemeral Nature**

   * Pods are **temporary**; they can be created, deleted, or replaced anytime.
   * Controllers like **Deployments** or **ReplicaSets** manage their lifecycle for stability.

3. **Multiple Containers**

   * A Pod can contain **one or more containers**.
   * There is **no strict limit**, but typically used for **tightly coupled containers** (e.g., main + sidecar).

4. **Networking**

   * Each Pod gets a **unique IP address** in the cluster.
   * Pods communicate with each other using **Pod IP**.
   * Containers inside a Pod communicate via **localhost** on different ports.
   * Ensure each container uses **different ports** to avoid conflicts.

5. **Resource Management**

   * You can specify **CPU and memory limits/requests** for each container.

6. **Shared Storage**

   * Containers can **share the same volumes** by mounting them into each container.

7. **Node Scheduling**

   * All containers of a Pod are scheduled on the **same node**.
   * Pods **cannot span multiple nodes**.

8. **Startup Order**

   * Regular containers **start simultaneously** (no guaranteed order).
   * **Init containers** run **sequentially** before main containers start.

---

✅ **Summary Diagram (Conceptual)**

```
Pod (1 IP) ──────────────┐
  ├─ Container A (localhost:8080)
  ├─ Container B (localhost:9090)
  └─ Container C (localhost:7070)
Shared Volume ────────────┘
All containers share: Network, IPC, UTS
Not shared: PID (optional), root filesystem
```

