## Question 16. Check Kubernetes Pods for Container Immutability
### Context
It is best-practice to design containers to be stateless and immutable.

### Task:
- Inspect Pods running in namespace fastcampus and delete any Pod that is either not stateless or not immutable.
- Use the following strict interpretation of stateless and immutable:
  + Pods being able to store data inside containers must be treated as not stateless.
  ```
  You don't have to worry whether data is actually stored inside containers or not already.
  ```
  + Pods being configured to be privileged in any way must be treated as potentially not stateless and not immutable.

> 🔍Search keyword: [Pod Security Standards](https://kubernetes.io/docs/concepts/security/pod-security-standards/)

### Solution:
```shell
k get pod -n fastcampus order -o yaml | grep -i securitycontext -m 3
k get pod -n fastcampus payment -o yaml | grep -i securitycontext -m 3
k get pod -n fastcampus user -o yaml | grep -i securitycontext -m 3

k delete pod -n fastcampus order --force
k delete pod -n fastcampus user --force
```