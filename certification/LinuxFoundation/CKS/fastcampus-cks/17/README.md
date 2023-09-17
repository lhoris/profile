## Question 17. Configure Audit Logging in Kubernetes
### Context
Audit logging is essential to any Kubernetes security strategy. Enable audit logs in the cluster.

### Task:
- Enable audit logs in the cluster. To do so, enable the log backend, and ensure that:
  + at maximum, a number of `2` old audit log files are retained
  + logs are stored at `/var/logs/k8s-audit/k8s-audit.log`
  + log files are retained for `10 days`.
- A basic policy is provided at `/etc/kubernetes/audit-policy.yaml`. It only specifiles what not to log.
  ```
  The base policy is located on the cluster's master node.
  ```
- Edit and extend the basic policy to log:
  + namespaces changes at RequestResponse level
  + Log request bodies (but not response bodies) for changes to Pods and Services in the fastcampus Namespace.
  + ConfigMaps and Secret changes in all namespaces at the Metadata level.
  + Also, add a catch-all rule to log all other requests at the Metadata level.

> 🔍Search keyword: [Audit kube-apiserver](https://kubernetes.io/docs/tasks/debug/debug-cluster/audit/#audit-policy)

### Solution:
```shell
$ vi /etc/kubernetes/audit-policy.yaml
---
apiVersion: audit.k8s.io/v1
kind: Policy
rules:
  - level: RequestResponse
    resources:
    - group: ""
      resources: ["namespaces"]

  - level: Request
    resources:
    - group: ""
      resources: ["pods", "services"]
    namespaces: ["fastcampus"]

  - level: Metadata
    resources:
    - group: ""
      resources: ["secrets", "configmaps"]

  - level: Metadata

$ vi /etc/kubernetes/manifests/kube-apiserver.yaml
...
  - command:
    - kube-apiserver
    - --audit-policy-file=/etc/kubernetes/audit-policy.yaml
    - --audit-log-path=/var/log/k8s-audit/k8s-audit.log
    - --audit-log-maxage=10
    - --audit-log-maxbackup=2
...
    volumeMounts:
    - name: audit-config
      mountPath: /etc/kubernetes/audit-policy.yaml
      readOnly: true
    - name: audit-log
      mountPath: /var/log/k8s-audit
      readOnly: false
...
  volumes:
  - name: audit-config
    hostPath:
      path: /etc/kubernetes/audit-policy.yaml
      type: File
  - name: audit-log
    hostPath:
      path: /var/log/k8s-audit
      type: DirectoryOrCreate

$ k get node
$ tail -f /var/log/k8s-audit/k8s-audit.log
```