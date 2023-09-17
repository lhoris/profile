## Question 05. Secret

### Task:
- Retrieve the content of the existing secret named db1-secret in the backend namespace.
- Store the username field in a file named /home/user/username.txt, and the password field in a file named /home/user/password.txt. You must create both files; they don't exist yes.
- Do not use/modify the created files in the follwing steps, create new temporary files if needed.
  + Create a new secret named db2-secret in the backend namespace, with the following content:
    - username: db-manager
    - password: QGRicGFzcy4K
  + Finally, create a new Pod that has access to the secret db2-secret via a volume:
    - Pod name&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;cache-pod
    - Namespace&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;backend
    - Container name&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;cache
    - Image&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;redis
    - Volume name&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;secret-volume
    - Mount path&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;/etc/secret

> 🔍Search keyword: [Secrets](https://kubernetes.io/docs/concepts/configuration/secret/#docker-config-secrets)

### Solution:
```shell
$ k get secret -n backend db1-secret -o jsonpath='{.data.username}' | base64 -d > /home/user/username.txt
$ k get secret -n backend db1-secret -o jsonpath='{.data.password}' | base64 -d > /home/user/password.txt
$ k create secret generic -n backend db2-secret --from-literal username=db-manager --from-literal=password=QGRicGFzcy4K

$ vi cache-pod.yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: cache-pod
spec:
  containers:
  - name: cache
    image: redis
    volumeMounts:
    - name: secret-volume
      mountPath: "/etc/secret"
  volumes:
  - name: secret-volume
    secret:
      secretName: db2-secret

$ k apply -f cache-pod.yaml
```