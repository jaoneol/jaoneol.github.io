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
	`docker volume create mysql`    
	`docker run -d --restart always --name mysql -v mysql:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=1234 -p 3306:3306 mysql:latest`      

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
# When you run the command `docker volume create mysql`, Docker creates a managed volume directory. 
# The location of this directory is `/var/lib/docker/volumes/mysql/_data`.
# You can check it using `docker volume inspect mysql_volume`.
(base) jaoneol@DESKTOP-B7GM3C5:~$ docker volume create mysql
mysql
(base) jaoneol@DESKTOP-B7GM3C5:~$ docker volume ls
DRIVER    VOLUME NAME
local     mysql
(base) jaoneol@DESKTOP-B7GM3C5:~$ docker volume inspect mysql
[
    {
        "CreatedAt": "2025-02-12T22:57:40+09:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/mysql/_data",
        "Name": "mysql",
        "Options": null,
        "Scope": "local"
    }
]
(base) jaoneol@DESKTOP-B7GM3C5:~$ 
```

-  Step3) Run the MySQL container
```bash
# docker run -d --name mysql -v mysql:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=1234 -p 3306:3306 mysql:latest
# docker update --restart always mysql # No need to run `docker stop mysql`. The update takes effect immediately.
# docker start mysql
(base) jaoneol@DESKTOP-B7GM3C5:~$ docker run -d --restart always --name mysql -v mysql:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=1234 -p 3306:3306 mysql:latest
1b98f129501345342c6c12c7d2a8b1629a27f6175aca3809de1bfa3f0ee3c322
(base) jaoneol@DESKTOP-B7GM3C5:~$ docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS                                                  NAMES
1b98f1295013   mysql:latest   "docker-entrypoint.s…"   29 seconds ago   Up 28 seconds   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   mysql
# If you navigate to `/var/lib/docker/volumes/mysql_volume/_data`, 
# you can see that all the files in the MySQL container's `/var/lib/mysql` directory are mounted there.
(base) jaoneol@DESKTOP-B7GM3C5:~$ su 
Password: 
root@DESKTOP-B7GM3C5:/home/jaoneol# cd /var/lib/docker/volumes/mysql/_data
root@DESKTOP-B7GM3C5:/var/lib/docker/volumes/mysql/_data# ls
'#ib_16384_0.dblwr'  '#innodb_redo'   auto.cnf        binlog.000002   ca-key.pem   client-cert.pem   ib_buffer_pool   ibtmp1   mysql.ibd    mysql_upgrade_history   private_key.pem   server-cert.pem   sys        undo_002
'#ib_16384_1.dblwr'  '#innodb_temp'   binlog.000001   binlog.index    ca.pem       client-key.pem    ibdata1          mysql    mysql.sock   performance_schema      public_key.pem    server-key.pem    undo_001
root@DESKTOP-B7GM3C5:/var/lib/docker/volumes/mysql/_data# exit
exit

# You can verify that it is a Named Volume using the `docker inspect mysql` command.
(base) jaoneol@DESKTOP-B7GM3C5:~$ docker inspect mysql
# The above part is omitted. 
"Mounts": [
            {
                "Type": "volume",
                "Name": "mysql",
                "Source": "/var/lib/docker/volumes/mysql/_data",
                "Destination": "/var/lib/mysql",
                "Driver": "local",
                "Mode": "z",
                "RW": true,
                "Propagation": ""
            }
        ],
```

## *Install Milvus on Ubuntu*


- Milvus    
	`wget https://github.com/milvus-io/milvus/releases/download/v2.5.4/milvus-standalone-docker-compose-gpu.yml -O docker-compose.yml`     
	`vi docker-compose.yml(adding attu and modifying named_volume, restart)`    
	`docker compose -p ds2man up -d`
	`docker compose -p ds2man ps -a`  
	`docker compose -p ds2man down --volumes` or `docker compose -p ds2man down`

<!--
https://do-hyeon.tistory.com/entry/Milvus-Milvus란-M1-Mac-Milvus-개발환경-구성하기
https://devocean.sk.com/blog/techBoardDetail.do?ID=165368
https://www.youtube.com/watch?v=K0ZayH0n7sI&t=65s
https://blog.csdn.net/qq_34146694/article/details/132658533
https://jackerlab.com/milvus-attu-docker-install/
-->

```bash
# To use `docker compose up -d`, make sure to name the file `docker-compose.yml` instead of `docker-compose-gpu.yml`, otherwise it won't execute properly!
(base) jaoneol@DESKTOP-B7GM3C5:~$ wget https://github.com/milvus-io/milvus/releases/download/v2.5.4/milvus-standalone-docker-compose-gpu.yml -O docker-compose.yml
--2025-02-18 00:49:13--  https://github.com/milvus-io/milvus/releases/download/v2.5.4/milvus-standalone-docker-compose-gpu.yml
Resolving github.com (github.com)... 20.200.245.247
Connecting to github.com (github.com)|20.200.245.247|:443... connected.
HTTP request sent, awaiting response... 302 Found
Location: https://objects.githubusercontent.com/github-production-release-asset-2e65be/208728772/3ea6d6f2-5847-4c4a-b2f6-c9258911248e?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=releaseassetproduction%2F20250217%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20250217T154914Z&X-Amz-Expires=300&X-Amz-Signature=8c3e71c0b84213986e19efff6817e9aeedb6e3b07d555350f5d1a14ae4586c99&X-Amz-SignedHeaders=host&response-content-disposition=attachment%3B%20filename%3Dmilvus-standalone-docker-compose-gpu.yml&response-content-type=application%2Foctet-stream [following]
--2025-02-18 00:49:14--  https://objects.githubusercontent.com/github-production-release-asset-2e65be/208728772/3ea6d6f2-5847-4c4a-b2f6-c9258911248e?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=releaseassetproduction%2F20250217%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20250217T154914Z&X-Amz-Expires=300&X-Amz-Signature=8c3e71c0b84213986e19efff6817e9aeedb6e3b07d555350f5d1a14ae4586c99&X-Amz-SignedHeaders=host&response-content-disposition=attachment%3B%20filename%3Dmilvus-standalone-docker-compose-gpu.yml&response-content-type=application%2Foctet-stream
Resolving objects.githubusercontent.com (objects.githubusercontent.com)... 185.199.111.133, 185.199.109.133, 185.199.110.133, ...
Connecting to objects.githubusercontent.com (objects.githubusercontent.com)|185.199.111.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 1774 (1.7K) [application/octet-stream]
Saving to: ‘docker-compose.yml’

docker-compose.yml                              100%[====================================================================================================>]   1.73K  --.-KB/s    in 0s      

2025-02-18 00:49:14 (7.14 MB/s) - ‘docker-compose.yml’ saved [1774/1774]

(base) jaoneol@DESKTOP-B7GM3C5:~$ ls
docker-compose.yml  miniconda3
```

```yml
# In `docker-compose.yml`, the Bind Mount method was used, but I will change it to a Named Volume so that it can be managed by Docker Volume.
(base) jaoneol@DESKTOP-B7GM3C5:~$ vi docker-compose.yml
services:
  etcd:
    container_name: milvus-etcd
    image: quay.io/coreos/etcd:v3.5.16
    restart: always # Adding restart option on docker-compose.yml
    environment:
      - ETCD_AUTO_COMPACTION_MODE=revision
      - ETCD_AUTO_COMPACTION_RETENTION=1000
      - ETCD_QUOTA_BACKEND_BYTES=4294967296
      - ETCD_SNAPSHOT_COUNT=50000
    volumes:
      # Bind Mount Usage
      # You need to add `DOCKER_VOLUME_DIRECTORY=/var/lib/docker` to the .env file.
      # - ${DOCKER_VOLUME_DIRECTORY:-.}/volumes/etcd:/etcd
      # Named volume Usage
      - etcd:/etcd
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
      # Bind Mount Usage
      # You need to add `DOCKER_VOLUME_DIRECTORY=/var/lib/docker` to the .env file.
      # - ${DOCKER_VOLUME_DIRECTORY:-.}/volumes/minio:/minio_data
      # Named volume Usage
      - minio:/minio_data
      # During testing, there is a mount for MinIO's internal configuration storage. 
      # Explicitly specify all related directories to prevent MinIO from automatically creating anonymous volumes.
      #  {
      #       "Type": "volume",
      #       "Name": "2b202e0a21487176e2cb0994c61de329d2266b2b2948bd148015da16579ea5e5",
      #       "Source": "/var/lib/docker/volumes/2b202e0a21487176e2cb0994c61de329d2266b2b2948bd148015da16579ea5e5/_data",
      #       "Destination": "/data",
      #       "Driver": "local",
      #       "Mode": "",
      #       "RW": true,
      #       "Propagation": ""
      #   }
      - minio-anonymous:/data
    command: minio server /minio_data --console-address ":9001"
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:9000/minio/health/live"]
      interval: 30s
      timeout: 20s
      retries: 3

  standalone:
    container_name: milvus-standalone
    image: milvusdb/milvus:v2.5.4-gpu
    restart: always # Adding restart option on docker-compose.yml
    command: ["milvus", "run", "standalone"]
    security_opt:
    - seccomp:unconfined
    environment:
      ETCD_ENDPOINTS: etcd:2379
      MINIO_ADDRESS: minio:9000
    volumes:
      # Bind Mount Usage
      # You need to add `DOCKER_VOLUME_DIRECTORY=/var/lib/docker` to the .env file.
      # - ${DOCKER_VOLUME_DIRECTORY:-.}/volumes/milvus:/var/lib/milvus
      # Named volume Usage
      - milvus:/var/lib/milvus
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

  # Adding attu on docker-compose.yml
  attu:
    container_name: attu
    image: zilliz/attu:v2.4.7
    restart: always # Adding restart option on docker-compose.yml
    environment:
      MILVUS_URL: milvus-standalone:19530
    ports:
      - "8000:3000"
    depends_on:
      - "standalone"

# To use a Named Volume, you must explicitly specify the volume.
volumes:
  milvus:
  etcd:
  minio:
  minio-anonymous:
  
networks:
  default:
    name: milvus
```

```bash
# When running `docker compose up -d`, the volume is created with the account name attached. 
# Use the `-p` option to attach it with the project name instead.
# `docker compose up -d` → `docker compose -p ds2man up -d`
# DRIVER    VOLUME NAME
# local     jaoneol_etcd → ds2man_etcd
# local     jaoneol_milvus → ds2man_milvus
# local     jaoneol_minio → ds2man_minio
# local     jaoneol_minio-anonymous → ds2man_minio-anonymous
(base) jaoneol@DESKTOP-B7GM3C5:~$ sudo docker compose -p ds2man up -d
[sudo] password for jaoneol:
[+] Running 53/53
 ✔ attu Pulled                                                                                                                                                                         13.7s 
 ✔ minio Pulled                                                                                                                                                                         8.2s 
 ✔ etcd Pulled                                                                                                                                                                         64.4s 
 ✔ standalone Pulled                                                                                                                                                                   89.1s 
[+] Running 9/9
 ✔ Network milvus                   Created                                                                                                                                             0.1s 
 ✔ Volume "ds2man_milvus"           Created                                                                                                                                             0.0s 
 ✔ Volume "ds2man_etcd"             Created                                                                                                                                             0.0s 
 ✔ Volume "ds2man_minio"            Created                                                                                                                                             0.0s 
 ✔ Volume "ds2man_minio-anonymous"  Created                                                                                                                                             0.0s 
 ✔ Container milvus-minio           Started                                                                                                                                             7.7s 
 ✔ Container milvus-etcd            Started                                                                                                                                             7.7s 
 ✔ Container milvus-standalone      Started                                                                                                                                             1.1s 
 ✔ Container attu                   Started                                                                                                                                             1.5s 
(base) jaoneol@DESKTOP-B7GM3C5:~$ 
```

```bash
# Since `docker compose -p ds2man up -d` was executed, you can check it using `docker compose -p ds2man ps -a`. 
# Note that it will not be visible with `docker compose ps -a`, so keep that in mind.
(base) jaoneol@DESKTOP-B7GM3C5:~$ docker compose ps -a
NAME      IMAGE     COMMAND   SERVICE   CREATED   STATUS    PORTS
(base) jaoneol@DESKTOP-B7GM3C5:~$ docker compose -p ds2man ps -a
NAME                IMAGE                                      COMMAND                  SERVICE      CREATED         STATUS                   PORTS
attu                zilliz/attu:v2.4.7                         "docker-entrypoint.s…"   attu         6 minutes ago   Up 6 minutes             0.0.0.0:8000->3000/tcp, [::]:8000->3000/tcp
milvus-etcd         quay.io/coreos/etcd:v3.5.16                "etcd -advertise-cli…"   etcd         6 minutes ago   Up 6 minutes (healthy)   2379-2380/tcp
milvus-minio        minio/minio:RELEASE.2023-03-20T20-16-18Z   "/usr/bin/docker-ent…"   minio        6 minutes ago   Up 6 minutes (healthy)   0.0.0.0:9000-9001->9000-9001/tcp, :::9000-9001->9000-9001/tcp
milvus-standalone   milvusdb/milvus:v2.5.4-gpu                 "/tini -- milvus run…"   standalone   6 minutes ago   Up 6 minutes             0.0.0.0:9091->9091/tcp, :::9091->9091/tcp, 0.0.0.0:19530->19530/tcp, :::19530->19530/tcp

(base) jaoneol@DESKTOP-B7GM3C5:~$ docker ps -a
CONTAINER ID   IMAGE                                      COMMAND                  CREATED          STATUS                   PORTS                                                                                      NAMES
682e142fe7ea   zilliz/attu:v2.4.7                         "docker-entrypoint.s…"   7 minutes ago    Up 7 minutes             0.0.0.0:8000->3000/tcp, [::]:8000->3000/tcp                                                attu
0e3d9c812be2   milvusdb/milvus:v2.5.4-gpu                 "/tini -- milvus run…"   7 minutes ago    Up 7 minutes             0.0.0.0:9091->9091/tcp, :::9091->9091/tcp, 0.0.0.0:19530->19530/tcp, :::19530->19530/tcp   milvus-standalone
88079d4db07f   minio/minio:RELEASE.2023-03-20T20-16-18Z   "/usr/bin/docker-ent…"   7 minutes ago    Up 7 minutes (healthy)   0.0.0.0:9000-9001->9000-9001/tcp, :::9000-9001->9000-9001/tcp                              milvus-minio
c6a22a7dcaa3   quay.io/coreos/etcd:v3.5.16                "etcd -advertise-cli…"   7 minutes ago    Up 7 minutes (healthy)   2379-2380/tcp                                                                              milvus-etcd
cbd7bed0cee5   mysql:latest                               "docker-entrypoint.s…"   26 minutes ago   Up 26 minutes            0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp                                       mysql

# `docker volume ls` 조회 결과는 앞서 말한 내용과 일치하게 생성된다.
(base) jaoneol@DESKTOP-B7GM3C5:~$ docker volume ls
DRIVER    VOLUME NAME
local     ds2man_etcd
local     ds2man_milvus
local     ds2man_minio
local     ds2man_minio-anonymous
local     mysql

(base) jaoneol@DESKTOP-B7GM3C5:~$ su
Password:
(base) root@DESKTOP-B7GM3C5:~$ su
Password:
root@DESKTOP-B7GM3C5:/home/jaoneol# cd /var/lib/docker/volumes
root@DESKTOP-B7GM3C5:/var/lib/docker/volumes# ls -lrt
total 48
brw------- 1 root root 8, 32 Feb 18 00:39 backingFsBlockDev
drwx-----x 3 root root  4096 Feb 18 00:41 mysql
drwx-----x 3 root root  4096 Feb 18 01:02 ds2man_minio
drwx-----x 3 root root  4096 Feb 18 01:02 ds2man_milvus
drwx-----x 3 root root  4096 Feb 18 01:02 ds2man_etcd
-rw------- 1 root root 32768 Feb 18 01:02 metadata.db
drwx-----x 3 root root  4096 Feb 18 01:02 ds2man_minio-anonymous
root@DESKTOP-B7GM3C5:/var/lib/docker/volumes# 
```

```bash
# Since `docker compose -p ds2man up -d` was executed, you can check it using `docker compose -p ds2man stop` and `docker compose -p ds2man start`. 
# Note that it will not stop with `docker compose stop`, so keep that in mind.
# Note that it will not start with `docker compose start`, so keep that in mind.
(base) jaoneol@DESKTOP-B7GM3C5:~$ docker compose -p ds2man stop
[+] Stopping 4/4
 ✔ Container attu               Stopped                                                                                                                                                                                              0.4s 
 ✔ Container milvus-standalone  Stopped                                                                                                                                                                                              1.4s 
 ✔ Container milvus-minio       Stopped                                                                                                                                                                                              1.0s 
 ✔ Container milvus-etcd        Stopped                                                                                                                                                                                              0.4s 

(base) jaoneol@DESKTOP-B7GM3C5:~$ docker compose -p ds2man ps -a
NAME                IMAGE                                      COMMAND                  SERVICE      CREATED         STATUS                      PORTS
attu                zilliz/attu:v2.4.7                         "docker-entrypoint.s…"   attu         5 minutes ago   Exited (1) 22 seconds ago   
milvus-etcd         quay.io/coreos/etcd:v3.5.16                "etcd -advertise-cli…"   etcd         5 minutes ago   Exited (0) 20 seconds ago   
milvus-minio        minio/minio:RELEASE.2023-03-20T20-16-18Z   "/usr/bin/docker-ent…"   minio        5 minutes ago   Exited (0) 20 seconds ago   
milvus-standalone   milvusdb/milvus:v2.5.4-gpu                 "/tini -- milvus run…"   standalone   5 minutes ago   Exited (0) 21 seconds ago   

(base) jaoneol@DESKTOP-B7GM3C5:~$ docker compose -p ds2man start
[+] Running 4/4
 ✔ Container milvus-minio       Started                                                                                                                                                                                              0.4s 
 ✔ Container milvus-etcd        Started                                                                                                                                                                                              0.3s 
 ✔ Container milvus-standalone  Started                                                                                                                                                                                              0.4s 
 ✔ Container attu               Started                                                                                                                                                                                              0.3s 
(base) jaoneol@DESKTOP-B7GM3C5:~$ docker compose -p ds2man ps -a
NAME                IMAGE                                      COMMAND                  SERVICE      CREATED         STATUS                            PORTS
attu                zilliz/attu:v2.4.7                         "docker-entrypoint.s…"   attu         9 minutes ago   Up 5 seconds                      0.0.0.0:8000->3000/tcp, [::]:8000->3000/tcp
milvus-etcd         quay.io/coreos/etcd:v3.5.16                "etcd -advertise-cli…"   etcd         9 minutes ago   Up 6 seconds (health: starting)   2379-2380/tcp
milvus-minio        minio/minio:RELEASE.2023-03-20T20-16-18Z   "/usr/bin/docker-ent…"   minio        9 minutes ago   Up 6 seconds (health: starting)   0.0.0.0:9000-9001->9000-9001/tcp, :::9000-9001->9000-9001/tcp
milvus-standalone   milvusdb/milvus:v2.5.4-gpu                 "/tini -- milvus run…"   standalone   9 minutes ago   Up 6 seconds                      0.0.0.0:9091->9091/tcp, :::9091->9091/tcp, 0.0.0.0:19530->19530/tcp, :::19530->19530/tcp
(base) jaoneol@DESKTOP-B7GM3C5:~$
```

```bash
# Since `docker compose -p ds2man up -d` was executed, you can check it using `docker compose -p ds2man down --volumes`. 
# Note that it will not delete with `docker compose down --volumes`, so keep that in mind.
(base) jaoneol@DESKTOP-B7GM3C5:~/milvus_files$ docker compose -p ds2man down --volumes
[+] Running 9/9
 ✔ Container attu                 Removed                                                                                                                                                                                                      0.3s 
 ✔ Container milvus-standalone    Removed                                                                                                                                                                                                      1.5s 
 ✔ Container milvus-etcd          Removed                                                                                                                                                                                                      1.8s 
 ✔ Container milvus-minio         Removed                                                                                                                                                                                                      1.9s 
 ✔ Volume ds2man_minio-anonymous  Removed                                                                                                                                                                                                      0.0s 
 ✔ Volume ds2man_milvus           Removed                                                                                                                                                                                                      0.0s 
 ✔ Volume ds2man_etcd             Removed                                                                                                                                                                                                      0.0s 
 ✔ Volume ds2man_minio            Removed                                                                                                                                                                                                      0.0s 
 ✔ Network milvus                 Removed                                                                                                                                                                                                      0.4s 
(base) jaoneol@DESKTOP-B7GM3C5:~/milvus_files$ docker volume ls
DRIVER    VOLUME NAME
local     mysql
(base) jaoneol@DESKTOP-B7GM3C5:~/milvus_files$ 
```