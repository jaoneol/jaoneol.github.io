---
title: Dockerfile, Docker Image, Docker Container
description: Let's write a Dockerfile and build a image and run a container.
author: DS2Man
date: 2024-10-13 11:00:00 +0000
categories: [Docker, Understanding]
tags:
  - WSL
  - Ubuntu
  - Docker
  - Command
math: true
pin: true
---

## *Dockerfile, Docker Image, Docker Container*

- It could be difficult to comprehend all the terms if you are new to Docker. Many Docker terminology can signify various things to different people, and they are occasionally used in diverse contexts. For instance, you could be curious about the differences between a Docker image and a Docker container, much like other people who are learning about Docker. They differ in a modest but important way.    
- The differences between Docker images and containers, and docker files will be covered in this article, which will also explain how and when to utilize each.

![Dockerfile, Image, Container](/assets/img/2024-10-12-Docker-Understanding3_1.png)
_Dockerfile, Docker Image, Docker Container_

## *Write a [Dockerfile]*

- A Dockerfile is **a file written for the purpose of creating an image in Docker**.  It can be considered a **template** that specifies information about the image to be built.  After writing a Dockerfile based on various commands and building it, Docker sequentially executes the instructions listed in the Dockerfile to create a Docker image.

<!--
https://hstory0208.tistory.com/entry/Docker-dockerfile의-개념과-작성법에-대해-알아보자
도커파일은 **docker 에서 이미지를 생성하기 위한 용도로 작성하는 파일**이다.
만들 이미지에 대한 정보를 기술해 둔 템플릿(template) 이라고 보면 된다.
여러가지 명령어를 토대로 Dockerfile을 작성한 후 빌드면 Docker는 Dockerfile에 나열된 명령문을 차례대로 수행하며 DockerImage를 생성한다.
-->

```bash
# This refers to the image that will serve as the foundation for the image being created.  
# If the image is not available locally, it will be pulled from Docker Hub.
FROM python:3.11-slim

# Install required packages (optimize apt and clean up)
# Execute commands in a new layer and create a new image.  
# (Each RUN command creates a single image.)
RUN apt update && apt install -y vim && rm -rf /var/lib/apt/lists/*

# Set the working directory first
# Specify the working directory. If the directory does not exist, it will be created.  
Once the working directory is set, all subsequent commands operate relative to this directory
WORKDIR /app

# Create required directories
RUN mkdir -p Common Config

# Set Python environment variable to prevent buffering
# When you want a Python application running inside a Docker container to output logs immediately.
ENV PYTHONUNBUFFERED=1 

# Copy only requirements.txt first to leverage Docker cache
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy the rest of the application files
COPY . /app/

# Set the entry point
ENTRYPOINT ["python", "dockertest_main.py"]
```

- The **.dockerignore** file specifies which files should be excluded when building an image with a Dockerfile (similar to **.gitignore**).  I configured the **.dockerignore** file as follows to exclude files unrelated to production when building the image.

<!--
**.dockerignore**파일은 Dockerfile로 이미지를 빌드할 때 어떤 파일을 제외시킬 것인지를 명시하는 파일입니다.(.gitignore와 유사함)
**.dockerignore**파일을 다음과 같이 구성하여 프로덕션과 관련없는 파일들을 이미지를 빌드할 때 제외시켜 주었습니다.
-->

```bash
##################
# vi .dockerignore
##################
.gitignore
.dockerignore
.flake8
.env
zDockerBuild.txt
```

## *Build a [Docker Image]*

- When creating a Docker image, you can enter the following command, and the image build process will start based on the contents of the Dockerfile.     
	`docker build -t {image_name:version} {path_to_dockerfile}`    
	`docker build -t ds2man/dockertest:v0.1 .`   

<!--
도커 이미지를 만들 때 아래와 같이 명령어를 입력하면 작성한 도커파일의 내용을 기반으로 이미지 빌드가 시작된다.
-->

```bash
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker build -t ds2man/dockertest:v0.1 .
[+] Building 11.5s (14/14) FINISHED                                                                                                                                                                                                                  docker:default
 => [internal] load build definition from dockerfile                                                                                                                                                                                                           0.0s
 => => transferring dockerfile: 818B                                                                                                                                                                                                                           0.0s
 => [internal] load metadata for docker.io/library/python:3.11-slim                                                                                                                                                                                            1.7s
 => [auth] library/python:pull token for registry-1.docker.io                                                                                                                                                                                                  0.0s
 => [internal] load .dockerignore                                                                                                                                                                                                                              0.0s
 => => transferring context: 2B                                                                                                                                                                                                                                0.0s
 => [1/8] FROM docker.io/library/python:3.11-slim@sha256:42420f737ba91d509fc60d5ed65ed0492678a90c561e1fa08786ae8ba8b52eda                                                                                                                                      0.0s
 => [internal] load build context                                                                                                                                                                                                                              0.0s
 => => transferring context: 877B                                                                                                                                                                                                                              0.0s
 => CACHED [2/8] RUN apt update && apt install -y vim                                                                                                                                                                                                          0.0s
 => CACHED [3/8] RUN mkdir -p /app /app/Common /app/Config                                                                                                                                                                                                     0.0s
 => CACHED [4/8] WORKDIR /app                                                                                                                                                                                                                                  0.0s
 => [5/8] COPY [*.py, *.txt, ./]                                                                                                                                                                                                                               0.0s
 => [6/8] COPY [common/*.py, common/]                                                                                                                                                                                                                          0.0s
 => [7/8] COPY [config/*.yml, config/]                                                                                                                                                                                                                         0.0s
 => [8/8] RUN pip install -r requirements.txt                                                                                                                                                                                                                  9.0s
 => exporting to image                                                                                                                                                                                                                                         0.6s 
 => => exporting layers                                                                                                                                                                                                                                        0.5s 
 => => writing image sha256:d2bf3aae2280c9aaf54db484aefd65f3274c65ca739611b989a12de1febcb489                                                                                                                                                                   0.0s 
 => => naming to docker.io/ds2man/dockertest:v0.1                                                                                                                                                                                                              0.0s 
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker images
REPOSITORY            TAG                            IMAGE ID       CREATED          SIZE                                                                                                                                                                           
ds2man/dockertest     v0.1                           d2bf3aae2280   36 seconds ago   440MB
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ su -
Password: 
# the location where image layers are stored is `/var/lib/docker/overlay2`.    
# This is where Docker's Union File System (OverlayFS) layers are stored.
# For reference, based on the Dockerfile, there should be a total of 6 layers(1 `FROM`, 3 `RUN`, and 2 `COPY`). 
# However, it was observed that a total of 13 layers were added.
# Here’s why:
# 1. Docker Uses Additional Metadata Layers
# 2. Multiple Layer IDs for a Single `RUN`
# 3. Cache and Intermediate Layers
# 4. Base Image (Python 3.11-slim) May Have Multiple Layers
The number of directories in **/var/lib/docker/overlay2/** does not always directly correspond to the number of layers in your Docker image. Here’s why:
root@DESKTOP-B7GM3C5:~# cd /var/lib/docker/overlay2
root@DESKTOP-B7GM3C5:/var/lib/docker/overlay2# ls -lrt
total 56
drwx--x--- 3 root root 4096 Feb 10 23:28 x7x9g5ma755hstt5pa9lsesbe
drwx--x--- 3 root root 4096 Feb 10 23:29 uhov30mxr5oouxlwdfeu5vndm
drwx--x--- 3 root root 4096 Feb 10 23:29 q7ej3cmtyuqadsj9g7rsklrpn
drwx--x--- 3 root root 4096 Feb 10 23:29 bba9b62b6e19449cf185100b6435349ea98942de2ff8f30415909d62aadb5cc7
drwx--x--- 4 root root 4096 Feb 10 23:29 1b92b82ac65573f09370464bc93b0494317c67a2e1d53fd39d348e6119d5b153
drwx--x--- 4 root root 4096 Feb 10 23:29 d90cbc202fcbb29ca8558e1d5a8fd601751739eb65939aadf0a217f2f587630d
drwx--x--- 4 root root 4096 Feb 10 23:29 d634bb397fb296e81aa3496e2ca71fb7e84a86e45ca4b135e4b280c08609ad6f
drwx--x--- 4 root root 4096 Feb 10 23:29 yc8cnrxe527q5ity9rsgvtaha
drwx--x--- 4 root root 4096 Feb 10 23:29 rgqlm10jleb879npij8e6iiqh
drwx--x--- 4 root root 4096 Feb 10 23:29 h2tl0wo9rypf4txvet5gq830r
drwx--x--- 4 root root 4096 Feb 10 23:29 st0t93ceil3umbwnl9t0awb4c
drwx------ 2 root root 4096 Feb 10 23:29 l
drwx--x--- 4 root root 4096 Feb 10 23:29 migrpzts9fsudtavuf44z3pd4
drwx--x--- 4 root root 4096 Feb 10 23:29 h0mfkmt88jzso9nbhjyu5mnot
root@DESKTOP-B7GM3C5:/var/lib/docker/overlay2# 
```

## *Run a [Docker Container]*

- Let's run the container using the following command.    
	`docker run -d --env-file={environment variable file} --name {container_name} {image_name:version}`    
	`docker run -d --env-file=.env --name mydocker ds2man/dockertest:v0.1`

- To access a Docker container and inspect its structure, run the following command.    
	`docker exec -it {container_name} /bin/bash`    
	`docker exec -it mydocker /bin/bash`

```bash
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker run -d --env-file=.env --name mydocker ds2man/dockertest:v0.1
7a82d4dd52c5e78b14af53a5f28ba0c1b4a5cbdd74a1fb8a8b6ccb9347701381
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker ps
CONTAINER ID   IMAGE                    COMMAND                  CREATED          STATUS          PORTS     NAMES
0558e84e3e9e   ds2man/dockertest:v0.1   "python dockertest_m…"   19 seconds ago   Up 18 seconds             mydocker
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ 
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker exec -it mydocker /bin/bash
root@7a82d4dd52c5:/app# ls
common  config  dockerfile  dockertest_main.py  log  requirements.txt
root@7a82d4dd52c5:/app# 
```
