- A Service can expose multiple ports (for apps that need HTTP + HTTPS, etc).
```yaml
apiVersion: v1
kind: Service
metadata:
  name: tutorial-point-service
spec:
  selector:
    application: my-application
  ports:
    - name: http
      protocol: TCP
      port: 80
      targetPort: 31999
    - name: https
      protocol: TCP
      port: 443
      targetPort: 31998
```
- Each port must have a name when using multiple ports.
- Useful for applications exposing more than one protocol.

### References:
- https://www.tutorialspoint.com/kubernetes/kubernetes_service.htm