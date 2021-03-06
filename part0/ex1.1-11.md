### 1.1: Getting Started

```sh
jouni@MacBook-Pro ~ % docker ps -a     
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS                     PORTS     NAMES
1fb3f4aabf24   nginx     "/docker-entrypoint.…"   51 seconds ago   Up 51 seconds              80/tcp    optimistic_chatelet
f5c0e63b2e1b   nginx     "/docker-entrypoint.…"   53 seconds ago   Exited (0) 2 seconds ago             sleepy_lalande
aee42c173315   nginx     "/docker-entrypoint.…"   55 seconds ago   Exited (0) 2 seconds ago             ecstatic_beaver
```

### 1.2: Clean up 

[Note: I've some older images but not that nginx]

```sh
jouni@MacBook-Pro ~ % docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
jouni@MacBook-Pro ~ % docker images
REPOSITORY                           TAG                                                     IMAGE ID       CREATED         SIZE
docker/desktop-kubernetes            kubernetes-v1.19.7-cni-v0.8.5-critools-v1.17.0-debian   93b3398dbfde   7 months ago    285MB
k8s.gcr.io/kube-proxy                v1.19.7                                                 9d368f4517bb   8 months ago    118MB
k8s.gcr.io/kube-apiserver            v1.19.7                                                 c15e4f843f01   8 months ago    119MB
k8s.gcr.io/kube-scheduler            v1.19.7                                                 4fa642720eea   8 months ago    45.6MB
k8s.gcr.io/kube-controller-manager   v1.19.7                                                 67b3bca112d1   8 months ago    111MB
k8s.gcr.io/etcd                      3.4.13-0                                                0369cf4303ff   12 months ago   253MB
k8s.gcr.io/coredns                   1.7.0                                                   bfe3a36ebd25   15 months ago   45.2MB
docker/desktop-storage-provisioner   v1.1                                                    e704287ce753   17 months ago   41.8MB
docker/desktop-vpnkit-controller     v1.0                                                    79da37e5a3aa   18 months ago   36.6MB
k8s.gcr.io/pause                     3.2                                                     80d28bedfe5d   19 months ago   683kB
kitematic/hello-world-nginx          latest                                                  03b4557ad7b9   6 years ago     7.91MB
jouni@MacBook-Pro ~ %   
```

### 1.3: Secret message

```sh
jouni@MacBook-Pro ~ % docker run devopsdockeruh/simple-web-service:ubuntu
Unable to find image 'devopsdockeruh/simple-web-service:ubuntu' locally
ubuntu: Pulling from devopsdockeruh/simple-web-service
5d3b2c2d21bb: Pull complete 
3fc2062ea667: Pull complete 
75adf526d75b: Pull complete 
965d4bbb586a: Pull complete 
4f4fb700ef54: Pull complete 
Digest: sha256:d44e1dce398732e18c7c2bad9416a072f719af33498302b02929d4c112e88d2a
Status: Downloaded newer image for devopsdockeruh/simple-web-service:ubuntu
Starting log output
Wrote text to /usr/src/app/text.log
Wrote text to /usr/src/app/text.log
...
jouni@MacBook-Pro ~ % docker ps -a
CONTAINER ID   IMAGE                                      COMMAND                  CREATED          STATUS                        PORTS     NAMES
2a890ce41224   devopsdockeruh/simple-web-service:ubuntu   "/usr/src/app/server"    16 minutes ago   Up 16 minutes                           goofy_brahmagupta
```

In other terminal:
```sh
jouni@MacBook-Pro / % docker exec -it goofy_brahmagupta tail -f ./text.log
Secret message is: 'You can find the source code here: https://github.com/docker-hy'
2021-09-11 08:17:39 +0000 UTC
```

### 1.4: Missing dependencies

First:
``jouni@MacBook-Pro ~ % docker run -d -it --name dep ubuntu sh -c 'echo "Input website:"; read website; echo "Searching.."; sleep 1; curl http://$website;' ``

``jouni@MacBook-Pro ~ % docker exec -it dep apt-get install curl  (<- edit: I think I did apt-get update earlier when struggling with this)``

```sh
jouni@MacBook-Pro ~ % docker start dep                        
dep
jouni@MacBook-Pro ~ % docker attach dep                       
helsinki.fi
Searching..
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>301 Moved Permanently</title>
</head><body>
<h1>Moved Permanently</h1>
<p>The document has moved <a href="https://www.helsinki.fi/">here</a>.</p>
</body></html>
```

### 1.5: Sizes of images

```sh
devopsdockeruh/simple-web-service    ubuntu 4e3362e907d5   6 months ago    83MB
devopsdockeruh/simple-web-service    alpine fd312adc88e0   6 months ago    15.7MB
```
```sh
jouni@MacBook-Pro ~ % docker run -d -it --name alpine1 devopsdockeruh/simple-web-service:alpine
abeac2c915ddfa79d13f1703d1d8229c188adfea3aaf0192692f182b73f41821
jouni@MacBook-Pro ~ % docker exec -it alpine1 tail -f ./text.log                             
2021-09-11 12:28:40 +0000 UTC
2021-09-11 12:28:42 +0000 UTC
2021-09-11 12:28:44 +0000 UTC
2021-09-11 12:28:46 +0000 UTC
2021-09-11 12:28:48 +0000 UTC
Secret message is: 'You can find the source code here: https://github.com/docker-hy'
```

### 1.6: Hello Docker Hub

From Docker Hub site (README.md):
"This is the secret message"

Option 2: 
``docker run -it devopsdockeruh/pull_exercise sh
/usr/app # less README.md``
-> 
...
This is the readme, use input "basics" to complete this exercise.

### 1.7: Two line Dockerfile

Dockerfile:
```sh
# Start from the alpine image that is smaller but no fancy tools
FROM devopsdockeruh/simple-web-service:alpine
CMD server
```

Commands:
``docker build . -t web-server``
``docker run web-server``

### 1.8: Image for Script

Dockerfile:
```sh
Dockerfile:
# Start from the ubuntu image
FROM ubuntu:18.04

# Use /usr/src/app as our workdir. The following instructions will be executed in this location.
WORKDIR /usr/src/app

# scipt.sh contains echo "Input website:"; read website; echo "Searching.."; sleep 1; curl http://$website;
COPY script.sh .

RUN chmod +x script.sh
RUN apt-get update && apt-get install -y curl 

CMD ./script.sh
```

(Build: docker build . -t curler)

Command: ``docker run -it curler``

### 1.9: Volumes

``touch text.log  
docker run -v "$(pwd)/text.log:/usr/src/app/text.log" devopsdockeruh/simple-web-service``

### 1.10: Ports Open

``docker run -p 8006:8080 web-server server``

### 1.11: Spring

Dockerfile: 
```sh
FROM openjdk:8
EXPOSE 3000
WORKDIR /usr/src/app

# this the hard part COPY . . didn't work with mac.
COPY /spring-example-project /usr/src/app 
RUN ./mvnw package
#RUN java -jar ./target/docker-example-1.1.3.jar
CMD ["java", "-jar", "./target/docker-example-1.1.3.jar"]
```

Commands: ``docker build . -t spring && docker run -p 8080:8080 spring``
