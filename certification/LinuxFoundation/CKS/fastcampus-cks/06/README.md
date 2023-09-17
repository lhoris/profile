## Question 06. Security Container(Dockerfile)

### Task:
- Analyze and edit the given Dockerfile (based on the node:13.12.0-alpine image) /home/user/FS00106/Dockerfile fixing two instructions present in the file being prominent security/best-practice issues.
- Analyze and edit the given manifest file /home/user/FS00106/deployment.yaml fixing two fields present in the file being prominent security/best-practice issues.
- Don't add or remove configuration settings; only modify the existing configuration settings, so that two configuration settings each are no longer security/best-practice concerns.
- Should you need an unprivileged user for any of the tasks, use user nobody with user id 65535.

> 🔍Search keyword: [Security Context Deployments]()

### Solution:
```shell
$ vi Dockerfile
---
FROM node:13.12.0-alpine
USER root
WORKDIR /app
ENV PATH /app/node_modules/.bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
COPY package.json ./
RUN npm install --silent
COPY . ./
USER nobody
CMD ["npm", "start"]

$ vi deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webserver-FS106
spec:
  replicas: 2
  selector:
    matchLabels:
      app: fs106
  template:
    metadata:
      labels:
        app: fs106
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        securityContext:
          privileged: false
          runAsUser: 65535
          capabilities:
            add: ["NET_ADMIN", "SYS_TIME"]
```