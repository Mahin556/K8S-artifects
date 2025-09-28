- Two ways to create resource
    1. Imperative way ( Manual approach)
    2. Declarative way ( Dynamic Approach )

---

### Imperative Way of Deploying Resources in Kubernetes

* User **specifies exact commands** to create, update, or delete resources.
* Uses commands like:

  * `kubectl create` → create resources
  * `kubectl delete` → delete resources
  * `kubectl scale` → scale replicas
  * `kubectl replace` → replace a resource
* **Characteristics**:

  * Full manual control
  * Faster for experiments or one-time tasks
  * No need for YAML files
  * Not idempotent (running the same command again may fail)

**Examples**:

```bash
kubectl create deployment demo-deployment --image=nginx:latest
kubectl create namespace production
kubectl create serviceaccount demo-user
kubectl create secret generic demo-secret --from-literal=password=12345
```

---

### Declarative Way of Deploying Resources in Kubernetes

* User defines the **desired state** in **YAML/JSON files**.
* Uses command:

  * `kubectl apply -f <file>`
* Kubernetes compares **current state vs desired state** and makes necessary changes.
* **Characteristics**:

  * Best for production and CI/CD
  * Idempotent (safe to run multiple times)
  * Tracks updates automatically
  * Enables version control of YAML files

**Example** (`deployment.yaml`):

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: demo-deployment-yaml
spec:
  replicas: 3
  selector:
    matchLabels:
      type: pods
  template:
    metadata:
      labels:
        type: pods
    spec:
      containers:
      - name: nginx-pod
        image: nginx
```

```bash
kubectl apply -f deployment.yaml
```

* If replicas are changed from 3 → 5 in the file and reapplied, Kubernetes updates automatically.

---

### Difference Between `kubectl create` and `kubectl apply`

| Feature              | **kubectl create** (Imperative)              | **kubectl apply** (Declarative)                       |
| -------------------- | -------------------------------------------- | ----------------------------------------------------- |
| **Approach**         | Imperative (explicit commands)               | Declarative (YAML manifests)                          |
| **Usage**            | Quick experiments, dev phase                 | Production deployments, CI/CD                         |
| **Idempotence**      | Not idempotent (fails on existing resources) | Idempotent (safe re-run, updates resources)           |
| **Workflow**         | One-time creation, manual control            | Continuous management, auto-updates                   |
| **Changes Handling** | Does not handle updates automatically        | Handles updates by comparing current vs desired state |

---

### When to Use What

* **Imperative (`create`)**:

  * Quick testing
  * Learning or small projects
  * One-time tasks
* **Declarative (`apply`)**:

  * Production-grade apps
  * Version-controlled deployments
  * Automation and GitOps pipelines


### References:
- https://www.geeksforgeeks.org/devops/kubernetes-kubectl-create-and-kubectl-apply/