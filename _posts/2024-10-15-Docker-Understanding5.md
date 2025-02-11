---
title: Docker Commands
description: Let's learn Docker commands to build a image and to run a container.
author: DS2Man
date: 2024-10-15 11:00:00 +0000
categories: [Docker, Understanding]
tags:
  - WSL
  - Ubuntu
  - Docker
  - Command
math: true
pin: true
---

## *Docker Commands*

In the previous post, we created a Dockerfile. Now, let's build a Docker image and run a Docker container to explore various commands. Each command has different options, so it would be helpful to search or ask GPT when needed.

<!--
이전 글에서 Dockerfile을 만들어 보았다. 그걸 가지고 도커 이미지 빌드, 도커 컨테이너 실행을 해보며 다양한 명령어들을 경험해 보자. 명령어마다 다양한 옵션이 존재하므로, 필요 시 검색하거나 GPT에 물어봐서 활용하면 좋을 거 같다.
-->

|**Command**|**Description**|**Example**|
|---|---|---|
|`docker version`|Displays the installed Docker version|`docker version`|
|`docker search`|Search for Docker images in Docker Hub|`docker search mysql`|
|`docker pull`|Downloads an image from Docker Hub|`docker pull mysql:latest`|
|`docker images`|Lists locally available Docker images|`docker images`|
|`docker rmi`|Removes a Docker image|`docker rmi mysql:latest`|
|`docker run`|Creates and starts a new container|`docker run -d --restart always --name mysqldb -p 3306:3306 -v /dbdata:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=1234 mysql:latest`|
|`docker ps`|Lists running containers|`docker ps`|
|`docker ps -a`|Lists all containers, including stopped ones|`docker ps -a`|
|`docker create`|Creates a new container|`docker create --restart always --name mysqldb -p 3306:3306 -v /dbdata:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=1234 mysql:latest`|
|`docker start`|Starts a stopped container|`docker start mysqldb`|
|`docker stop`|Stops a running container|`docker stop mysqldb`|
|`docker restart`|Restarts a container|`docker restart mysqldb`|
|`docker rm`|Deletes a container|`docker rm mysqldb`|
|`docker rm -f`|Stops and Deletes a container|`docker rm -f mysqldb`|
|`docker inspect`|retrieves detailed information in JSON format.|`docker inspect mysqldb`|
|`docker exec`|Runs a command inside a running container|`docker exec -it mysqldb /bin/bash`|
|`docker logs`|Displays logs from a container|`docker logs mysqldb`|
|`docker volume ls`|Lists available Docker volumes|`docker volume ls`|
|`docker volume create`|Creates a new Docker volume|`docker volume create my_volume`|
|`docker volume rm`|Deletes a Docker volume|`docker volume rm my_volume`|
|`docker build`|Builds a Docker image from a Dockerfile|`docker build -t dockertest .`|
|`docker tag`|Adds a tag to an existing image|`docker tag dockertest ds2man/dockertest:v1`|
|`docker push`|Uploads an image to Docker Hub|`docker push ds2man/dockertest:v1`|

## *Docker Commands Practice*

- Here are some Docker commands I have executed.

```bash
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker build -t ds2man/dockertest:v0.1 .
[+] Building 1.9s (13/13) FINISHED                                                                                                                                                                                                    docker:default
 => [internal] load build definition from dockerfile                                                                                                                                                                                            0.0s
 => => transferring dockerfile: 1.58kB                                                                                                                                                                                                          0.0s
 => [internal] load metadata for docker.io/library/python:3.11-slim                                                                                                                                                                             1.7s
 => [auth] library/python:pull token for registry-1.docker.io                                                                                                                                                                                   0.0s
 => [internal] load .dockerignore                                                                                                                                                                                                               0.0s
 => => transferring context: 178B                                                                                                                                                                                                               0.0s
 => [1/7] FROM docker.io/library/python:3.11-slim@sha256:42420f737ba91d509fc60d5ed65ed0492678a90c561e1fa08786ae8ba8b52eda                                                                                                                       0.0s
 => [internal] load build context                                                                                                                                                                                                               0.0s
 => => transferring context: 2.00kB                                                                                                                                                                                                             0.0s
 => CACHED [2/7] RUN apt-get update &&     apt-get install -y vim &&     rm -rf /var/lib/apt/lists/*                                                                                                                                            0.0s
 => CACHED [3/7] WORKDIR /app                                                                                                                                                                                                                   0.0s
 => CACHED [4/7] RUN mkdir -p common config                                                                                                                                                                                                     0.0s
 => CACHED [5/7] COPY requirements.txt .                                                                                                                                                                                                        0.0s
 => CACHED [6/7] RUN pip install --no-cache-dir -r requirements.txt                                                                                                                                                                             0.0s
 => [7/7] COPY . /app/                                                                                                                                                                                                                          0.0s
 => exporting to image                                                                                                                                                                                                                          0.0s
 => => exporting layers                                                                                                                                                                                                                         0.0s
 => => writing image sha256:502c0f89c34dbe127a4e4f8bbdec3d9efe2140b8eb702365bea753e323d79a16                                                                                                                                                    0.0s
 => => naming to docker.io/ds2man/dockertest:v0.1                                                                                                                                                                                               0.0s
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker run -d --env-file=.env --name mydocker ds2man/dockertest:v0.1
79685bc8687b1d017163221da1d47fd0df1f3c9ce40a1f016295644e1ab98330
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker ps
CONTAINER ID   IMAGE                    COMMAND                  CREATED          STATUS         PORTS     NAMES
79685bc8687b   ds2man/dockertest:v0.1   "python dockertest_m…"   26 minutes ago   Up 8 seconds             mydocker
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker stop mydocker
mydocker
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker start mydocker
mydocker
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker inspect mydocker
[
    {
        "Id": "79685bc8687b1d017163221da1d47fd0df1f3c9ce40a1f016295644e1ab98330",
        "Created": "2025-02-11T11:00:50.741714182Z",
        "Path": "python",
        "Args": [
            "dockertest_main.py"
        ],
        "State": {
            "Status": "running",
            "Running": true,
........
........
]
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker exec -it mydocker /bin/bash
root@79685bc8687b:/app# exit
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker logs mydocker
2025-02-11 11:30:31 | MyDockerTest | INFO | [MainThread] Test Start
2025-02-11 11:30:31 | MyDockerTest | INFO | [MainThread] UTC Time: 2025-02-11 11:30:31+00:00, KST Time: 2025-02-11 20:30:31+09:00
2025-02-11 11:30:31 | MyDockerTest | INFO | [MainThread] MySQLDB Connect Test
2025-02-11 11:30:31 | MyDockerTest | INFO | [MainThread] DockerTest End!!
```
