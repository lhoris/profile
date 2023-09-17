## Question 15. Kubernetes Threat Detection with sysdig/falco
### Context
Use sysdig/falco to analyze the container for any spawning processes.

### Task:
- Use runtime detection tools to detect anomalous processes spawning and executing frequently in the single container belonging to Pod web. Two tools are available to use
  + sysdig, falco
- Using the tool of your choice (including any non preinstalled tool), analyse the container's behaviour for at least 20 seconds, using filters that detect newly spawning and executing processes.
- Store an incident file at /home/user/FS00115/summary, containing the detected incidents, one per line, in the following format:

> The tools are pre-installed on the cluster's worker node only; they are not available on the base system or the master node.
```
[timestamp], [uid], [processName]
```

### Solution:
```shell
$ sudo -i
$ sysdig --help

$ sysdig -l | grep -e timestamp -e uid -e proc

$ docker ps | grep web

$ sysdig -M 20 -p "[%evt.time], [%user.uid], [%proc.name]" container.id=af95c0fb9452 > /home/user/FS00115/summary

$ cat /home/user/FS00115/summary
```