## Mounting Persistent Volumes in Kubernetes

Applications often require storage to persist data, ensuring it remains intact even if pods are restarted. Kubernetes provides **Persistent Volumes (PV)** and **Persistent Volume Claims (PVC)** to manage this storage.

### Steps to Mount a Persistent Volume

1. **Create a Persistent Volume (PV)** – defines the actual storage.
2. **Create a Persistent Volume Claim (PVC)** – requests the storage defined in the PV.
3. **Mount the volume** in your application container at the desired directory.

---

### Example: Mounting a Persistent Volume in a Pod

```yaml
spec:
  template:
    spec:
      containers:
        - name: my-app
          image: my-app-image:latest
          volumeMounts:
            - name: test-storage
              mountPath: /data  # Directory where your app writes data

      volumes:
        - name: test-storage
          persistentVolumeClaim:
            claimName: test-pvc  # PVC that binds to your PV
```

---

### Notes:

* This approach ensures **data persistence** even when pods are restarted.
* Kubernetes also allows **mounting other resources** as volumes:

  * **Secrets** – for sensitive data like passwords or keys.
  * **ConfigMaps** – for configuration files and environment settings.

### References
- https://devopscube.com/kubernetes-deployment-tutorial/