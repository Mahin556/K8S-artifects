
### 1. What is a Kubernetes Secret?

* A **Secret** stores sensitive information such as:

  * Passwords
  * API keys
  * TLS certificates
  * Docker registry credentials
* Unlike ConfigMaps, Secrets are **base64-encoded** for safer handling.

---

### 2. Types of Secrets

From the screenshot, `kubectl create secret` supports:

* **generic** → Create from literal values, files, or directories.
* **docker-registry** → Store credentials for pulling private images.
* **tls** → Store TLS certificates (cert + private key).

---

### 3. Creating a Generic Secret

* **From literal values**:

  ```bash
  kubectl create secret generic mysecret \
    --from-literal=username=admin \
    --from-literal=password=12345
  ```

* **From a file**:

  ```bash
  kubectl create secret generic mysecret \
    --from-file=./username.txt \
    --from-file=./password.txt
  ```

* **From a directory** (each file becomes a key):

  ```bash
  kubectl create secret generic mysecret --from-file=./secrets/
  ```

---

### 4. Viewing the Secret

* List all secrets:

  ```bash
  kubectl get secrets
  ```
* Describe secret (metadata only, values are hidden):

  ```bash
  kubectl describe secret mysecret
  ```
* View decoded value:

  ```bash
  kubectl get secret mysecret -o jsonpath='{.data.username}' | base64 --decode
  ```

---

### 5. Using Secret in a Pod

* Mount as environment variable:

  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: secret-pod
  spec:
    containers:
    - name: mycontainer
      image: nginx
      env:
      - name: USER_NAME
        valueFrom:
          secretKeyRef:
            name: mysecret
            key: username
  ```

* Mount as volume:

  ```yaml
  volumes:
  - name: secret-volume
    secret:
      secretName: mysecret
  ```
