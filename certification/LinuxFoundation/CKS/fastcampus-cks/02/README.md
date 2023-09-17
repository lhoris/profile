## Question 02. NetworkPolicy (Type 1)
### Context
A default-deny NetworkPolicy avoids to accidentally expose a Pod in a namespace that doesn't have any other NetworkPolicy defined.

### Task:
- Create a new default-deny NetworkPolicy named `deny-all-np` in the namespace `eshop` for all traffic of type Ingress and Egress.
- The new NetworkPolicy must deny all Ingress and Egress traffic in the namespace eshop.
- Apply the newly created default-deny NetworkPolicy to all Pods running in namespace eshop.

✅ You can find a skeleton manifest file at `eshop-deny-networkpolicy.yaml`

> 🔍Search keyword: [Network Policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/#default-deny-all-ingress-and-all-egress-traffic)

### Solution:

1. Create Networkpolicy
```shell
$ vi /home/user/FS00102/eshop-deny-networkpolicy.yaml
---
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all-np
  namespace: eshop
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  - Egress

$ k apply -f /home/user/FS00102/eshop-deny-networkpolicy.yaml
```

2. validation
```shell
$ k run -n eshop webserver --image=nginx
$ k get pod -n eshop webserver -o wide

# default namespace ---> eshop namespace's pod
$ k run client -it --rm --image=centos -- /bin/bash
[root@client /]# curl 192.168.166.130
[root@client /]# exit
```