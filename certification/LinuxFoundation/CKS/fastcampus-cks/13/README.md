## Question 13. Scanning Images for Vulnerabilities with Trivy
### Context
Scan the images used by all Pods in this namespace with Trivy and save the results, then delete any Pods that have high- or critical-severity vulnerabilities.

### Task:
- Use the Trivy open-source container scanner to detect images with severe vulnerabilities used by Pods in the namespace fastcampus.
- Look for images with High or Critical severity vulnerabilities, and delete the Pods that use those images.

> Trivy is pre-installed on the cluster's master node only; it is not available on the base system or the worker nodes. You'll have to connect to the cluster's master node to use Trivy.

> 🔍Search keyword: [Trivy](https://aquasecurity.github.io/trivy/)

### Solution:
```shell
$ k describe pod -n fastcampus web | grep -i image:
$ k describe pod -n fastcampus agent | grep -i image:
$ k describe pod -n fastcampus cache | grep -i image:

$ trivy image busybox:1.33.1
$ trivy image nginx:1.14
$ trivy image redis:3.2

$ k delete pod -n fastcampus web --force
$ k delete pod -n fastcampus cache --force
```