# 🔹 What is a ConfigMap?

* A **ConfigMap** is a Kubernetes API object used to store **non-confidential configuration data** in key-value pairs.
* ConfigMaps allow you to keep config values separate(decouple) from your code and container images. 
  * Externalize environment-specific settings, such as a database hostname, URLs, settings or IP address, without modifying the container image.plane-text in etcd if anyone gain access to etcd they see info in plane text.
  * make application portable and easy to manage accross different environment.
* Not used to store sensitive data  because it store data in 
* Often used alongside **Secrets** (which are for sensitive data).
* Built-in Kubernetes API
* `Non-sensitive` key-value config data. 
* Values can be `strings` or `Base64-encoded binary data`.
* Save on `cluster(etcd)`
* Injected as as:
  * `environment variables`
  * `command line arguments`
  * `filesystem volumes(mount file)`
  <br>
* Application can take value from the Environment variable and from the file.
* Prevent image rebuilding.
* Non-Sensitive data
* Store relatively small amounts of simple data.
* The total size of a ConfigMap object must be less than `1 MiB`. To store more data we should split your configuration into multiple `ConfigMaps` or consider using a `separate database` or `key-value store`.
* For example, a system that connects to a database can retrieve the database server address from a ConfigMap instead of hardcoding it. 
* Application must be designed to use the configuration from the environment variable and file.
* We can directly change the data from the `etcd` but in real world we change it using Kubernetes API server and Kubectl to create and manage your ConfigMaps.
config map ---> `DATABASE_HOST`
secret ---> `DATABASE_PASSWORD`
Supports both string data and binary data.
Can be consumed by Pods via:
  Environment variables
  Command-line arguments
  Volume mounts

---

# 🔹 Ways to Create a ConfigMap

### 1. From **Literal Values**

```bash
kubectl create configmap my-config \
  --from-literal=APP_MODE=production \
  --from-literal=APP_DEBUG=false
```

### 2. From a **File**

* app-config.yaml
```yaml
app:
  name: demo-application
  environment: development
  debug: "true"
  version: "1.0.0"

database:
  host: db-service
  port: 5432
  user: demo-user
  password: change-me
```

```bash
kubectl create configmap app-config --from-file=app-config.yaml
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: configmap-demo
spec:
  containers:
    - name: demo-container
      image: nginx:latest
      volumeMounts:
        - name: config-volume
          mountPath: /etc/app-config   # directory inside the container
          readOnly: true               # ConfigMap volumes should be read-only
  volumes:
    - name: config-volume
      configMap:
        name: app-config             # Name of the existing ConfigMap
        items:
          - key: app-config.yaml     # Key in the ConfigMap
            path: app-config.yaml   # File path inside /etc/app-config
```
```bash
kubectl apply -f pod-configmap-demo.yaml
kubectl get pods configmap-demo
kubectl describe pod configmap-demo
kubectl exec -it configmap-demo -- cat /etc/app-config/app-config.yaml
```

👉 Each file becomes a key, file content becomes the value.

### 3. From a **Directory**

```bash
kubectl create configmap my-config-dir \
  --from-file=config/
kubectl get configmaps
kubectl describe configmap app-config 
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
- Keys can only contain alphanumeric characters and the `.`, `-`, and `_` symbols.
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: demo-config
data:
  database_host: "192.168.0.1"
  debug_mode: "1"
  log_level: "verbose"
```

### 5. Update configmap value from imparative command
```bash
kubectl create configmap demo1 --from-literal=app=demo1 --from-literal=env=prod -o yaml --dry-run=client | kubectl apply -f -

kubectl edit configmap my-configmap
```

### 6. ConfigMap with both data and binaryData
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: demo-config
data:
  app_name: "demo-app"
  environment: "production"
binaryData:
  file_template: RGVtbwo=    # Base64 for "Demo\n"
```
**data** ---> Stores key-value pairs as plain text strings, uses for configs, env vars, YAML, JSON, INI, etc. (plain text).
**binaryData** ---> Stores Base64-encoded binary values (e.g., compiled files, templates, images) uses for certificates, images, or other binary content.

* You can decode the binaryData manually:
```bash
echo "RGVtbwo=" | base64 --decode #Demo
```

* You can also create a ConfigMap with both types programmatically:
```bash
kubectl create configmap demo-config \
  --from-literal=app_name=demo-app \
  --from-literal=environment=production \
  --from-file=file_template=./template.bin
```

* Pod Environment Variables
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: demo-pod-env
spec:
  containers:
    - name: demo-container
      image: busybox
      command: [ "sh", "-c", "env && sleep 3600" ]
      envFrom:
        - configMapRef:
            name: demo-config
```
```bash
kubectl log demo-pod-env
```
Output:
```bash
APP_NAME=demo-app
ENVIRONMENT=production
```
`binaryData` (like `file_template`) won’t appear as an environment variable — only data keys can.

* Pod As Mounted Files (volume)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: demo-pod-volume
spec:
  containers:
    - name: demo-container
      image: busybox
      command: [ "sh", "-c", "ls /config && cat /config/file_template && sleep 3600" ]
      volumeMounts:
        - name: config-volume
          mountPath: /config
  volumes:
    - name: config-volume
      configMap:
        name: demo-config
```
```bash
/config/app_name          # contains "demo-app"
/config/environment       # contains "production"
/config/file_template     # contains decoded "Demo\n"
```

```bash
$ kubectl get configmaps
NAME               DATA   AGE
demo-config        3      83m
```

```bash
$ kubectl describe configmap demo-config
Name:         demo-config
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
database_host:
----
192.168.0.1
debug_mode:
----
1
log_level:
----
verbose

BinaryData
====

Events:  <none>
```
* Getting a ConfigMap’s Content as JSON
```bash
controlplane:~$ kubectl get configmap demo-config -o jsonpath='{.data}'|jq
{
  "app_name": "demo-app",
  "environment": "production"
}
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


### Readonly file system
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: demo-config
data:
  database_host: "192.168.0.1"
  debug_mode: "true"
  log_level: "verbose"
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: demo-pod
spec:
  containers:
    - name: app
      image: busybox:latest
      command: ["sh", "-c", "echo 'Files from ConfigMap:' && ls -l /etc/app-config && echo && cat /etc/app-config/log_level && sleep 3600"]
      volumeMounts:
        - name: config
          mountPath: "/etc/app-config"
          readOnly: true #Prevents modification from inside the container
  volumes:
    - name: config
      configMap:
        name: demo-config
```

```bash
controlplane:~$ kubectl exec -it demo-pod -- sh  
/ # cat /etc/app-config/log_level
/ # echo Hello > /etc/app-config/log_level
sh: can't create /etc/app-config/log_level: Read-only file system
/ #
```

### `data` Field
* Stores string data only (UTF-8 encoded).
* Keys represent filenames (when mounted as a volume) or variable names (when used as env vars).
* Values are strings — multi-line values are allowed using YAML block literals.
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  log_level: "debug"
  database_host: "db.example.com"
  multi_line_config: |
    line1
    line2
    line3
```

### `binaryData` Field
* Stores binary data encoded as base64.
* Each key is the file name; the value is base64-encoded content.
* Kubernetes decodes the data automatically when mounted as a volume.

* Why binaryData?
  * Some apps require non-text files like certificates, keys, images, or compiled files.
  * `data` cannot store these because it expects UTF-8 strings.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: binary-config
binaryData:
  tls.crt: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCg==  # base64-encoded certificate
  tls.key: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQo=  # base64-encoded private key
```
* Keys in binaryData must be unique, cannot overlap with data.
* Ideal for small binaries (<1 MiB due to ConfigMap size limits).

* You can have both in a single ConfigMap, e.g.:
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mixed-config
data:
  app_name: "my-app"
binaryData:
  config.bin: U29tZSBiaW5hcnkgY29uZmlnCg==

---

apiVersion: v1
kind: Pod
metadata:
  name: configmap-demo
spec:
  containers:
    - name: demo-container
      image: busybox
      command: ["sh", "-c", "ls /config && cat /config/app_name && cat /config/config.bin"]
      env:
      - name: app_name
        valueFrom:
          configMapKeyRef:
            name: mixed-config
            key: app_name
      volumeMounts:
        - name: config-volume
          mountPath: /config
  volumes:
    - name: config-volume
      configMap:
        name: mixed-config
```
* When mounted as a volume:
  * app_name → /config/app_name
  * config.bin → /config/config.bin (decoded automatically)



### References:
- https://spacelift.io/blog/kubernetes-configmap
- https://www.tutorialspoint.com/kubernetes/kubernetes_monitoring.htm
- https://www.geeksforgeeks.org/devops/kubernetes-configmap/