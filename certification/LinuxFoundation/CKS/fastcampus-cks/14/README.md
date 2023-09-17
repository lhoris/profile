## Question 14. Automate Kubernetes Image Vulnerability Scanning
### Context
A container image scanner is set up on the cluster, but it's not yet fully integrated into the cluster's configuration. When complte, the container image scanner shall scan for and reject the use of vulnerable images.

### Task:
- You will find a skeleton admission control configuration at `/etc/kubernetes/fastcampus/admission-configuration.yaml`. Configure the admission controller by adding the necessary configuration to this file.
  + The kubeconfig is located at `/etc/kubernetes/fastcampus/webhook_backend.kubeconfig`.
  + Configure the admission controller to `deny` workload creation by `default`, even if the backend webhook cannot be reached.
- Modify the admission controller's kubeconfig to point to the backend service. The backend service can be reached at `https://acg.trivy.k8s.webhook:8090/scan`.
- Configure the API server to enable any necessary admission control plugins.
- Once this is done, the API server should be using the backend webhook to scan images and deny workloads that have major vulnerabilities.
- Finally, test if the configuration is working by trying to deploy the vulnerable resource `/home/user/FS00114/vulnerable-manifest.yaml`.

> You have to complte the entire task on the cluster's master node, where all services and files have been prepared and placed.

> 🔍Search keyword: [ImagePolicyWebhook](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#imagepolicywebhook)

### Solution:
```shell
$ vi /etc/kubernetes/fastcampus/admission-configuration.yaml
---
apiVersion: apiserver.config.k8s.io/v1
kind: AdmissionConfiguration
plugins:
  - name: ImagePolicyWebhook
    configuration:
      imagePolicy:
        kubeConfigFile: /etc/kubernetes/fastcampus/webhook_backend.kubeconfig

$ vi /etc/kubernetes/fastcampus/webhook_backend.kubeconfig
---
apiVersion: v1
kind: Config
clusters:
- cluster:
  name: trivy-k8s-webhook
  cluster:
    certificate-authority: /etc/kubernetes/fastcampus/imagepolicywebhook-ca.crt
    server: https://acg.trivy.k8s.webhook:8090/scan
...
users:
- name: api-server
  user:
    client-certificate: /etc/kubernetes/fastcampus/api-server-client.crt
    client-key: /etc/kubernetes/fastcampus/api-server-client-key

$ vi /home/user/FS00114/vulnerable-manifest.yaml
...
- command:
  - kube-apiserver
  - --admission-control-config-file=/etc/kubernetes/fastcampus/admission-configuration.yaml
...
    volumeMounts:
    - name: admission-control
      mountPath: /etc/kubernetes/fastcampus
      readOnly: true
  volumes:
  - name: admission-control
    hostPath:
      path: /etc/kubernetes/fastcampus
      type: DirectoryOrCreate

$ k get node
```