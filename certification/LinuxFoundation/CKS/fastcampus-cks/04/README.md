## Question 04. RBAC(Role)
### Context
A Role bound to a Pod's ServiceAccount grants overly permissive permissions.
Complete the following tasks to reduce the set of permissions.

### Task:
- Given an existing Pod named web-pod running in the namespace frontend.
- Edit the existing Role bound to the Pod's ServiceAccount web-sa to only allow performing get operations, only on resources of type Endpoint.
- Create a new Role named ns-role in the namespace frontend, which only allows performing delete operations, only on resources of type namespaces.
- Create a new RoleBinding named ns-role-binding binding the newly created Role to the Pod's ServiceAccount.
- Don't delete the existing RoleBinding.

> 🔍Search keyword: [RBAC](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#kubectl-create-role)

### Solution:
```shell
$ k edit role -n frontend web-role

---
rules:
- apiGroups:
  - ""
  resourceNames:
  - web-pod
  resources:
  - endpoints
  verbs:
  - get

$ k describe role -n frontend web-role

$ k create role -n frontend ns-role --verb=delete --resource=namespace
$ k create rolebinding -n frontend ns-role-binding --serviceaccount=frontend:web-sa --role=ns-role

# validation
$ k auth can-i delete -n frontend namespaces --as=system:serviceaccount:frontend:web-sa
```