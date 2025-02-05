---
title: MySQL, Milvus Installation and Configuration
description: Let's install MySQL which are necessary for the LLM service(DS2Man).
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
	`docker run -d --restart always --name mysql -e MYSQL_ROOT_PASSWORD=1234 -d -p 3306:3306 mysql:latest`      

<!--
MySQL
-->

```bash
jaoneol@DESKTOP-B7GM3C5:~$ docker pull mysql
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
jaoneol@DESKTOP-B7GM3C5:~$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
mysql        latest    a52cba19e8cc   13 days ago   797MB
```

```bash
# docker run --name mysql -e MYSQL_ROOT_PASSWORD=1234 -d -p 3306:3306 mysql:latest
# docker update --restart always mysql # No need to run `docker stop mysql`. The update takes effect immediately.
# docker start mysql
jaoneol@DESKTOP-B7GM3C5:~$ docker run -d --restart always --name mysql -e MYSQL_ROOT_PASSWORD=1234 -d -p 3306:3306 mysql:latest
987a0e7deafecd99436ce56629150a2fbe648380620c5230fa24869e22c8b6d1
jaoneol@DESKTOP-B7GM3C5:~$ docker ps -a
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS                                                  NAMES
987a0e7deafe   mysql:latest   "docker-entrypoint.s…"   7 seconds ago   Up 6 seconds   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   mysql
jaoneol@DESKTOP-B7GM3C5:~$
```

## *Install Milvus on Ubuntu(Ing..)*

- Milvus    
	`wget https://github.com/milvus-io/milvus/releases/download/v2.3.3/milvus-standalone-docker-compose.yml -O docker-compose.yml`        
	`sudo docker-compose up -d`     
	`sudo docker compose ps`     
	`docker run -p 8000:3000 -d milvusdb/milvus-insight`

<!--
https://do-hyeon.tistory.com/entry/Milvus-Milvus란-M1-Mac-Milvus-개발환경-구성하기
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
[+] Running 4/4
 ✔ Network milvus               Created                                                                                                                                                     0.1s
 ✔ Container milvus-etcd        Started                                                                                                                                                     4.5s
 ✔ Container milvus-minio       Started                                                                                                                                                     4.5s
 ✔ Container milvus-standalone  Started                                                                                                                                                     0.6s
(base) jaoneol@DESKTOP-B7GM3C5:~$ docker-compose ps
Command 'docker-compose' not found, but can be installed with:
sudo snap install docker          # version 27.2.0, or
sudo apt  install docker-compose  # version 1.29.2-6
See 'snap info docker' for additional versions.
```

```bash
(base) jaoneol@DESKTOP-B7GM3C5:~$ sudo docker-compose ps
WARN[0000] /home/jaoneol/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion
NAME                IMAGE                                      COMMAND                  SERVICE      CREATED         STATUS                   PORTS
milvus-etcd         quay.io/coreos/etcd:v3.5.16                "etcd -advertise-cli…"   etcd         5 minutes ago   Up 5 minutes (healthy)   2379-2380/tcp
milvus-minio        minio/minio:RELEASE.2023-03-20T20-16-18Z   "/usr/bin/docker-ent…"   minio        5 minutes ago   Up 5 minutes (healthy)   0.0.0.0:9000-9001->9000-9001/tcp, :::9000-9001->9000-9001/tcp
milvus-standalone   milvusdb/milvus:v2.5.4                     "/tini -- milvus run…"   standalone   5 minutes ago   Up 5 minutes (healthy)   0.0.0.0:9091->9091/tcp, :::9091->9091/tcp, 0.0.0.0:19530->19530/tcp, :::19530->19530/tcp
(base) jaoneol@DESKTOP-B7GM3C5:~$

```

```bash
(base) jaoneol@DESKTOP-B7GM3C5:~$ docker run -p 8000:3000 -d milvusdb/milvus-insight
Unable to find image 'milvusdb/milvus-insight:latest' locally
latest: Pulling from milvusdb/milvus-insight
3c55fec5aa21: Pull complete
70c9ba1bcd3f: Pull complete
bcd611556cda: Pull complete
921d745fc5a4: Pull complete
2daa4ad36fda: Pull complete
38304eb5bd66: Pull complete
d7d26aa929d8: Pull complete
a2f53a5f850e: Pull complete
e69849d04b49: Pull complete
721af8aa4534: Pull complete
Digest: sha256:f4d74a93e456c49f6e60885ae333f12f5856068a4cab2f3ddfe8bdd9b972a023
Status: Downloaded newer image for milvusdb/milvus-insight:latest
406f28e7c980626b507f87abc8c65ac98168002e348f9da44b77e6462bb980e0
(base) jaoneol@DESKTOP-B7GM3C5:~$ docker ps
```