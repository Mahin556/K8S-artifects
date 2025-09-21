Finer-grained restart control (Alpha)
For scenarios where different containers within a single pod require different restart policies, a new alpha feature called "Container Restart Rules" was introduced in Kubernetes v1.34. 
This feature allows for specifying a restartPolicy and restartPolicyRules on individual containers, overriding the pod's global restartPolicy.
This advanced capability is currently only available for standalone pods and not for those managed by controllers like Deployments or ReplicaSets. 

### References
- https://kubernetes.io/blog/2025/08/29/kubernetes-v1-34-per-container-restart-policy *