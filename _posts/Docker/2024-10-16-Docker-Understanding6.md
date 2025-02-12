---
title: Container Volume
description: Let's explore the various options for the run command in Docker.
author: DS2Man
date: 2024-10-16 11:00:00 +0000
categories: [Docker, Understanding]
tags:
  - WSL
  - Ubuntu
  - Docker
  - Command
math: true
pin: true
---

## *Why should we use Docker volumes?*

Docker containers each have their own independent storage by default. If multiple containers are created from a single image, each container is allocated an independent volume (an accessible storage space with its own file system). When a container is deleted, its associated volume is also removed. The issue is that **data stored inside a container is deleted when the container itself is deleted**.    
To ensure data persistence, Docker provides **volumes** and **bind mount**. These methods maintain data persistence by separating the file system from the container. This means that even if a container is deleted and recreated, the data remains intact as long as it is connected to a Docker volume.    
Later, in a Kubernetes (K8s) environment, you will learn about **Persistent Volumes (PV)**, which function **similarly to Docker volumes**. Keep this in mind as you explore these concepts further.

|Feature|Volume|Bind Mount|
|---|---|---|
|**Data Storage Location**|Managed automatically by Docker (`/var/lib/docker/volumes/`)|Specific directory on the host|
|**Container Independence**|Data persists even after the container is deleted|Data persists even after the container is deleted|
|**Security**|Isolated from the host, providing better security|Directly uses the host directory|
|**Sharing**|Easily shareable across multiple containers|Shareable across containers but requires path specification|
|**Use Cases**|Database storage, log storage, etc.|File sharing in development environments|

<!--
도커 컨테이너는 컨테이너마다 기본적으로 독립적인 저장소를 가지고 있다. 하나의 이미지로 여러 컨테이너를 만든다면 해당 컨테이너마다 독립적인 볼륨(하나의 파일 시스템을 갖춘 접근 가능한 저장 공간)이 할당되고, 컨테이너가 삭제되면 해당 볼륨 또한 삭제된다. 문제는 **컨테이너 내부에 저장된 데이터는 컨테이너가 삭제되었을 경우 같이 삭제**된다.

그래서 도커에서는 데이터의 영속성을 보장하기 위하여 **볼륨과** **바인드 마운트**를 이용한다. **볼륨과 바인드 마운트**는 영속성을 보장하며 파일 시스템과 컨테이너를 분리하여 관리한다. 즉 컨테이너를 지웠다가 다시 실행해도 도커 볼륨과 연결한다면 데이터는 그대로 유지된다.

나중에 K8s 환경에서는 PV(Persistent Volume)에 대해서도 배울 건데, 이 방식은 볼륨과 유사하다는 점은 참고해서 이해해 보자.
-->

![Volume](/assets/img/2024-10-16-Docker-Understanding6_1.png)
_Docker volume and Bind Mount(Source: [Blog](https://deveric.tistory.com/111))_

## *Docker Volume*

- The following describes the experiment conducted to verify whether data persists after creating a Docker volume, mounting it using `run -v`, deleting, and recreating the `mydocker` container.    
	`docker volume create myvolume`    
	`docker run -d --env-file=.env --name mydocker -v myvolume:/app/log ds2man/dockertest:v0.1`

<!--
아래 내용은 docker volume 생성후 run -v을 통해 mount 시킨후 mydocker container를 삭제/재생성했을때, data가 유지되는지를 실험한 결과이다.
-->

```bash
# When you run the command `docker volume create myvolume`, Docker creates a managed volume directory. 
# The location of this directory is `/var/lib/docker/volumes/myvolume/_data`.
# You can check it using `docker volume inspect myvolume`.
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker volume create myvolume
myvolume
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker volume ls
DRIVER    VOLUME NAME
local     myvolume
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker volume inspect myvolume
[
    {
        "CreatedAt": "2025-02-11T23:22:38+09:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/myvolume/_data",
        "Name": "myvolume",
        "Options": null,
        "Scope": "local"
    }
]

# If you access `/var/lib/docker/volumes/myvolume/_data` with root privileges, you will see that it is an empty directory.
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ su -
Password: 
root@DESKTOP-B7GM3C5:~# cd /var/lib/docker/volumes/myvolume/_data
root@DESKTOP-B7GM3C5:/var/lib/docker/volumes/myvolume/_data# ls
root@DESKTOP-B7GM3C5:/var/lib/docker/volumes/myvolume/_data# exit

# If you set the `-v myvolume:/app/config` option with the `run` command, 
# the file (`MyDockerTest.log`) inside `/app/log` in the container will be mounted to the Docker volume directory `myvolume`, making it accessible.
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker run -d --env-file=.env --name mydocker -v myvolume:/app/log ds2man/dockertest:v0.1
17ba03674345b469170650c998a0032911aff27656a9a162cce5570c8292f149
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ su -
Password: 
root@DESKTOP-B7GM3C5:~# ls -lrt /var/lib/docker/volumes/myvolume/_data
total 4
-rw-r--r-- 1 root root 283 Feb 10 06:30 MyDockerTest.log
root@DESKTOP-B7GM3C5:~# cd /var/lib/docker/volumes/myvolume/_data
root@DESKTOP-B7GM3C5:/var/lib/docker/volumes/myvolume/_data# ls
MyDockerTest.log

# If you create the `ds2man.txt` file in the Docker volume, 
# then access the `mydocker` container, you will see that `ds2man.txt` exists in `/app/config/`.
root@DESKTOP-B7GM3C5:/var/lib/docker/volumes/myvolume/_data# touch ds2man.txt
root@DESKTOP-B7GM3C5:/var/lib/docker/volumes/myvolume/_data# docker exec -it mydocker /bin/bash
root@17ba03674345:/app# cd log
root@17ba03674345:/app/log# ls
ds2man.txt  MyDockerTest.log
root@17ba03674345:/app/config# exit

# Running `docker rm -f mydocker` will delete the container.
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker rm -f mydocker
mydocker
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker ps -a
CONTAINER ID   IMAGE                    COMMAND                  CREATED              STATUS              PORTS     NAMES

# Although the Docker container has been deleted, the file still exists in the Docker volume on the host OS.
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ 
root@DESKTOP-B7GM3C5:/var/lib/docker/volumes/myvolume/_data# ls
ds2man.txt  MyDockerTest.log
root@DESKTOP-B7GM3C5:/var/lib/docker/volumes/myvolume/_data# exit
logout

# If you create a new Docker container(run command) and access the inside of `mydocker2`, you will see that `ds2man.txt` still exists inside the container.
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker run -d --env-file=.env --name mydocker2 -v myvolume:/app/log ds2man/dockertest:v0.1
324a55f79003e4a34a6ef5e8ac3e109b9b2e470405afa46f0b53f8f8f3e313c3
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker exec -it mydocker2 /bin/bash
root@324a55f79003:/app# cd log
root@324a55f79003:/app/log# ls
ds2man.txt  MyDockerTest.log
root@324a55f79003:/app/log# exit

# Running `docker volume rm myvolum` will delete the docker volume(myvolume).
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker volume ls
DRIVER    VOLUME NAME
local     myvolume
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker volume rm myvolume
myvolume
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker volume ls
DRIVER    VOLUME NAME
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ 
```

## *Bind Mount*

- The following describes the experiment conducted to verify whether data persists after mounting using `run -v` without creating a Docker volume and then deleting and recreating the Docker container.      
	`docker run -d --env-file=.env --name mydocker -v /home/jaoneol/GP-MyReference/10.MyDockerTest/mydata:/app/log ds2man/dockertest:v0.1`

<!--
아래 내용은 docker volume 생성없이 run -v을 통해 mount 시킨후 docker container 삭제/생성 시 data가 유지되는지를 실험한 결과이다.
-->

```bash
# Without pre-creating `mydata` in `/home/jaoneol/GP-MyReference/10.MyDockerTest`,
# it was observed that `mydata` was created and the container started successfully when mounted using `run -v`.
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ ls
common  config  dockerfile  dockertest_main.py  log  requirements.txt  zDockerBuild.txt
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker run -d --env-file=.env --name mydocker -v /home/jaoneol/GP-MyReference/10.MyDockerTest/mydata:/app/log ds2man/dockertest:v0.1
b913d951b6e6002683f1211ed14c2a1da7ba8a86d370a71fe22405a2d7716ec2
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker ps
CONTAINER ID   IMAGE                    COMMAND                  CREATED         STATUS         PORTS     NAMES
b913d951b6e6   ds2man/dockertest:v0.1   "python dockertest_m…"   7 seconds ago   Up 7 seconds             mydocker
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ ls
common  config  dockerfile  dockertest_main.py  log  mydata  requirements.txt  zDockerBuild.txt
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ cd mydata
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest/mydata$ ls
MyDockerTest.log

# It was confirmed that the data in `mydata` persisted even after deleting the Docker container.
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest/mydata$ docker ps
CONTAINER ID   IMAGE                    COMMAND                  CREATED          STATUS          PORTS     NAMES
b913d951b6e6   ds2man/dockertest:v0.1   "python dockertest_m…"   12 minutes ago   Up 12 minutes             mydocker
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest/mydata$ docker rm -f mydocker
mydocker
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest/mydata$ ls
MyDockerTest.log
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest/mydata$ 
```