# 🔹 What is a ConfigMap?

* A **ConfigMap** is a Kubernetes API object used to store **non-confidential configuration data** in key-value pairs.
* Lets you decouple configuration from container images → so you can change config without rebuilding the image.
* Often used alongside **Secrets** (which are for sensitive data).

---

# 🔹 Ways to Create a ConfigMap

### 1. From **Literal Values**

```bash
kubectl create configmap my-config \
  --from-literal=APP_MODE=production \
  --from-literal=APP_DEBUG=false
```

### 2. From a **File**

```bash
kubectl create configmap my-config --from-file=app.properties
```

👉 Each file becomes a key, file content becomes the value.

### 3. From a **Directory**

```bash
kubectl create configmap my-config --from-file=./config-dir/
```

### 4. From a **YAML Manifest**

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-config
data:
  APP_MODE: "production"
  APP_DEBUG: "false"
```

---

# 🔹 ConfigMap Structure

A ConfigMap can hold:

* **data** → key-value pairs (user-supplied)
* **binaryData** → base64-encoded binary files (images, certs, etc.)

Example:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: example-config
data:
  log_level: "debug"
  db_url: "mysql://db:3306"
binaryData:
  cert.pem: "bXktY2VydC1kYXRhCg=="   # base64 encoded
```

---

# 🔹 Using ConfigMap in Pods

## 1. **As Environment Variables**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: mycontainer
    image: busybox
    envFrom:
    - configMapRef:
        name: my-config
```

👉 Injects **all keys** as environment variables.

Or inject specific keys:

```yaml
env:
- name: APP_MODE
  valueFrom:
    configMapKeyRef:
      name: my-config
      key: APP_MODE
```

---

## 2. **As Command-line Arguments**

```yaml
args: ["--mode=$(APP_MODE)"]
```

---

## 3. **As Files via Volumes**

```yaml
volumes:
- name: config-volume
  configMap:
    name: my-config
containers:
- name: app
  image: nginx
  volumeMounts:
  - name: config-volume
    mountPath: /etc/config
```

👉 Keys become files, values become file contents:

```
/etc/config/APP_MODE → production
/etc/config/APP_DEBUG → false
```

---

# 🔹 Updating a ConfigMap

* ConfigMaps are **mutable**.
* If you update the ConfigMap:

  * **Environment variable method** → Pod restart required to reflect changes.
  * **Volume mount method** → File content updates automatically (within ~1 minute).

---

# 🔹 Viewing & Editing ConfigMaps

```bash
kubectl get configmap
kubectl describe configmap my-config
kubectl edit configmap my-config
kubectl delete configmap my-config
kubectl exec -it <pod> -- printenv FIRST_NAME
```

---

# 🔹 ConfigMap vs Secret

| Feature            | ConfigMap                | Secret                             |
| ------------------ | ------------------------ | ---------------------------------- |
| Stores             | Non-sensitive config     | Sensitive info (passwords, tokens) |
| Encoding           | Plaintext                | Base64 encoded                     |
| Encryption at rest | ❌ (unless KMS enabled)   | ✅ (by default with KMS)            |
| Typical usage      | App configs, URLs, flags | Passwords, API keys, certs         |

---

# 🔹 Best Practices

* ✅ Use **ConfigMap for non-sensitive data**, Secrets for sensitive.
* ✅ Use `envFrom` for simple apps, **volumes** for complex configs.
* ✅ Watch out: Environment variables **don’t auto-update** when ConfigMap changes.
* ✅ Use ConfigMaps with **Deployments** to avoid hardcoding configs.
* ✅ Version your ConfigMaps (e.g., `app-config-v1`, `app-config-v2`) for controlled rollouts.
* ✅ Mount config files instead of putting huge configs as env vars.

---

# 🔹 Advanced Usage

* **Immutable ConfigMaps**: Prevent accidental updates:

  ```yaml
  apiVersion: v1
  kind: ConfigMap
  metadata:
    name: my-config
  immutable: true
  data:
    key: value
  ```
* **Reference in Deployment** (auto redeploy if ConfigMap changes, using `checksum/config` annotation):

  ```yaml
  spec:
    template:
      metadata:
        annotations:
          checksum/config: "{{ .Values.configMap | sha256sum }}"
  ```
