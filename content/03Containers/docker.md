---
title: "Docker Fundamentals"
linkTitle: "Docker Fundamentals"
weight: 2
---

## Intro

From [Docker's Website](https://docs.docker.com/get-started/overview/){:target="_blank"}: Docker is an open platform for developing, shipping, and running applications. Docker enables you to separate your applications from your infrastructure so you can deliver software quickly. With Docker, you can manage your infrastructure in the same ways you manage your applications. By taking advantage of Docker’s methodologies for shipping, testing, and deploying code quickly, you can significantly reduce the delay between writing code and running it in production.


## Quick Start

The first step is to install docker. You can do this on any ubuntu linux

```bash
sudo apt-get update
sudo apt  install docker.io
```

```bash
sudo docker info
```

```
ubuntu@ip-10-0-15-223:~$ sudo docker info
Client:
 Context:    default
 Debug Mode: false

Server:
 Containers: 0
  Running: 0
  Paused: 0
  Stopped: 0
 Images: 0
 Server Version: 20.10.21
 Storage Driver: overlay2
  Backing Filesystem: extfs
  Supports d_type: true
  Native Overlay Diff: true
  userxattr: false
 Logging Driver: json-file
 Cgroup Driver: systemd
 Cgroup Version: 2
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
 Swarm: inactive
 Runtimes: io.containerd.runc.v2 io.containerd.runtime.v1.linux runc
 Default Runtime: runc
 Init Binary: docker-init
 containerd version: 
 runc version: 
 init version: 
 Security Options:
  apparmor
  seccomp
   Profile: default
  cgroupns
 Kernel Version: 5.19.0-1025-aws
 Operating System: Ubuntu 22.04.2 LTS
 OSType: linux
 Architecture: x86_64
 CPUs: 1
 Total Memory: 965.7MiB
 Name: ip-10-0-15-223
 ID: LOLA:J7IH:363C:AN4D:LAA6:F2RP:R7VA:7BTV:F3FG:YFSH:XB4M:B2FV
 Docker Root Dir: /var/lib/docker
 Debug Mode: false
 Registry: https://index.docker.io/v1/
 Labels:
 Experimental: false
 Insecure Registries:
  127.0.0.0/8
 Live Restore Enabled: false

ubuntu@ip-10-0-15-223:~$
```
```bash
sudo docker images
```

```
ubuntu@ip-10-0-15-223:~$ sudo docker images
REPOSITORY   TAG       IMAGE ID   CREATED   SIZE
ubuntu@ip-10-0-15-223:~$ 
```


```bash
docker images
```

```
ubuntu@ip-10-0-15-223:~$ docker images
Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get "http://%2Fvar%2Frun%2Fdocker.sock/v1.24/images/json": dial unix /var/run/docker.sock: connect: permission denied
ubuntu@ip-10-0-15-223:~$
```

```bash
sudo usermod -aG docker ubuntu
newgrp docker
```
Try again...

```bash
docker run alpine ls
```

```
ubuntu@ip-10-0-15-223:~$ docker run alpine ls
Unable to find image 'alpine:latest' locally
latest: Pulling from library/alpine
31e352740f53: Pull complete 
Digest: sha256:82d1e9d7ed48a7523bdebc18cf6290bdb97b82302a8a9c27d4fe885949ea94d1
Status: Downloaded newer image for alpine:latest
bin
dev
etc
home
lib
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var
ubuntu@ip-10-0-15-223:~$ 
```

```bash
docker run alpine pwd
```
```
ubuntu@ip-10-0-15-223:~$ docker run alpine pwd
/
ubuntu@ip-10-0-15-223:~$ docker run alpine pwd
```

```bash
docker run alpine whoami
```

```
ubuntu@ip-10-0-15-223:~$ docker run alpine whoami
root
ubuntu@ip-10-0-15-223:~$ 
```
## Working with Images

```bash
docker images
```

```
ubuntu@ip-10-0-15-223:~$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED      SIZE
alpine       latest    c1aabb73d233   9 days ago   7.33MB
ubuntu@ip-10-0-15-223:~$ 
```


```bash
docker ps -a
```

```
ubuntu@ip-10-0-15-223:~$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
ubuntu@ip-10-0-15-223:~$ docker ps -a
CONTAINER ID   IMAGE     COMMAND         CREATED              STATUS                          PORTS     NAMES
9f750c816036   alpine    "whoami"        About a minute ago   Exited (0) About a minute ago             strange_khorana
646337c35466   alpine    "pwd"           2 minutes ago        Exited (0) 2 minutes ago                  pensive_darwin
a3b144ec4226   alpine    "ls"            3 minutes ago        Exited (0) 3 minutes ago                  recursing_elbakyan
ubuntu@ip-10-0-15-223:~$ 
```

```bash
docker rm 9f750c816036 646337c35466 a3b144ec4226
```

```
ubuntu@ip-10-0-15-223:~$ docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
ubuntu@ip-10-0-15-223:~$ 
```

```bash
docker pull ubuntu
```

```
ubuntu@ip-10-0-15-223:~$ docker pull ubuntu
Using default tag: latest
latest: Pulling from library/ubuntu
6b851dcae6ca: Pull complete 
Digest: sha256:6120be6a2b7ce665d0cbddc3ce6eae60fe94637c6a66985312d1f02f63cc0bcd
Status: Downloaded newer image for ubuntu:latest
docker.io/library/ubuntu:latest
ubuntu@ip-10-0-15-223:~$ 
```

```bash
docker images
```

```
ubuntu@ip-10-0-15-223:~$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
alpine       latest    c1aabb73d233   9 days ago    7.33MB
ubuntu       latest    99284ca6cea0   2 weeks ago   77.8MB
ubuntu@ip-10-0-15-223:~$ 
```

```bash
docker run --name container1 -it ubuntu bash
```
`exit`

```
ubuntu@ip-10-0-15-223:~$ docker run --name container1 -it ubuntu bash
root@7c613023d471:/# exit
exit
ubuntu@ip-10-0-15-223:~$ 
```

``` bash
docker ps -a
```
```
ubuntu@ip-10-0-15-223:~$ docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED          STATUS                      PORTS     NAMES
7c613023d471   ubuntu    "bash"    53 seconds ago   Exited (0) 39 seconds ago             container1
ubuntu@ip-10-0-15-223:~$ 
```


```
ubuntu@ip-10-0-15-223:~$ docker run --name container2 -d ubuntu top
215004f4e5f356a78b1b1f3526f5527edd75dce242266bb0d064f90dcd510b02
ubuntu@ip-10-0-15-223:~$ 
```

```
ubuntu@ip-10-0-15-223:~$ docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED          STATUS                      PORTS     NAMES
215004f4e5f3   ubuntu    "top"     28 seconds ago   Exited (1) 28 seconds ago             container2
7c613023d471   ubuntu    "bash"    3 minutes ago    Exited (0) 3 minutes ago              container1
ubuntu@ip-10-0-15-223:~$ 
```

```
ubuntu@ip-10-0-15-223:~$ docker run -d --name container2 -ti ubuntu bash 
974cbceb84c405d0cfa1d7e62a6ddaf551fca640115c0d6194833586668a1546
ubuntu@ip-10-0-15-223:~$ docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED         STATUS                     PORTS     NAMES
974cbceb84c4   ubuntu    "bash"    4 seconds ago   Up 3 seconds                         container2
7c613023d471   ubuntu    "bash"    7 minutes ago   Exited (0) 7 minutes ago             container1
ubuntu@ip-10-0-15-223:~$ 
```
```bash
docker start container1
```

```
ubuntu@ip-10-0-15-223:~$ docker start container1
container1
ubuntu@ip-10-0-15-223:~$ docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED          STATUS          PORTS     NAMES
974cbceb84c4   ubuntu    "bash"    57 seconds ago   Up 56 seconds             container2
7c613023d471   ubuntu    "bash"    8 minutes ago    Up 7 seconds              container1
ubuntu@ip-10-0-15-223:~$ 
```

```
ubuntu@ip-10-0-15-223:~$ docker stop container1
container1
ubuntu@ip-10-0-15-223:~$ docker stop container2
container2
ubuntu@ip-10-0-15-223:~$ docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED          STATUS                        PORTS     NAMES
974cbceb84c4   ubuntu    "bash"    7 minutes ago    Exited (137) 7 seconds ago              container2
7c613023d471   ubuntu    "bash"    15 minutes ago   Exited (137) 22 seconds ago             container1
ubuntu@ip-10-0-15-223:~$ 
```
<!-- 
## Building a docker image

vim Dockerfile

```
# Specify the base image
FROM alpine

```

### Adding Non-privilaged User

### DockerHub -->
