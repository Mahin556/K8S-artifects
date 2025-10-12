### 🔐 **1. Encrypt Secrets at Rest**

* By default, Kubernetes stores Secrets **unencrypted in etcd**.
* Enable **encryption at rest** using the Kubernetes API server configuration (`EncryptionConfiguration`).
* This ensures even if etcd is compromised, the secrets remain unreadable.

---

### 👮 **2. Apply Least-Privilege Access (RBAC)**

* Use **Role-Based Access Control (RBAC)** to limit access to Secrets.
* Only allow specific users, pods, or namespaces to access certain Secrets.
* Example:
  Create roles that restrict who can `get`, `list`, or `update` Secrets.

---

### 🧩 **3. Restrict Secret Access to Specific Pods**

* Mount only the **required Secrets** into specific containers or namespaces.
* Never expose Secrets as environment variables unless necessary (as they can appear in logs or crash dumps).
* Use volume mounts for better security and isolation.

---

### ☁️ **4. Use External Secret Stores**

* Integrate with external providers like:

  * **AWS Secrets Manager**
  * **HashiCorp Vault**
  * **Google Secret Manager**
  * **Azure Key Vault**
* Use tools like the **Kubernetes External Secrets Operator (ESO)** to sync secrets securely from these stores.

---

### 🧠 **5. Avoid Storing Secrets in Git Repositories**

* Never commit plain-text secrets to source control.
* Use tools like:

  * **Sealed Secrets**
  * **SOPS**
  * **External Secrets Operator**
  * **GitOps workflows with encrypted values**

---

### 🕵️ **6. Audit and Monitor Secret Usage**

* Enable **audit logging** for access to Secrets.
* Use monitoring tools (e.g., Falco, Kyverno) to detect unauthorized access or changes.
* Periodically **rotate secrets** and **review permissions**.

---

### 🧰 **7. Automate and Manage with GitOps + Spacelift**

* Use **Spacelift** or similar GitOps tools for:

  * Version-controlled deployments
  * Policy enforcement (security/compliance checks)
  * Approval workflows for changes
* Policies (like **plan** and **approval** policies) help maintain compliance and prevent misconfigurations.

---

### ✅ **Summary Table**

| Practice                            | Purpose                                           |
| ----------------------------------- | ------------------------------------------------- |
| **Encrypt Secrets at Rest**         | Protects secrets in etcd from unauthorized access |
| **Use RBAC Controls**               | Limits who and what can access secrets            |
| **Mount Only Needed Secrets**       | Reduces exposure of sensitive data                |
| **Use External Secret Stores**      | Centralized, secure secret management             |
| **Avoid Secrets in Git**            | Prevents accidental leaks                         |
| **Audit and Rotate Regularly**      | Detects misuse and maintains freshness            |
| **Use GitOps Tools like Spacelift** | Ensures secure, compliant automation              |


### References:
- https://spacelift.io/blog/kubernetes-secrets