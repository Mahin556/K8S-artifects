- When the container image should be pulled from the container registry (like Docker Hub or a private registry).

* `IfNotPresent`
  * Kubernetes **pulls the image only if it is not already present** on the node.
  * If the image is already cached locally on the node, it will use the local copy.
  * This is the **default policy** for images with a **specific tag** (like `nginx:1.21` or `myapp:v2`).
  * Saves time and bandwidth since it skips unnecessary pulls.
  * However, it can lead to outdated images if a newer image with the same tag exists in the registry.

  **Example:**
  ```yaml
  containers:
  - name: web
    image: nginx:1.21
    imagePullPolicy: IfNotPresent
  ```

* `Always`
  * Kubernetes **always pulls the image** from the registry **every time the Pod is started**.
  * Ensures that the latest version of the image (especially with mutable tags like `latest`) is used.
  * Slightly slower Pod startup due to the pull operation each time.
  * Useful in development or CI/CD environments.
  **Example:**
  ```yaml
  containers:
  - name: web
    image: nginx:latest
    imagePullPolicy: Always
  ```

* `Never`
  * Kubernetes **never pulls the image** from a registry.
  * The image **must already exist locally** on the node; otherwise, Pod creation fails.
  * Useful in **air-gapped** or **offline environments** where images are preloaded on all nodes.
  **Example:**
  ```yaml
  containers:
  - name: web
    image: myapp:offline
    imagePullPolicy: Never
  ```

---

### 🧠 Default Behavior Summary

| Image Tag Type                | Default `imagePullPolicy` | Behavior                         |
| ----------------------------- | ------------------------- | -------------------------------- |
| `:latest`                     | `Always`                  | Always pull the latest version   |
| Custom Tag (e.g., `:v1.0.0`)  | `IfNotPresent`            | Pull only if not already present |
| No Tag (treated as `:latest`) | `Always`                  | Always pull the latest version   |

 
```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
    resources: {}
    imagePullPolicy: IfNotPresent #Always, Never and IfNotPresent
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```
