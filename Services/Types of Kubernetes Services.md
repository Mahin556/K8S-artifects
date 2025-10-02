## Types of Kubernetes Services

### ClusterIP (default)

* Exposes the service **only within the cluster**.
* Pods in the same cluster can access it, but **not from outside**.

Example:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: clusterip-service
spec:
  type: ClusterIP #Optional
  selector:
    app: myapp
  ports:
    - port: 8080
      targetPort: 8080
```

Access: `curl http://clusterip-service:8080` (inside cluster only).

---

### NodePort

* Exposes the service on **each node’s IP + static port (30000–32767 range)**.
* Accessible from outside the cluster using `NodeIP:NodePort`.
* A **ClusterIP service is created automatically** in the background.

Example:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nodeport-service
spec:
  type: NodePort
  selector:
    app: myapp
  ports:
    - port: 8080       # Service port
      targetPort: 8080 # Pod port
      nodePort: 31999  # Fixed external port
```

Access: `http://<NodeIP>:31999`

💡 Use Case: Quick external access in dev/test clusters.

---

### LoadBalancer

* Available on **cloud providers** (AWS, GCP, Azure, etc).
* Operates at transport layer (TCP).
* Integrates with cloud provider load balancers.
* Provisions a cloud load balancer → routes to NodePort → routes to ClusterIP → routes to pods.
* Ingress is a more advanced alternative at application layer (HTTP/HTTPS) for smarter routing.

Example:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: loadbalancer-service
spec:
  type: LoadBalancer
  selector:
    app: myapp
  ports:
    - port: 80
      targetPort: 8080
```

Access: External IP allocated by cloud load balancer.

💡 Use Case: Production workloads needing stable external access.

---

### ExternalName

* Maps a service to an **external DNS name**.
* No selector, no endpoints.

Example:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: external-db
spec:
  type: ExternalName
  externalName: db.example.com
  ports:
  - port: 3306
```

Pods can now connect to `external-db` and it resolves to `db.example.com`.

---

## 6. Example: Full NodePort Service YAML

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
  labels:
    k8s-app: myapp
spec:
  type: NodePort
  selector:
    k8s-app: myapp
    component: nginx
    env: production
  ports:
    - name: web
      protocol: TCP
      port: 8080        # Service port
      targetPort: 80    # Pod container port
      nodePort: 31999   # Fixed NodePort
```

---

## 7. How Services Work Internally

* Pods get **labels** (e.g., `app=myapp`).
* Service uses **selectors** to find matching pods.
* Kubernetes creates **Endpoints object** with IPs of those pods.
* Service forwards traffic → kube-proxy (on each node) sets up iptables/IPVS rules → traffic goes to pod.
* Load balancing: traffic is distributed across pods automatically.

---

## 8. Best Practices

* Always use **labels + selectors** consistently.
* For multiple ports, always **name ports**.
* Use **ClusterIP** for internal communication, **NodePort/LoadBalancer/Ingress** for external.
* In production, prefer **LoadBalancer + Ingress** for external access instead of raw NodePorts.
* Monitor services with:

  ```bash
  kubectl get svc
  kubectl describe svc <service-name>
  ```

### References:
- https://www.tutorialspoint.com/kubernetes/kubernetes_service.htm