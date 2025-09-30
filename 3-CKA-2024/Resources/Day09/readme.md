# Day 9/40 - Kubernetes Services Explained - ClusterIP vs NodePort vs Loadbalancer vs External ☸️


## Check out the video below for Day9 👇

[![Day9/40 - Kubernetes Services Explained - ClusterIP vs NodePort vs Loadbalancer vs External](https://img.youtube.com/vi/tHAQWLKMTB0/sddefault.jpg)](https://youtu.be/tHAQWLKMTB0)


### Pre-requisite for Kind cluster
If you use a Kind cluster, you must perform the port mapping to expose the container port. Use the below config to create a new Kind cluster

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  extraPortMappings:
  - containerPort: 30001
    hostPort: 30001
- role: worker
- role: worker
```
###command to create new cluster 

``` kind create cluster --config kind.yaml --name cka-cluster```

### What is Service in Kubernetes

Different applications communicate with each other within Kubernetes using a service; it is also used to access applications outside the cluster.

![image](https://github.com/piyushsachdeva/CKA-2024/assets/40286378/e768b073-dd7b-478a-bbea-ad6acae18051)

There are 4 types of Services:
- ClusterIP(For Internal access)
- NodePort(To access the application on a particular port)
- LoadBalancer(To access the application on a domain name or IP address without using the port number)
- External (To use an external DNS for routing)

### ClusterIP

![image](https://github.com/piyushsachdeva/CKA-2024/assets/40286378/3817a5e7-5208-41c8-9dee-d4c052038151)

#### Sample YAML for ClusterIP

```yaml
apiVersion: v1
kind: Service
metadata:
  name: cluster-svc
  labels:
    env: demo
spec:
  ports:
  - port: 80
  selector:
    env: demo
```


### NodePort

![image](https://github.com/piyushsachdeva/CKA-2024/assets/40286378/8aa9c482-be3a-450a-95b7-0a0c0e80403e)

#### Sample YAML for Nodeport

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nodeport-svc
  labels:
    env: demo
spec:
  type: NodePort
  ports:
  - nodePort: 30001
    port: 80
    targetPort: 80
  selector:
    env: demo
```
-> NodePort: Expose on nodes for external access
-> port: Internal port that is use by internal application to communicate with service
-> targetPort: container port


### LoadBalancer
- Your loadbalancer service will act as nodeport if you are not using any managed cloud Kubernetes such as GKE,AKS,EKS etc. In a managed cloud environment, Kubernetes creates a load balancer within the cloud project, which redirects the traffic to the Kubernetes Loadbalancer service.

![image](https://github.com/piyushsachdeva/CKA-2024/assets/40286378/8f5acc88-4394-47e9-a3c5-041d396166d0)

#### Sample YAML for Loadbalancer

```yaml
apiVersion: v1
kind: Service
metadata:
  name: lb-svc
  labels:
    env: demo
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    env: demo
```
* [Kind LB](https://kind.sigs.k8s.io/docs/user/loadbalancer)


#### Sample YAML for external name

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
  namespace: prod
spec:
  type: ExternalName
  externalName: my.api.example.com
```

```bash
kubectl get pods --show-labels
kubectl apply -f service.yaml
kubectl get svc
kubectl get svc -l <lables>
kubectl get endpoints
kubectl describe svc <svc_name>
kubectl describe endpoint <ep_name>
kubectl api-resources | grep -i endpoints
kubectl api-resources | grep -i svc
kubectl expose <resource_type> <resource_name> --port=<port> --target-port=<targetPort> --type=<serviceType>

```
* Expose a Deployment externally (NodePort)
```bash
kubectl expose deployment my-deployment --port=80 --target-port=8080 --type=NodePort
```
- Service gets a random port between 30000–32767
- You can access it using <NodeIP>:<NodePort>

* Expose with a fixed NodePort
```bash
kubectl expose deployment my-deployment --port=80 --target-port=8080 --type=NodePort --name=my-service --overrides='
{
  "spec": {
    "ports": [{
      "port": 80,
      "targetPort": 8080,
      "nodePort": 31000
    }]
  }
}'
```

* Expose a ReplicaSet
```bash
kubectl expose rs my-replicaset --port=80 --target-port=8080
```

```bash
kubectl expose pod nginx1 --port=80 --target-port=80 --type=NodePort --name=my-service1 --dry-run=server -ojson
{
    "kind": "Service",
    "apiVersion": "v1",
    "metadata": {
        "name": "my-service1",
        "namespace": "default",
        "uid": "33342579-ccd4-405b-acdb-f7144751f097",
        "creationTimestamp": "2025-09-30T15:35:08Z",
        "labels": {
            "run": "nginx1"
        }
    },
    "spec": {
        "ports": [
            {
                "protocol": "TCP",
                "port": 80,
                "targetPort": 80,
                "nodePort": 32769
            }
        ],
        "selector": {
            "run": "nginx1"
        },
        "clusterIP": "10.96.0.0",
        "clusterIPs": [
            "10.96.0.0"
        ],
        "type": "NodePort",
        "sessionAffinity": "None",
        "externalTrafficPolicy": "Cluster",
        "ipFamilies": [
            "IPv4"
        ],
        "ipFamilyPolicy": "SingleStack",
        "internalTrafficPolicy": "Cluster"
    },
    "status": {
        "loadBalancer": {}
    }
}
```