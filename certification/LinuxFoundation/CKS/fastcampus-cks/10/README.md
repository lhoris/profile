## Question 10. Move Kubernetes Pods to a Secured Runtime Sandbox(containerd)
### Context
This cluster uses containerd as CRI runtime. Containerd's default runtime handler is runc. Containerd has been prepared to support an additional runtime handler, runsc (gVisor).

### Task:
- Create a RuntimeClass for the Sandbox
  + Create a RuntimeClass named fs-class using the prepared runtime handler named runsc.
- Move All Pods in the fastcampus Namespace to the New Runtime Sandbox
  + Update all Pods in the namespace fastcampus to run on gVisor, unless they are already running on a non-default runtime handler.

> ✅ You can find a skeleton manifest file at  
./home/user/FS00110/runtime-class.yaml  
./home/user/FS00110/order.yaml  
./home/user/FS00110/pay.yaml  

> 🔍Search keyword: [Runtime Class](https://kubernetes.io/docs/concepts/containers/runtime-class/#1-configure-the-cri-implementation-on-nodes)

### Solution:
```shell
$ vi runtime-class.yaml
---
apiVersion: node.k8s.io/v1
kind: RuntimeClass
metadata:
  name: fs-class
handler: runsc

$ k apply -f runtime-class.yaml

$ vi order.yaml
---
...
spec:
  runtimeClassName: fs-class

$ k apply -f order.yaml

$ vi pay.yaml
---
...
spec:
  runtimeClassName: fs-class

$ k apply -f pay.yaml
```