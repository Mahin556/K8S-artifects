
### 1. What is a Kubernetes Secret?

* A Kubernetes Secret is an object to store sensitive data, like passwords, API tokens, Docker registry credentials, SSH keys, or certificates.
* Stored in base64-encoded form (not encrypted by default)in rest(etcd).
* Can be made more secure with Encryption at Rest or external vault integration (e.g., HashiCorp Vault, AWS KMS).
---

### 2. Types of Secrets

From the screenshot, `kubectl create secret` supports:

* **Opaque**(default)
* **generic** → Create from literal values, files, or directories.
* **docker-registry** → Store credentials for pulling private images.
* **tls** → Store TLS certificates (cert + private key).
* Custom types

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

  kubectl create secret generic tls-secret \
  --from-file=tls.crt=./mycert.crt \
  --from-file=tls.key=./mykey.key
  ```

* **From a directory** (each file becomes a key):

  ```bash
  kubectl create secret generic mysecret --from-file=./secrets/
  ```

* **From YAML manifest**:
```yaml
  apiVersion: v1
  kind: Secret
  metadata:
    name: my-manual-secret
  type: Opaque
  data:
    username: YWRtaW4=          # base64 of 'admin'
    password: c2VjcmV0cGFzcw==  # base64 of 'secretpass'
```
```bash
  # Encode
  echo "test@123" | base64
  # Decode
  echo "dHJhaW53aXRoc2h1YmhhbQ==" | base64 --decode
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
  name: secret-demo
spec:
  containers:
  - name: demo-container
    image: nginx:latest
    env:
    - name: DB_USERNAME
      valueFrom:
        secretKeyRef:
          name: db-secret
          key: username
    - name: DB_PASSWORD
      valueFrom:
        secretKeyRef:
          name: db-secret
          key: password
```
```bash
kubectl exec -it secret-demo -- env | grep DB_
# Output:
# DB_USERNAME=admin
# DB_PASSWORD=secretpass
```

* Mount as volume:

```yaml
volumes:
  - name: secret-volume
    secret:
      secretName: db-secret
volumeMounts:
  - name: secret-volume
    mountPath: /etc/secret
    readOnly: true

---
volumes:
  - name: config-volume
    configMap:
      name: app-config
  - name: secret-volume
    secret:
      secretName: my-secret

volumeMounts:
  - name: config-volume
    mountPath: /etc/config
  - name: secret-volume
    mountPath: /etc/secret
```

### 6.Updating a Secret
```bash
kubectl create secret generic db-secret \
  --from-literal=username=demo-user \
  --from-literal=password=newSecretPass \
  --dry-run=client -o yaml | kubectl apply -f -
```
- Pods do not automatically reload updated Secrets if consumed as env vars or command-line arguments.

##### Option 1 — Manual rolling restart:
```bash
kubectl rollout restart deployment <deployment-name>
```

##### Option 2 — Hash annotation trick:
Annotate your Pod template with a hash of the Secret:
```bash
metadata:
  annotations:
    secret-hash: "<sha256-of-secret-data>"
```
When Secret changes, hash changes → triggers new rollout automatically.

```yaml


### References:
- https://www.tutorialspoint.com/kubernetes/kubernetes_monitoring.htm
- https://muditmathur121.medium.com/mastering-configmaps-and-secrets-in-kubernetes-16df7ad514f6
- https://medium.com/@ravipatel.it/introduction-kubernetes-configmaps-and-secrets-with-example-a2987076065e