## Question 12. Add TLS to a Kubernetes Service with Ingress
### Context
Use an Ingress to implement TLS termination for the Service

### Task:
- Certificate for Ingress has already been created in /home/user/FS00112/tls.crt, /home/user/FS00112/tls.key in the fastcampus.com domain
- Create a new secret named fs-secret in the fastcampus namespace with TLS credentials.
- The frontend-svc can be found in the fastcampus namespace. The Service listens on port 80.
- Create an ingress named fs-ingress that contains the certificate in the fastcampus namespace. When the configuration is complte, try connecting to workernode:30200/ and check if it is connected to the frontend-service:80 service.

> You can find a skeleton manifest file at  
./home/user/FS00112/fs-secret.yaml  
./home/user/FS00112/frontend-ingress.yaml  
./home/user/FS00112/tls.key  
./home/user/FS00112/tls.crt  

### Solution:
```shell
$ k create secret tls -n fastcampus fs-secret --key=/home/user/FS00112/tls.key --cert=/home/user/FS00112/tls.crt

$ vi /home/user/FS00112/frontend-ingress.yaml
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: fs-ingress
  namespace: fastcampus
spec:
  tls:
  - hosts:
    secretName: fs-secret
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: frontend-svc
            port:
              number: 80

$ k apply -f /home/user/FS00112/frontend-ingress.yaml
```