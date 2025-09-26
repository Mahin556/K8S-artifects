# 🚀 **Kubernetes Init Containers**

### 🔹 What are Init Containers?

* **Init containers** are special containers in a Pod that **run before the main application containers start**.
* They always **run to completion** before the main containers are started.
* You can define **multiple init containers**, and they run sequentially in the order they are defined.
* Unlike app containers, init containers:

  * Always run **once**
  * Cannot be restarted unless the whole Pod restarts
  * Have their own image, filesystem, environment

---

### 🔹 Why Init Containers are Useful

1. **Pre-setup tasks**

   * Initialize environment before main app runs.
   * Example: Download configs, check DB availability, generate secrets.

2. **Dependency checks**

   * Ensure a dependency (like DB, API, or Service) is ready before app starts.

3. **Security**

   * Run setup tasks with elevated privileges but keep main containers restricted.

4. **Separation of concerns**

   * Keep the main container image lean while init containers handle setup logic.

---

### 🔹 Key Characteristics

* Run **sequentially** (one after another).
* Must **succeed** before main app containers start.
* If an init container **fails**, Kubernetes restarts the Pod until it succeeds.
* Each init container can have its own:

  * Image
  * Command
  * Resource limits
  * Volumes

---

### 🔹 Example: Basic Init Container

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
spec:
  initContainers:
  - name: init-myservice
    image: busybox
    command: ['sh', '-c', 'echo Waiting for service... && sleep 10']
  containers:
  - name: myapp-container
    image: nginx
    ports:
    - containerPort: 80
```

📌 Flow:

1. Init container runs → prints message + sleeps for 10s.
2. After success → main `nginx` container starts.

---

### 🔹 Example: Init Container for Dependency Check

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-with-init
spec:
  initContainers:
  - name: wait-for-db
    image: busybox
    command: ['sh', '-c', 'until nc -z my-database 5432; do echo waiting for db; sleep 5; done;']
  containers:
  - name: app-container
    image: my-app:latest
    ports:
    - containerPort: 8080
```

📌 Here:

* Init container waits for DB (`my-database:5432`) to become available.
* Once DB is ready → app container starts.

---

### 🔹 Example: Sharing Data Between Init and App Containers

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-with-shared-data
spec:
  volumes:
  - name: workdir
    emptyDir: {}
  initContainers:
  - name: init-download
    image: busybox
    command: ['sh', '-c', 'echo "Config from init container" > /work-dir/config.txt']
    volumeMounts:
    - name: workdir
      mountPath: /work-dir
  containers:
  - name: main-app
    image: busybox
    command: ['sh', '-c', 'cat /app/config.txt && sleep 3600']
    volumeMounts:
    - name: workdir
      mountPath: /app
```

📌 Here:

* Init container writes a config file into a **shared volume** (`emptyDir`).
* Main app container reads the same file.

---

### 🔹 How Init Containers Work Internally

1. Pod scheduled → init containers start **in order**.
2. Each must **exit successfully** (exit code `0`).
3. If one fails, the Pod restarts (all init containers rerun).
4. After all succeed → main containers start **in parallel**.

---

### 🔹 Real-World Use Cases

* ✅ Wait for a **database or API** before app starts.
* ✅ Set up **config files** or environment dynamically.
* ✅ Run **database migrations** before app.
* ✅ Fetch secrets from a vault and mount them into volumes.
* ✅ Perform **filesystem permissions** setup.

---

### 🔹 Monitoring Init Containers

Check pod status:

```bash
kubectl get pod myapp-pod
```

Describe pod:

```bash
kubectl describe pod myapp-pod
```

You’ll see:

* Init containers listed separately.
* Their logs accessible with:

```bash
kubectl logs myapp-pod -c <init-container-name>
```

---

### 🔹 Differences: Init Containers vs App Containers

| Feature            | Init Container                   | App Container                         |
| ------------------ | -------------------------------- | ------------------------------------- |
| **Purpose**        | Setup / pre-checks               | Run the main app                      |
| **Execution**      | Sequential, must finish first    | Run in parallel (after init finishes) |
| **Restart Policy** | Pod restarts if init fails       | Can restart individually              |
| **Lifetime**       | Short-lived (one-time execution) | Long-running                          |

---

✅ **In summary:**
Init containers are powerful for **preparing environments, checking dependencies, and initializing data** before main containers run. They enforce an order of execution, improve security, and keep main app containers clean and focused.
