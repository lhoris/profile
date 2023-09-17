## Question 11. Protect a Kubernetes Cluster with AppArmor(Profile)
### Context
AppArmor is enabled on the cluster's worker node. An AppArmor profile is prepared, but not enforced yet.  
ℹ️ You may use your browser to open one additional tab to access the AppArmor documentation.

### Task:
- On the cluster's worker node, enforce the prepared AppArmor profile located at /home/user/FS00111/k8s-apparmor-deny-write.
- Edit the prepared manifest file located at /home/user/FS00111/deny-write-pod.yaml to apply the AppArmor profile.
- Finally, apply the manifest file and create the Pod specified in it.

> 🔍Search keyword: [AppArmor](https://gitlab.com/apparmor/apparmor/-/wikis/Documentation), [Resources with AppArmor](https://kubernetes.io/docs/tutorials/security/apparmor/#example)

### Solution:
```shell
$ k get node
$ ssh k8s-worker1
$ cat /home/user/FS001/k8s-apparmor-deny-write
$ sudo apparmor_parser /home/user/FS001/k8s-apparmor-deny-write

$ vi /home/user/FS00111/deny-write-pod.yaml
---
...
metadata:
  name: writedeny-pod
  namespace: fastcampus
  annotations:
    container.apparmor.security.beta.kubernetes.io/writedeny: localhost/k8s-apparmor-deny-write
spec:
  containers:
  - name: writedeny

$ k apply -f /home/user/FS00111/deny-write-pod.yaml
```