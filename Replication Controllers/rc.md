- Manages a replicas of pod
- Auto healing
- Handle pod failover
- Not run single pod instead run pod through rc,rs,deployment for handling failover or if node fail where pod is running it will allocate it on different node.
- without Rc,Rs,deployment a unmanaged pod will be terminated and not gonna deploy to other node if node failed.
- Not support a set-based selector
  
```
apiVersion: v1
kind: ReplicationController
metadata:
  name: Tomcat-ReplicationController
spec:
  replicas: 3 
  template:
    metadata:
      name: Tomcat-ReplicationController
      labels:
        app: App
        component: neo4j
    spec:
      containers:
      - name: Tomcat
        image: tomcat: 8.0
        ports:
        - containerPort: 7474
```

### references
- https://www.tutorialspoint.com/kubernetes/kubernetes_replication_controller.htm
- 
