---
title: MySQL, Milvus Installation and Configuration
description: Let's install MySQL, Milvus which are necessary for the LLM service(DS2Man).
author: DS2Man
date: 2025-02-01 17:00:00 +0000
categories:
  - Project-DS2Man
tags:
  - DS2Man
math: true
pin: true
---

Let's install MySQL, Milvus which are necessary for the LLM service(DS2Man).

<!--
Let's install MySQL, Milvus which are necessary for the LLM service(DS2Man).
-->

## *Install MySQL on Ubuntu*

- MySQL    
	`docker pull mysql`        
	`docker volume create mysql_volume`    
	`docker run -d --restart always --name mysql -v mysql_volume:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=1234 -p 3306:3306 mysql:latest`      

- Step1) Pull the MySQL image.
```bash
(base) jaoneol@DESKTOP-B7GM3C5:~$ docker pull mysql
Using default tag: latest
latest: Pulling from library/mysql
2c0a233485c3: Pull complete
21577e00f2ba: Pull complete
c294da35c13e: Pull complete
facc8f3107c1: Pull complete
de4342aa4ad8: Pull complete
4643f1cf56c2: Pull complete
139aca660b47: Pull complete
b10e1082570e: Pull complete
26313a3e0799: Pull complete
d43055c38217: Pull complete
Digest: sha256:45f5ae20cfe1d6e6c43684dfffef17db1e1e8dc9bf7133ceaafb25c16b10f31b
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest
(base) jaoneol@DESKTOP-B7GM3C5:~$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
mysql        latest    a52cba19e8cc   13 days ago   797MB
```

-  Step2) Create a Docker volume
```bash
# When you run the command `docker volume create mysql_volume`, Docker creates a managed volume directory. 
# The location of this directory is `/var/lib/docker/volumes/mysql_volume/_data`.
# You can check it using `docker volume inspect mysql_volume`.
(base) jaoneol@DESKTOP-B7GM3C5:~$ docker volume create mysql_volume
mysql_volume
(base) jaoneol@DESKTOP-B7GM3C5:~$ docker volume ls
DRIVER    VOLUME NAME
local     mysql_volume
(base) jaoneol@DESKTOP-B7GM3C5:~$ docker volume inspect mysql_volume
[
    {
        "CreatedAt": "2025-02-12T22:57:40+09:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/mysql_volume/_data",
        "Name": "mysql_volume",
        "Options": null,
        "Scope": "local"
    }
]
(base) jaoneol@DESKTOP-B7GM3C5:~$ 
```

-  Step3) Run the MySQL container
```bash
# docker run -d --name mysql -v mysql_volume:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=1234 -p 3306:3306 mysql:latest
# docker update --restart always mysql # No need to run `docker stop mysql`. The update takes effect immediately.
# docker start mysql
(base) jaoneol@DESKTOP-B7GM3C5:~$ docker run -d --restart always --name mysql -v mysql_volume:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=1234 -p 3306:3306 mysql:latest
1b98f129501345342c6c12c7d2a8b1629a27f6175aca3809de1bfa3f0ee3c322
(base) jaoneol@DESKTOP-B7GM3C5:~$ docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS                                                  NAMES
1b98f1295013   mysql:latest   "docker-entrypoint.s…"   29 seconds ago   Up 28 seconds   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   mysql
# If you navigate to `/var/lib/docker/volumes/mysql_volume/_data`, 
# you can see that all the files in the MySQL container's `/var/lib/mysql` directory are mounted there.
(base) jaoneol@DESKTOP-B7GM3C5:~$ su 
Password: 
root@DESKTOP-B7GM3C5:/home/jaoneol# cd /var/lib/docker/volumes/mysql_volume/_data
root@DESKTOP-B7GM3C5:/var/lib/docker/volumes/mysql_volume/_data# ls
'#ib_16384_0.dblwr'  '#innodb_redo'   auto.cnf        binlog.000002   ca-key.pem   client-cert.pem   ib_buffer_pool   ibtmp1   mysql.ibd    mysql_upgrade_history   private_key.pem   server-cert.pem   sys        undo_002
'#ib_16384_1.dblwr'  '#innodb_temp'   binlog.000001   binlog.index    ca.pem       client-key.pem    ibdata1          mysql    mysql.sock   performance_schema      public_key.pem    server-key.pem    undo_001
root@DESKTOP-B7GM3C5:/var/lib/docker/volumes/mysql_volume/_data# exit
exit
(base) jaoneol@DESKTOP-B7GM3C5:~$
```

## *Install Milvus on Ubuntu(Ing..)*

- Milvus    
	`wget https://github.com/milvus-io/milvus/releases/download/v2.3.3/milvus-standalone-docker-compose.yml -O docker-compose.yml`     
	`vi docker-compose.yml(adding attu)`    
	`echo 'export DOCKER_VOLUME_DIRECTORY=/var/lib/docker/volumes/milvus_volume' >> ~/.bashrc`    
	`sudo docker compose up -d`     
	`sudo docker compose ps`   
	`docker compose down --volumes` or `docker compose down`

<!--
https://do-hyeon.tistory.com/entry/Milvus-Milvus란-M1-Mac-Milvus-개발환경-구성하기
https://devocean.sk.com/blog/techBoardDetail.do?ID=165368
https://www.youtube.com/watch?v=K0ZayH0n7sI&t=65s
https://blog.csdn.net/qq_34146694/article/details/132658533
https://jackerlab.com/milvus-attu-docker-install/
-->

```bash
(base) jaoneol@DESKTOP-B7GM3C5:~$ wget https://github.com/milvus-io/milvus/releases/download/v2.5.4/milvus-standalone-docker-compose.yml -O docker-compose.yml
--2025-02-06 01:12:19--  https://github.com/milvus-io/milvus/releases/download/v2.5.4/milvus-standalone-docker-compose.yml
Resolving github.com (github.com)... 20.200.245.247
Connecting to github.com (github.com)|20.200.245.247|:443... connected.
HTTP request sent, awaiting response... 302 Found
Location: https://objects.githubusercontent.com/github-production-release-asset-2e65be/208728772/a8ed256e-9092-4694-a8fe-909c8e0a7018?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=releaseassetproduction%2F20250205%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20250205T161219Z&X-Amz-Expires=300&X-Amz-Signature=b1e5ce2386a701d3381910e3c8ff2688e9eb6f0e59030011e04ad2eb3ff743fa&X-Amz-SignedHeaders=host&response-content-disposition=attachment%3B%20filename%3Dmilvus-standalone-docker-compose.yml&response-content-type=application%2Foctet-stream [following]
--2025-02-06 01:12:19--  https://objects.githubusercontent.com/github-production-release-asset-2e65be/208728772/a8ed256e-9092-4694-a8fe-909c8e0a7018?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=releaseassetproduction%2F20250205%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20250205T161219Z&X-Amz-Expires=300&X-Amz-Signature=b1e5ce2386a701d3381910e3c8ff2688e9eb6f0e59030011e04ad2eb3ff743fa&X-Amz-SignedHeaders=host&response-content-disposition=attachment%3B%20filename%3Dmilvus-standalone-docker-compose.yml&response-content-type=application%2Foctet-stream
Resolving objects.githubusercontent.com (objects.githubusercontent.com)... 185.199.109.133, 185.199.110.133, 185.199.108.133, ...
Connecting to objects.githubusercontent.com (objects.githubusercontent.com)|185.199.109.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 1767 (1.7K) [application/octet-stream]
Saving to: ‘docker-compose.yml’

docker-compose.yml                               100%[=======================================================================================================>]   1.73K  --.-KB/s    in 0s

2025-02-06 01:12:19 (18.1 MB/s) - ‘docker-compose.yml’ saved [1767/1767]

(base) jaoneol@DESKTOP-B7GM3C5:~$ ls
docker-compose.yml  miniconda3
```

```yml
(base) jaoneol@DESKTOP-B7GM3C5:~$ vi docker-compose.yml
version: '3.5'

services:
  etcd:
    container_name: milvus-etcd
    image: quay.io/coreos/etcd:v3.5.5
    restart: always # Adding restart option on docker-compose.yml
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
    restart: always # Adding restart option on docker-compose.yml
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
    image: milvusdb/milvus:v2.3.3
    restart: always # Adding restart option on docker-compose.yml
    command: ["milvus", "run", "standalone"]
    security_opt:
    - seccomp:unconfined
    environment:
      ETCD_ENDPOINTS: etcd:2379
      MINIO_ADDRESS: minio:9000
    volumes:
      - ${DOCKER_VOLUME_DIRECTORY:-.}/volumes/milvus:/var/lib/milvus
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:9091/healthz"]
      interval: 30s
      start_period: 90s
      timeout: 20s
      retries: 3
    ports:
      - "19530:19530"
      - "9091:9091"
    depends_on:
      - "etcd"
      - "minio"

  # Adding attu on docker-compose.yml
  attu:
    container_name: attu
    image: zilliz/attu:v2.2.6
    restart: always # Adding restart option on docker-compose.yml
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

```bash
(base) jaoneol@DESKTOP-B7GM3C5:~$ sudo docker compose up -d
[sudo] password for jaoneol:
WARN[0000] /home/jaoneol/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion
[+] Running 31/31
 ✔ minio Pulled                                                                                                                                                                             7.2s
   ✔ c7e856e03741 Pull complete                                                                                                                                                             1.9s
   ✔ c1ff217ec952 Pull complete                                                                                                                                                             1.9s
   ✔ b12cc8972a67 Pull complete                                                                                                                                                             1.9s
   ✔ 4324e307ea00 Pull complete                                                                                                                                                             1.9s
   ✔ 152089595ebc Pull complete                                                                                                                                                             2.0s
   ✔ 05f217fb8612 Pull complete                                                                                                                                                             3.9s
 ✔ standalone Pulled                                                                                                                                                                       22.0s
   ✔ 7646c8da3324 Pull complete                                                                                                                                                             4.7s
   ✔ dbc42d8e117f Pull complete                                                                                                                                                             5.2s
   ✔ 76d3189230b2 Pull complete                                                                                                                                                             5.2s
   ✔ 3b9d0f6feaf4 Pull complete                                                                                                                                                            11.5s
   ✔ ac4cc8944ee4 Pull complete                                                                                                                                                            11.6s
   ✔ 81a63fae5d9f Pull complete                                                                                                                                                            18.7s
 ✔ etcd Pulled                                                                                                                                                                             21.1s
   ✔ 804c8aba2cc6 Pull complete                                                                                                                                                            11.6s
   ✔ 2ae710cd8bfe Pull complete                                                                                                                                                            11.6s
   ✔ d462aa345367 Pull complete                                                                                                                                                            12.8s
   ✔ 0f8b424aa0b9 Pull complete                                                                                                                                                            13.3s
   ✔ d557676654e5 Pull complete                                                                                                                                                            13.3s
   ✔ c8022d07192e Pull complete                                                                                                                                                            14.4s
   ✔ d858cbc252ad Pull complete                                                                                                                                                            14.4s
   ✔ 1069fc2daed1 Pull complete                                                                                                                                                            15.2s
   ✔ b40161cd83fc Pull complete                                                                                                                                                            15.3s
   ✔ 5318d93a3a65 Pull complete                                                                                                                                                            15.3s
   ✔ 307c1adadb60 Pull complete                                                                                                                                                            15.9s
   ✔ fbb01d9e9dc9 Pull complete                                                                                                                                                            16.8s
   ✔ fbfea02ac3cf Pull complete                                                                                                                                                            16.9s
   ✔ 8c26e4bf18e2 Pull complete                                                                                                                                                            17.2s
   ✔ 1e59a65f8816 Pull complete                                                                                                                                                            17.2s
   ✔ ffbd4ca5f0bd Pull complete                                                                                                                                                            17.6s

[+] Running 5/5
 ✔ Network milvus               Created                                                                                                                                                                                              0.1s 
 ✔ Container milvus-minio       Started                                                                                                                                                                                              3.7s 
 ✔ Container milvus-etcd        Started                                                                                                                                                                                              3.6s 
 ✔ Container milvus-standalone  Started                                                                                                                                                                                              0.7s 
 ✔ Container attu               Started                                                                                                                                                                                              1.0s 
(base) jaoneol@DESKTOP-B7GM3C5:~$ 
```

```bash
(base) jaoneol@DESKTOP-B7GM3C5:~$ docker compose ps -a
WARN[0000] /home/jaoneol/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion
NAME                IMAGE                                      COMMAND                  SERVICE      CREATED          STATUS                    PORTS
attu                zilliz/attu:v2.2.6                         "docker-entrypoint.s…"   attu         24 minutes ago   Up 24 minutes             0.0.0.0:8000->3000/tcp, [::]:8000->3000/tcp
milvus-etcd         quay.io/coreos/etcd:v3.5.16                "etcd -advertise-cli…"   etcd         6 hours ago      Up 41 minutes (healthy)   2379-2380/tcp
milvus-minio        minio/minio:RELEASE.2023-03-20T20-16-18Z   "/usr/bin/docker-ent…"   minio        6 hours ago      Up 41 minutes (healthy)   0.0.0.0:9000-9001->9000-9001/tcp, :::9000-9001->9000-9001/tcp
milvus-standalone   milvusdb/milvus:v2.5.4                     "/tini -- milvus run…"   standalone   6 hours ago      Up 41 minutes (healthy)   0.0.0.0:9091->9091/tcp, :::9091->9091/tcp, 0.0.0.0:19530->19530/tcp, :::19530->19530/tcp
(base) jaoneol@DESKTOP-B7GM3C5:~$ docker ps -a
CONTAINER ID   IMAGE                                      COMMAND                  CREATED          STATUS                   PORTS                                                                                      NAMES
8f8e1173f0c1   zilliz/attu:v2.2.6                         "docker-entrypoint.s…"   3 minutes ago    Up 2 minutes             0.0.0.0:8000->3000/tcp, [::]:8000->3000/tcp                                                attu
84802d64c365   milvusdb/milvus:v2.3.3                     "/tini -- milvus run…"   3 minutes ago    Up 2 minutes (healthy)   0.0.0.0:9091->9091/tcp, :::9091->9091/tcp, 0.0.0.0:19530->19530/tcp, :::19530->19530/tcp   milvus-standalone
9f44aacbe577   minio/minio:RELEASE.2023-03-20T20-16-18Z   "/usr/bin/docker-ent…"   3 minutes ago    Up 3 minutes (healthy)   0.0.0.0:9000-9001->9000-9001/tcp, :::9000-9001->9000-9001/tcp                              milvus-minio
33e02b7bf540   quay.io/coreos/etcd:v3.5.5                 "etcd -advertise-cli…"   3 minutes ago    Up 3 minutes (healthy)   2379-2380/tcp                                                                              milvus-etcd
1b98f1295013   mysql:latest                               "docker-entrypoint.s…"   28 minutes ago   Up 28 minutes            0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp                                       mysql
```
