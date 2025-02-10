---
title: Docker Image Layers
description: Let's Understand the Features of Docker Image Layers.
author: DS2Man
date: 2024-10-12 11:00:00 +0000
categories: [Docker, Understanding]
tags:
  - WSL
  - Ubuntu
  - Docker
  - Command
math: true
pin: true
---

## *Structure of a Docker Image*

- A Docker image consists of layers, with each layer being read-only. <ins>On top of these layers, a container-specific read/write layer is added **when the container runs.**</ins> All data written by the container is stored in this writable layer.  **The read-only layers are shared among all containers. Therefore, running multiple containers from the same image results in identical containers.** This is also the reason why the image is not downloaded again when running the same image multiple times.   

|**Category**|**Image Layer**|**Container Layer**|
|---|---|---|
|**Permissions**|Read-Only|Read-Write|
|**Creation Time**|Created during `docker build`|Created during `docker run`|
|**Persistence**|Persistent (remains until image is deleted)|Temporary (removed when the container is deleted)|
|**Sharing**|Shared across multiple containers|Unique to each container|
|**Storage Location**|Docker image storage (Registry or Local)|Host machine's filesystem (OverlayFS, AUFS, etc.)|

<!--
Docker image는 layer로 이루어져 있으며, 각 layer 계층은 모두 Read only로 구성됩니다.
그 후 마지막 layer 위에 Container read/write 부분의 layer가 얹어지고 실행됩니다.
이 layer에 Container에서 write된 모든 데이터들이 저장됩니다.
각 Read only layer들은 모든 Container가 공유하게 됩니다.
따라서 같은 image를 사용해 run을 하면 모두 동일한 Container를 얻을 수 있게 되는 것입니다.
또한 동일한 image를 run할 때 image를 새로 받아오지 않는 이유도 다 이 때문입니다.
-->

![Containers and Layers](/assets/img/2024-10-12-Docker-Understanding2_1.png)
_Docker containers and layers(Source: [Docker Docs](https://docs.docker.com/engine/storage/drivers/))_

## *Pull Docker Image*

- I will fetch the MySQL image from the [Docker Hub](https://hub.docker.com/) and run a Docker container.  I will cover the Docker commands in the next post, so for now, it's best to focus on understanding the concept. For reference, the location where image layers are stored is `/var/lib/docker/overlay2`.    
	`docker pull mysql`      
	`docker images`      

<!--
Docker registry에서 mysql image를 가져와 Docker Container를 실행시켜 볼께. Docker Command는 다음 글에서 작성할 예정이니, 우선은 개념부터 파악하면 좋다.
참고로 Image Layers 가 저장되는 장소는  /var/lib/docker/overlay2 이다.
-->

```bash
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker pull mysql
Using default tag: latest
latest: Pulling from library/mysql
# MySQL consists of a total of 10 image layers.
4473ac30a868: Pull complete 
1e59b1e9b7fc: Pull complete 
bdf87f630bc8: Pull complete 
886200dd5e38: Pull complete 
b49812044423: Pull complete 
b48319207fe2: Pull complete 
e4312bbe95c6: Pull complete 
7dd0a00427a8: Pull complete 
2f4e973a399a: Pull complete 
274b57fed122: Pull complete 
Digest: sha256:d56d039139a7f3b71f6d1c9f07ca4ee9f977b0fca13acdd27a1b13bfd4a4e3be
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker images
REPOSITORY            TAG                            IMAGE ID       CREATED         SIZE
mysql                 latest                         3e34946bc4c4   2 weeks ago     797MB
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ su -
Password: 
# the location where image layers are stored is `/var/lib/docker/overlay2`.    
# This is where Docker's Union File System (OverlayFS) layers are stored.
root@DESKTOP-B7GM3C5:~# cd  /var/lib/docker/overlay2
root@DESKTOP-B7GM3C5:/var/lib/docker/overlay2# ls -lrt
total 44
drwx--x--- 3 root root 4096 Feb 10 22:12 9e3bf5200687a5e0ccb88af7ad4ee06893adcf097443424df44eb07f723cdece
drwx--x--- 4 root root 4096 Feb 10 22:12 1b391626a13451c3c38a9a3947134df5e5c57174bf03cc9ea113464048b810cb
drwx--x--- 4 root root 4096 Feb 10 22:12 fdf593cba5d5614d199f03419d81aa88a11beaaf7e4009d4b244aa9ff4a19652
drwx--x--- 4 root root 4096 Feb 10 22:12 81a1805bdb968fe3491f60aade77e61dcd1d654007f152632fa09eba9dc86051
drwx--x--- 4 root root 4096 Feb 10 22:12 1cc95ee7a44b4c9e10cb25c0a9369bcb99f958bdbb05c5945c73590b27b577d6
drwx--x--- 4 root root 4096 Feb 10 22:12 9590135d338dd622929c2603358e61b1becf5e422a272d45c119dd86d6fb4409
drwx--x--- 4 root root 4096 Feb 10 22:12 3d48337bc6e8edbde090848414c8fecf99598c56e39ff9ef9eee5f9d67ec11df
drwx--x--- 4 root root 4096 Feb 10 22:12 a10dca5a7925633e6004d704d587af121d913cc8aea68992e50ce5b901b1da8a
drwx------ 2 root root 4096 Feb 10 22:12 l
drwx--x--- 4 root root 4096 Feb 10 22:12 35f35e341cadf7df8479528f63351b07cb31cb04cf35485626c0ae4c3a084947
drwx--x--- 4 root root 4096 Feb 10 22:12 1167f9760815c65b52d8cd335ff28992c6885326cf1cd7c9a2dfc7c6c0b4247d
root@DESKTOP-B7GM3C5:/var/lib/docker/overlay2# 
```

- The basic principles for calculating the number of Docker image layers are as follows:    
	`FROM` → Provides the base image layer for the new image.    
	`RUN` → Creates a new layer each time it is executed.    
	`COPY` and `ADD` → Create a new layer each time a file is copied.    
	`ENV`, `VOLUME`, `EXPOSE`, `CMD`, `ENTRYPOINT` → **Do not create new layers** (only modify metadata).

<!--
Docker Image Layer의 수 계산의 기본 윈칙은 다음과 같디ㅏ.
- `FROM` → 새로운 이미지의 기본 레이어를 제공
- `RUN` → 실행될 때마다 새로운 레이어를 생성
- `COPY` 및 `ADD` → 파일이 복사될 때마다 새로운 레이어를 생성
- `ENV`, `VOLUME`, `EXPOSE`, `CMD`, `ENTRYPOINT` → **새로운 레이어를 생성하지 않음** (메타데이터 변경만 수행)
-->

- When searching for MySQL on Docker Hub, you can see various versions available.  
Clicking on **latest** redirects you to the [Dockerfile](https://github.com/docker-library/mysql/blob/df3a5c483a5e8c3c4d1eae61678fa5372c403bf0/innovation/Dockerfile.oracle), and upon closer inspection, you can see that it consists of a total of **10 image layers**, including `1 FROM (Base Image)`, `8 RUN`, and `1 COPY` commands.

<!--
Docker Hub에서 mysql 검색 시 다양한 버전을 확인할 수 있다. latest를 클릭시, dockerfile로 연결되고, 내용을 자세히 보면 FROM 1개(Base Image), RUN 7개, ENV 3개로 총 11개 Image Layer로 구성됨을 확인할수 있다.
-->

|Command|Description|Layer Count|
|---|---|---|
|`FROM oraclelinux:9-slim`|Base image|1|
|`RUN set -eux; groupadd --system ...`|Create MySQL group and user|2|
|`RUN set -eux; arch="$(uname -m)"; curl ... install gosu`|Download and install gosu|3|
|`RUN set -eux; microdnf install -y bzip2 ...`|Install essential packages|4|
|`RUN set -eux; gpg --batch --recv-keys ...`|Register MySQL GPG key|5|
|`RUN set -eu; { echo '[mysqlinnovation-server-minimal]'; ...}`|Add MySQL repository|6|
|`RUN set -eux; microdnf install -y "mysql-community-server-minimal-$MYSQL_VERSION"`|Install MySQL server package|7|
|`RUN set -eu; { echo '[mysql-tools-community]'; ...}`|Add MySQL Tools repository|8|
|`RUN set -eux; microdnf install -y "mysql-shell-$MYSQL_SHELL_VERSION"`|Install MySQL Shell|9|
|`COPY docker-entrypoint.sh /usr/local/bin/`|Copy file|10|

![Dockerfile](/assets/img/2024-10-12-Docker-Understanding2_2.png)
_Dockerfile in Dockerhub(Source: [Dockerhub](https://hub.docker.com), [Dockerfile](https://github.com/docker-library/mysql/blob/df3a5c483a5e8c3c4d1eae61678fa5372c403bf0/innovation/Dockerfile.oracle))_

## *Run Docker Container*

- Let's run the container using the following command. As mentioned earlier, I will be writing about Docker commands in the next post, so for now, it would be good to first understand the concepts.    
	`docker run -d --restart always --name mysql -v /dbdata:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=1234 -d -p 3306:3306 mysql:latest`

```bash
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker run -d --restart always --name mysql -e MYSQL_ROOT_PASSWORD=1234 -d -p 3306:3306 mysql:latest
81218b12c66963e0d014a6d0f98691c3d30ead6b7cd6724725fd043a826aae48
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS                                                  NAMES
81218b12c669   mysql:latest   "docker-entrypoint.s…"   6 seconds ago   Up 5 seconds   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   mysql
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ 
```

## *Stop and delete Docker Container*

- Let's stop the Docker container and try deleting both the container and the image.    
	`docker rm -f mysql`    
	`docker stop mysql`    
	`docker rm mysql`,     
	`docker ps`, `docker ps -a`    
	`docker rmi mysql`    
 
```bash
# docker rm -f mysql = docker stop mysql + docker rm mysql 
# Stop mysql container
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker stop mysql
mysql
# Lists running containers
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
# Lists all containers, including stopped ones
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker ps -a
CONTAINER ID   IMAGE                                      COMMAND                  CREATED              STATUS                      PORTS                                                                                      NAMES
81218b12c669   mysql:latest                               "docker-entrypoint.s…"   About a minute ago   Exited (0) 4 seconds ago                                                                                               mysql
# Deletes a mysql container
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker rm mysql
mysql
# Lists all containers, including stopped ones
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker ps -a
CONTAINER ID   IMAGE                                      COMMAND                  CREATED      STATUS                      PORTS                                                                                      NAMES
# Removes a Docker image
# From the output below, you can see that a total of 11 layers have been deleted.
# The output of 'docker rmi` may include image metadata (Untagged), unused intermediate layers, and hidden layers related to the base image.
# So, the output may show 11 layers being deleted instead of 10.
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker rmi mysql
Untagged: mysql:latest
Untagged: mysql@sha256:d56d039139a7f3b71f6d1c9f07ca4ee9f977b0fca13acdd27a1b13bfd4a4e3be
Deleted: sha256:3e34946bc4c418b9083622074deea6115aebefb4557d998897162bea8433f083
Deleted: sha256:c52f6c78f1ac0ed65d42f6e9879b028aa9f566e43d72b3e36cec5b12d7f47659
Deleted: sha256:4ac067d6c41dd29b5a48de465f9aed3e4c29a260e91b0eb79c4e518c705c2ec4
Deleted: sha256:585b28e3e28079b12f40eb5816b3ae573fd51ff38b25fc2884fb478bf929e528
Deleted: sha256:0fd5816edfba7adca6bb85f6ac690de80e36c6ced10026ee9d4ca6fc268ef842
Deleted: sha256:408b802e8842f8a5c4919b5464b120b2724caabf6c7d89578824071acad3bdbf
Deleted: sha256:7eae367086c6e824068c836f9d90aa5ee5a778df661e6240e6d5f6fb35f2a962
Deleted: sha256:353563b0227fd02e3fb91d242fe640764b4ea074d9cb67174c43811d1ded240e
Deleted: sha256:b9cd9d01c64a4184b7d1be1dce09049cc1ff2e3038ad0accf8ae2b1509f52d1d
Deleted: sha256:765b980c0cd3298d3756c10e33187588411d09a8ab303848af90b6fc4b3743cf
Deleted: sha256:d0c9e1d9df6f4a64dc0e3484c0dedae0ee5a97128c8b198bf4a67e9bb5077f58
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker images
REPOSITORY            TAG                            IMAGE ID       CREATED         SIZE
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ 
```

## *How to Delete Image Layers*

- As you know, **/var/lib/docker/overlay2** is where Docker's Union File System (OverlayFS) layers are stored. Do not manually delete **/var/lib/docker/overlay2** under any circumstances! Since it is a storage location used by Docker, deleting it manually may cause Docker to malfunction. Let's delete image layers using the following command. If the purpose of deleting files is simply for cleanup, <ins>**`docker system prune -af --volumes`**</ins> should be sufficient.   
	
	1. Stop and Remove Running Containers    
	   	`docker ps -q | xargs docker stop`   
	   	`docker ps -aq | xargs docker rm`    
	2. Remove Unused Images    
	   	`docker images -q | xargs docker rmi -f`   
	3. Remove Unused Volumes    
	   	`docker volume prune -f`,     
	4. Remove Unused Networks    
	   	`docker network prune -f`    
	5. (Recommend)Clean Up Docker System Cache    
	   	`docker system prune -af --volumes`    

<!--
아시다시피 ' /var/lib/docker/overlay2' 는 Docker의 Union File System (OverlayFS) 레이어가 저장되는 곳이다. 무조건 /var/lib/docker/overlay2를 수동으로 직접 삭제하지 마세요! Docker가 사용하는 저장소이기 때문에, 직접 삭제하면 Docker가 정상적으로 동작하지 않을 수 있습니다.
파일을 삭제하는 목적이 단순한 정리라면, docker system prune -af --volumes 만으로도 충분합니다.
-->