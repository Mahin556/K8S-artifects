- Abstraction over a containers
- Containers have shared network namespace(default) and pid name space(not default). 
- 2 types of pod
  1. single container pod
  2. multi container pod
 
```
kubectl run <name of pod> --image=<name of the image from registry>
```
```
kubectl run tomcat --image = tomcat:8.0
```
