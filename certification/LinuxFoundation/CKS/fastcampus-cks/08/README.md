## Question 08. Verify kubernetes Binaries
### Context
#### Verify the Binaries and Delete Any That May Contain Malicious Code
Use the checksums to verify all of the binaries contained in /data/cks/binary.
Delete any binary files that fail checksum verification, as they could contain malicious code.

### Task:
- Locate and Download the Checksums for the Binary Files
  + the binary files are located in /data/cks/binary/.
  + Your coworker has left the version number they used in a file called version.txt. Check this file for the version number. Don't try to check the version by running any of the binaries before you have verified them (e.g.. with a command like kubectl version)! All of the binaries are the same version.
- Use the version number to download the correct checksum file for each of the binary files.
- Download the checksums to the same directory the binaries are located in (/data/cks/binary/).

> 🔍Search keyword: [kubectl checksum](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/#install-kubectl-binary-with-curl-on-linux)

### Solution:
```shell
$ echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check

---
kubectl: OK
or
kubectl: FAILED
sha256sum: WARNING: 1 computed checksum did NOT match
```