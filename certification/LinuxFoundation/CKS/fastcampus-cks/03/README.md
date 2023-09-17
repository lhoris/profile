## Question 03. NetworkPolicy (Type 2)
### Context
Create a NetworkPolicy named class-netpol to restrict access to Pod class-list running in namespace elearning-ns.

### Task:
Only allow the following Pods to connect to Pod `class-list`:
- Pods in the namespace management-ns
- Pods with label group=student, in any namespace
- Make sure to apply the NetworkPolicy.

✅ You can find a skeleton manifest file at `class-networkpolicy.yaml`

> 🔍Search keyword: [Network Policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/#default-deny-all-ingress-and-all-egress-traffic)

### Solution:
```shell
# 1. Create NetworkPolicy
$ vi class-networkpoicy.yaml
---
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: class-netpol
  namespace: elearning-ns
spec:
  podSelector:
    matchLabels:
      category: edu
  policyTypes:
    - Ingress
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              role: management
        - podSelector:
            matchLabels:
              group: student
          namespaceSelector: {}

$ k apply -f class-networkpolicy.yaml

# 2. validation

$ k get pod -n elearning-ns class-list -o wide
NAME         READY   STATUS    RESTARTS   AGE   IP               NODE     NOMINATED NODE   READINESS GATES
class-list   1/1     Running   0          14m   172.16.103.131   w2-k8s   <none>           <none

# 2-1. OK case
$ k run test -n management-ns -it --rm --image=nginx -- /bin/bash
If you don't see a command prompt, try pressing enter.
root@test:/# curl 172.16.103.131
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
root@test:/# exit

---

# 2-2. OK case
$ k run test --labels=group=student -it --rm --image=nginx -- /bin/bash
If you don't see a command prompt, try pressing enter.
root@test:/# curl 172.16.103.131
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
root@test:/# exit

---

# 2-3. NO case
$ k run test -it --rm --image=nginx -- /bin/bash
If you don't see a command prompt, try pressing enter.
root@test:/# curl 172.16.103.131
^C
root@test:/# exit
exit
Session ended, resume using 'kubectl attach test -c test -i -t' command when the pod is running
pod "test" deleted
```