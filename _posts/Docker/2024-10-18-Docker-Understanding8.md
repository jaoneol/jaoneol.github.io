---
title: Docker Compose
description: Let's learn Docker Compose commands.
author: DS2Man
date: 2024-10-18 11:00:00 +0000
categories: [Docker, Understanding]
tags:
  - WSL
  - Ubuntu
  - Docker
  - Command
math: true
pin: true
---

## *Docker Compose file Commands*

Docker Compose is a tool that allows you to define and run multiple containers at once. To operate a single service, multiple applications need to run together. The content previously written in the `dockerfile` can be written in `docker-compose.yml`. If you are not yet fully familiar with Dockerfile commands, I recommend reviewing them again. Let's apply the newly added commands.   

<!--
docker compose는 여러 컨테이너를 일괄적으로 정의하고 실행할 수 있는 툴이다. 하나의 서비스를 운영하기 위해서는 여러개의 어플리케이션이 동작해야 한다. 이전 글에서 dockerfile 에 작성했던 내용을 docker-compose.yml에 작성하면 된다. 혹시나, 아직 dockerfile command에 대해 이해가 부족하면 다시 복습하기를 추천한다. 추가된 명령어들을 적용해 보자. 
-->

|**Category**|**Description**|**Example**|
|---|---|---|
|**version**|Specifies the Compose version. The supported syntax varies depending on the version.|`version: "3.5"`|
|**services**|Defines the container options to run using Compose.|`service:`  <br>`etcd:`  <br>    `image: quay.io/coreos/etcd:v3.5.16` <br>`minio:` <br>    `image: minio/minio:RELEASE.2023-03-20T20-16-18Z`|
|**build**|Builds the container.|`webapp:`  <br>    `build: .`|
|**image**|Specifies the image to run using Compose.|`etcd:`  <br>    `image: quay.io/coreos/etcd:v3.5.16`|
|**command**|Specifies the command to execute when running the container.|`etcd:`  <br>    `image: quay.io/coreos/etcd:v3.5.16`<br>    `command: etcd -advertise-client-urls=http://127.0.0.1:2379 -listen-client-urls `|
|**port**|Declares the ports exposed by the container.|`minio:`  <br>    `image: minio/minio:RELEASE.2023-03-20T20-16-18Z`  <br>    `port:`  <br>        `- "9001:9001"`  <br>        `- "9000:9000"`|
|**link**|Specifies the container to link when connecting to another container.|`webserver:`  <br>    `image: wordpress:latest`  <br>    `link:`  <br>        `- db:mysql`|
|**expose**|Ports exposed to linked containers.|`webapp:`  <br>    `expose:`|
|**volumes**|Mounts volumes in the container.|`etcd:`  <br>    `image: quay.io/coreos/etcd:v3.5.16`  <br>    `volumes:`  <br>         `- /var/lib/docker/volumes/etcd:/etcd`|
|**environment**|Defines environment variables to apply in the container.|`etcd:`  <br>    `image: quay.io/coreos/etcd:v3.5.16`  <br>    `environment:`  <br>        `- ETCD_AUTO_COMPACTION_MODE=revision`|
|**restart**|Restart policy applied when the container stops.  <br>- **no**: Do not restart  <br>- **always**: Always restart the container  <br>- **on-failure**: Restart only when an error occurs|`etcd:`  <br>    `image: quay.io/coreos/etcd:v3.5.16`  <br>    `restart: always`|
|**depends_on**|Defines dependencies between containers. **The specified container must start first.**|`services:`  <br>`standalone:`  <br>    `image: milvusdb/milvus:v2.5.4-gpu`  <br>    `depends_on:`  <br>        `- "etcd"`  <br>        `- "minio"`|

## *Docker Compose file Example*

The following `docker-compose.yml` file defines the installation of Milvus, a type of VectorDB, along with the necessary applications such as MinIO and Attu. I will use this as a reference for the explanation.

<!--
아래 docker-compose.yml은 vectordb의 일종인 milvus를 설치하면서 필요한 minio, attu 등의 어플리케이션도 일괄적으로 정의한 파일이다. 이것을 기준으로 설명하겠다.
-->

```yml
(base) jaoneol@DESKTOP-B7GM3C5:~$ vi docker-compose.yml
# compose version, The supported syntax varies depending on the version.
version: '3.5'

# Defines the container options to be executed via Compose.
# The services are broadly defined into four containers: `etcd`, `MinIO`, `Standalone`, and `Attu`.
services:
  etcd:
    container_name: milvus-etcd
    image: quay.io/coreos/etcd:v3.5.16
    restart: always
    environment:
      - ETCD_AUTO_COMPACTION_MODE=revision
      - ETCD_AUTO_COMPACTION_RETENTION=1000
      - ETCD_QUOTA_BACKEND_BYTES=4294967296
      - ETCD_SNAPSHOT_COUNT=50000
    volumes:
      - ${DOCKER_VOLUME_DIRECTORY:-.}/volumes/etcd:/etcd
    command: etcd -advertise-client-urls=http://127.0.0.1:2379 -listen-client-urls http://0.0.0.0:2379 --data-dir /etcd
    healthcheck:
      test: ["CMD", "etcdctl", "endpoint", "health"]
      interval: 30s
      timeout: 20s
      retries: 3

  minio:
    container_name: milvus-minio
    image: minio/minio:RELEASE.2023-03-20T20-16-18Z
    restart: always
    environment:
      MINIO_ACCESS_KEY: minioadmin
      MINIO_SECRET_KEY: minioadmin
    ports:
      - "9001:9001"
      - "9000:9000"
    volumes:
      - ${DOCKER_VOLUME_DIRECTORY:-.}/volumes/minio:/minio_data
    command: minio server /minio_data --console-address ":9001"
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:9000/minio/health/live"]
      interval: 30s
      timeout: 20s
      retries: 3

  standalone:
    container_name: milvus-standalone
    image: milvusdb/milvus:v2.5.4-gpu
    restart: always
    command: ["milvus", "run", "standalone"]
    security_opt:
    - seccomp:unconfined
    environment:
      ETCD_ENDPOINTS: etcd:2379
      MINIO_ADDRESS: minio:9000
    volumes:
      - ${DOCKER_VOLUME_DIRECTORY:-.}/volumes/milvus:/var/lib/milvus
    ports:
      - "19530:19530"
      - "9091:9091"
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              capabilities: ["gpu"]
              device_ids: ["0"]
    depends_on:
      - "etcd"
      - "minio"

  attu:
    container_name: attu
    image: zilliz/attu:v2.4.7
    restart: always
    environment:
      MILVUS_URL: milvus-standalone:19530
    ports:
      - "8000:3000"
    depends_on:
      - "standalone"

networks:
  default:
    name: milvus
```

## *Docker Compose Commands*

The following `docker-compose.yml` file defines the installation of Milvus, a type of VectorDB, along with the necessary applications such as MinIO and Attu. I will use this as a reference for the explanation.

<!--
아래 docker-compose.yml은 vectordb의 일종인 milvus를 설치하면서 필요한 minio, attu 등의 어플리케이션도 일괄적으로 정의한 파일이다. 이것을 기준으로 설명하겠다.
-->


| **Command**                     | **Description**                                      | **Example**            |
| ------------------------------- | ---------------------------------------------------- | ---------------------- |
| `docker compose up -d`          | Starts services defined in `docker-compose.yml`      | `docker compose up -d` |
| `docker compose down`           | Stops and removes Compose services                   | `docker compose down`  |
| `docker compose down --volumes` | Stops and removes Compose services including volumes | `docker compose down`  |
| `docker compose ps`             | Lists running Compose services                       | `docker compose ps`    |
| `docker compose logs`           | Displays logs from Compose services                  | `docker compose logs`  |
| `docker compose build`          | Builds images for Compose services                   | `docker compose build` |

```bash
(base) jaoneol@DESKTOP-B7GM3C5:~$ sudo docker compose up -d
[sudo] password for jaoneol:
[+] Running 4/4
 ✔ Container milvus-etcd        Started                                                                                                                                                                                              0.5s 
 ✔ Container milvus-minio       Started                                                                                                                                                                                              0.4s 
 ✔ Container milvus-standalone  Started                                                                                                                                                                                              0.7s 
 ✔ Container attu               Started                                                                                                                                                                                              1.0s 
(base) jaoneol@DESKTOP-B7GM3C5:~$ docker compose ps
NAME                IMAGE                                      COMMAND                  SERVICE      CREATED         STATUS                   PORTS
attu                zilliz/attu:v2.4.7                         "docker-entrypoint.s…"   attu         6 minutes ago   Up 2 minutes             0.0.0.0:8000->3000/tcp, [::]:8000->3000/tcp
milvus-etcd         quay.io/coreos/etcd:v3.5.5                 "etcd -advertise-cli…"   etcd         6 minutes ago   Up 2 minutes (healthy)   2379-2380/tcp
milvus-minio        minio/minio:RELEASE.2023-03-20T20-16-18Z   "/usr/bin/docker-ent…"   minio        6 minutes ago   Up 2 minutes (healthy)   0.0.0.0:9000-9001->9000-9001/tcp, :::9000-9001->9000-9001/tcp
milvus-standalone   milvusdb/milvus:v2.3.3                     "/tini -- milvus run…"   standalone   6 minutes ago   Up 2 minutes (healthy)   0.0.0.0:9091->9091/tcp, :::9091->9091/tcp, 0.0.0.0:19530->19530/tcp, :::19530->19530/tcp
(base) jaoneol@DESKTOP-B7GM3C5:~$ docker compose down --volumes
[+] Running 5/5
 ✔ Container attu               Removed                                                                                                                                                                                                                                                     0.3s 
 ✔ Container milvus-standalone  Removed                                                                                                                                                                                                                                                     0.4s 
 ✔ Container milvus-minio       Removed                                                                                                                                                                                                                                                     1.0s 
 ✔ Container milvus-etcd        Removed                                                                                                                                                                                                                                                     0.4s 
 ✔ Network milvus               Removed                                                                                                                                                                                                                                                     0.4s 
(base) jaoneol@DESKTOP-B7GM3C5:~$ 

```