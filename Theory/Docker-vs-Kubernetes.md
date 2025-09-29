### Docker
- Container engine
- Manage the container lifecycle
- Containers are ephemeral(short living) in nature.
- Can build image, store images, create and run containers, manage networking and volumes but can not make sure your container will run always.
- Issues with docker:
    - OOMKilled(killed by OMMKilled to run other highpriority process)
        Kernel delete the container(low priority process based on algo) 
    - ImageIssue
    - Docker host failed(single host nature of docker)
    - Auto scaling(based on traffic)
- If container dies there is no machanism that create it again(no auto healing), user manually create it again.
- There is no Load balancing machanism in it we need to manually do it.
- To achive HA using docker we need to do lot of manually work or may write script to do it but in both case it is not a good approach.
- Docker is not supposed to do orchestration
- Docker not support Enterprise level standards(can use docker swarm to achive this)
    - LB
    - Auto healing
    - Auto Scaling
    - Firewall
    - Api Gateway

  
- 

### Kubernetes
- Container Orchestration Platform
- Containers are ephemeral(short living) in nature
- All the problem in docker is solved by kubernetes
    - Multiple Host
    - Auto scaling
    - Auth healing
    - Enterprise level support(networking, advance LB etc)
- K8S configure in form of cluster(master-slave architecture) --> solve problem of resource consumption of a node and Node failure.
- Kubernetes provide objects like RC,RS,Deployment for managing multiple replicas of application for HA and also provide a auto healing capablity.
- When ever container(pod) goes down kubernetes controller send request to kube-apiserver and the kubeapi-server intruct the kubelet to rollout the new controller in place of died one.
- Kubernetes also provide object HPA,VPA to increase the replicas of application based on traffic(after reaching the specifed threshold).
- By default does not support advance load blancing capablities.

