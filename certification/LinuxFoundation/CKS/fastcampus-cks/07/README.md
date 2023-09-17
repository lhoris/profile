## Question 07. CIS benchmarks(kube-bench)
### Context
A CIS Benchmark tool was run against the kubeadm - created cluster and found multiple issues that must be addressed immediately.

### Task:
- Fix all issues via configuration and restart the affected components to ensure the new settings take effect.
- Fix all of the following violations that were found against the API server:
```
[FAIL] 1.2.7 Ensure that the --authorization-mode argument includes Node
[FAIL] 1.2.8 Ensure that the --authorization-mode argument includes RBAC
[FAIL] 1.2.10 Ensure that the admission control plugin AlwaysAdmit is not set
```
- Fix all of the following violations that were found against etcd:
```
[FAIL] 2.2 Ensure that the --client-cert-auth argument is set to true
```
- Fix all of the following violations that were found against the kubelet:
```
[FAIL] 4.2.1 Ensure that the --anonymous-auth argument is set to false
[FAIL] 4.2.2 Ensure that the --authorization-mode argument is not set to AlwaysAllow
```
  + Use Webhook authn/authz where possible.

### Solution:
```shell
$ vi /etc/kubernetes/manifest/kube-apiserver.yaml
---
...
spec:
  containers:
  - command:
    - kube-apiserver
    - --authorization-mode=Node,RBAC
    - --enable-admission-plugins=NodeRestriction

$ vi /etc/kubernetes/manifest/etcd.yaml
---
...
spec:
  containers:
  - command:
    - etcd
    - --client-cert-auth=true

$ vi /var/lib/kubelet/config.yaml
---
...
authentication:
  anonymous:
    enabled: false
...
authorization:
  mode: Webhook

$ systemctl restart kubelet.service
```