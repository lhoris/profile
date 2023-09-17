## Question 01. ServiceAccount

### Context
A **Pod fails to run** because of an incorrectly specified **ServiceAccount**.

### Task:
- Create a new **ServiceAccount** named `devops-sa` in the existing namespace `fastcampus`, witch must not have access to any **secrets**.
- Inspect the Pod named `devops` running in the namespace `fastcampus`. Edit the Pod to use the newly created ServiceAccount.
- Final, Delete the service account except for the `devops-sa` in the `fastcampus` namespace.

> 🔍Search keyword: [Service Accounts](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#opt-out-of-api-credential-automounting)

### Solution:
1. Create ServiceAccount
```shell
$ k create serviceaccount devops-sa -n fastcampus --dry-run=client -o yaml > devops-sa.yaml
$ vi devops-sa.yaml

apiVersion: v1
kind: ServiceAccount
metadata:
  name: devops-sa
  namespace: fastcampus
automountServiceAccountToken: false
...

$ k apply -f devops-sa.yaml
```

2. Edit use serviceaccount in pod yaml
```shell
$ k get pod -n fastcampus devops -o yaml > devops.yaml
$ vi devops.yaml
...
  serviceAccount: default
  serviceAccountName: default
...

...
  serviceAccount: devops-sa
  serviceAccountName: devops-sa
  automountServiceAccountToken: false
...
```

3. replace pod
```shell
$ k replace -f devops.yaml --force
```