---
title: Container Resource
description: Let's limit container resources.
author: DS2Man
date: 2024-10-17 11:00:00 +0000
categories: [Docker, D-Understanding]
tags:
  - WSL
  - Ubuntu
  - Docker
  - Command
math: true
pin: true
---

## *Why should we limit container resources?*

By default, containers are not restricted in their use of the host's hardware resources. However, if resource limits are not set, a container may excessively consume resources, preventing other containers from functioning properly. Therefore, it is necessary to impose resource limits when needed. In this post, we will conduct stress testing to analyze various constraints.    
The resources that can be limited using Docker commands are `CPU`, `Memory`. The command to monitor a running container is as follows.

- `docker stat` or `docker stat {container}`    
	Check the runtime statistics of a running container.

```bash
(base) jaoneol@DESKTOP-B7GM3C5:~/stresstest$ docker stats
CONTAINER ID   NAME                CPU %     MEM USAGE / LIMIT     MEM %     NET I/O           BLOCK I/O   PIDS
2c79a399533d   attu                0.01%     84.18MiB / 31.27GiB   0.26%     2.41kB / 0B       0B / 0B     19
30030fa9a5db   milvus-standalone   2.06%     366.4MiB / 31.27GiB   1.14%     7.67MB / 10.8MB   0B / 0B     66
35ff6cc5ea65   milvus-etcd         0.34%     47.88MiB / 31.27GiB   0.15%     10.8MB / 7.65MB   0B / 0B     18
6789d6c91dd5   milvus-minio        0.00%     133.7MiB / 31.27GiB   0.42%     29.6kB / 21.7kB   0B / 0B     25
1b98f1295013   mysql               0.25%     503.2MiB / 31.27GiB   1.57%     1.96kB / 0B       0B / 0B     44
```
	
<!--
기본적으로 컨테이너는 호스트의 하드웨어 리소스에 대한 사용 제한을 받지 않는다. 그런데 리소스 제한하지 않으면 Container는 전자원을 마구 사용하게 된다. 결과적으로 다른 컨테이너는 제대로 동작하지 못한다고 함. 그래서 필요시 용량 제한을 둬야한다. 이번 글에선 stress 테스트를 통해서 다양한 제약 조건을 파악하고자 한다.
Docker command를 통해서 제한할 수 리소스는 `CPU`, `Memory`, `Disk I/O`가 있다.
실행 중인 컨테이너를 모니터 하는 명령어는 다음과 같다.
-->

## *Preparation: Build a image for stress testing*

The `stress` command is commonly used as a tool for generating load, and various options can be checked using `stress --help`. I will use a few of them.

- `stress --vm 2 --vm-bytes 90m -t 5s`    
	Creates two memory-consuming (worker) processes.  Each VM worker allocates 90MB of memory.

![stress](/assets/img/docker/2024-10-17-Docker-Understanding7_1.png)
_`stress --vm 2 --vm-bytes 90m -t 5s`_

- `stress -c 3 -t 5s` or `stress --cpu 3 --timeout 5s`    
	Creates two CPU worker processes to generate CPU load.

![stress](/assets/img/docker/2024-10-17-Docker-Understanding7_2.png)
_`stress -c 3 -t 5s`_

<!--
부하 테스트를 위해서 아래 dockerfile을 만들었다. stress 명령어는 부하를 주는 tool 로 주로 사용되며 stress --help로 다양한 옵션을 확인할 수 있다. 이중에서 몇가지 사용하겠다.

stress --cpu 2 --timeout 60s #### CPU 부하 테스트, cpu 2개를 100% 사용
stress --memory 2 --vm-bytes 128M --timeout 60s #### Memory 부하 테스트
-->

I create the following Dockerfile for stress testing, build a image, and run a container

```bash
FROM debian

LABEL maintainer="ds2man@example.com"
LABEL version="1.0"

RUN apt-get update && \
    apt-get install -y stress && \
    rm -rf /var/lib/apt/lists/*

    CMD ["stress", "-c", "2"]
```

```bash
(base) jaoneol@DESKTOP-B7GM3C5:~/stresstest$ docker build -t mystress .
(base) jaoneol@DESKTOP-B7GM3C5:~/stresstest$ docker images
REPOSITORY            TAG                            IMAGE ID       CREATED          SIZE
mystress              latest                         044b247aa916   49 seconds ago   117MB
(base) jaoneol@DESKTOP-B7GM3C5:~/stresstest$ 
```
	

## Memory Limitation Options

|Option|Meaning|
|---|---|
|`--memory`, `-m`|Specifies the maximum amount of memory a container can use.|
|`--memory-swap`|Configures the swap memory area the container can use. Memory + Swap. By default, it is set to twice the memory value.|
|`--memory-reservation`|Sets a soft limit that is lower than the `--memory` value.|
|`--oom-kill-disable`|Protects the process from being killed by the OOM (Out of Memory) Killer.|

`Swap memory` refers to a space that utilizes disk storage to supplement insufficient memory when the physical RAM is fully occupied, and additional memory is needed. Since disk space is used like memory, it can be considered virtual memory. However, because swap memory utilizes the hard disk instead of actual physical memory, its speed is significantly slower compared to RAM.

- `docker run -d -m 512m nginx:lastest`    
	The maximum memory allocated to the nginx container is set to 512MB.    
	However, since swap is not disabled, it can use up to 1024MB (twice the limit).
- `docker run -d -m 1g --memory-reservation 500m nginx:lastest`    
	The nginx container is allowed to use a maximum of 1GB of memory, with a guaranteed minimum of 500MB.
- `docker run -d -m 200m --memory-swap 300m nginx:lastest`    
	The container can use up to 200MB of memory and 300MB of swap (which utilizes disk space as virtual memory).    
	A common misconception is that the total available memory is 500MB, but in reality, the total swap-inclusive memory usage is 300MB. That is, WAP 300MB = 200MB memory + actual SWAP 100MB.
- `docker run -d -m 200m --oom-kill-disable nginx:lastest`    
	If the Linux kernel detects insufficient physical memory, it triggers the Out-of-Memory (OOM) killer, terminating containers that consume excessive virtual memory.     
	This command prevents the container from being killed by OOM.

```bash
# When creating the `stress` container, 100MB of memory is allocated, including swap. Then, a memory load of 90MB is generated for 5 seconds → Works as expected.
(base) jaoneol@DESKTOP-B7GM3C5:~/stresstest$ docker run -m 100m --memory-swap 100m mystress:latest stress --vm 1 --vm-bytes 90m -t 5s
stress: info: [1] dispatching hogs: 0 cpu, 0 io, 1 vm, 0 hdd
stress: info: [1] successful run completed in 5s

# When creating the `stress` container, 100MB of memory is allocated, including swap. Then, a memory load of 150MB is generated for 5 seconds → Does not work as expected.
(base) jaoneol@DESKTOP-B7GM3C5:~/stresstest$ docker run -m 100m --memory-swap 100m mystress:latest stress --vm 1 --vm-bytes 150m -t 5s
stress: info: [1] dispatching hogs: 0 cpu, 0 io, 1 vm, 0 hdd
stress: FAIL: [1] (425) <-- worker 7 got signal 9
stress: WARN: [1] (427) now reaping child worker processes
stress: FAIL: [1] (431) kill error: No such process
stress: FAIL: [1] (461) failed run completed in 0s

# When creating the `stress` container, swap is omitted, and memory is set to 100MB, allowing a maximum allocation of 200MB. Then, a memory load of 150MB is generated for 5 seconds. → Works as expected.
(base) jaoneol@DESKTOP-B7GM3C5:~/stresstest$ docker run -m 100m mystress:latest stress --vm 1 --vm-bytes 150m -t 5s
stress: info: [1] dispatching hogs: 0 cpu, 0 io, 1 vm, 0 hdd
stress: info: [1] successful run completed in 5s

# When creating the `stress` container, swap is omitted, and memory is set to 100MB, allowing a maximum allocation of 200MB. Then, a memory load of 250MB is generated for 5 seconds. → it triggers the Out-of-Memory (OOM) killer, terminating containers that consume excessive virtual memory.  
# But!, Using `--oom-kill-disable=true`, The container is protected from being killed by OOM (Out of Memory).
(base) jaoneol@DESKTOP-B7GM3C5:~/stresstest$ docker run -d -m 100M --oom-kill-disable=true  mystress:latest stress --vm 1 --vm-bytes 250m -t 5s
a5edc5fbe316897821d80146b25057eba2832eb913cf69d48f5c02b62d868439
(base) jaoneol@DESKTOP-B7GM3C5:~/stresstest$ 
```

## CPU Resource Limitation Options

|Option|Meaning|
|---|---|
|`--cpus`|Specifies the number of CPU cores allocated to the container.  <br>`--cpus="1.5"` allows the container to use up to 1.5 CPU cores.|
|`--cpuset-cpus`|Specifies the CPU cores that the container can use. The CPU index starts from 0.  <br>`--cpuset-cpus=0-4` allows the container to use CPU cores 0 to 4.|
|`--cpu-share`|Sets the CPU share using a base value of 1024.  <br>`--cpu-share 2048` allows the container to use twice the default CPU resources.|

- `docker run -d --cpus=".5" ubuntu:24.04`    
	When there are four CPU cores, they are numbered as Core 0, 1, 2, and 3. Using one core fully corresponds to 100% usage. Setting `cpus=1` allows the container to fully utilize one of the cores (Core 0, 1, 2, or 3).    
	Setting `cpus=0.5` allows the container to use 50% of a randomly selected core among the four cores.
- `docker run -d --cpuset-cpus 0-3 ubuntu:24.04`    
	Specifies the CPU cores that the container can use. For example, setting `0-4` allows the container to access cores from 0 to 4.
- `docker run -d --cpu-shares 2048 ubuntu:24.04`    
	Defines the relative weight of CPU resource allocation. By default, all application containers use a weight of 1024. If a container is assigned `2048`, it will receive twice the CPU resources compared to a container with the default setting.

```bash
# When creating the `stress` container, 1 CPU is allocated. Then, 100% load is applied to a single CPU. → Works as expected.
(base) jaoneol@DESKTOP-B7GM3C5:~/stresstest$ docker run --cpuset-cpus 1 -d mystress:latest stress --cpu 1 -t 5s
f3f63524e4aba1d7861f68a1109c0d5a3df92895e0f3e06339ef5bd4c987371e

# When creating the `stress` container, CPUs 0 and 1 are allocated. Then, 100% load is applied to a single CPU, causing load on either CPU 0 or CPU 1. → Works as expected.
(base) jaoneol@DESKTOP-B7GM3C5:~/stresstest$ docker run --cpuset-cpus 0-1 -d mystress:latest stress --cpu 1 -t 5s
f18f0102283150f3f1b41d3b968113c13f0a09e6c1d1228137ccb91624d50a9f
(base) jaoneol@DESKTOP-B7GM3C5:~/stresstest$ 
```

<!--
## Block I/O Limitation Options

|Option|Meaning|
|---|---|
|`--blkio-weight`|Sets the Block I/O quota, with a selectable range between 100 and 1000 (default: 500).|
|`--blkio-weight-device`|Specifies the Block I/O weight for a specific device.|
|`--device-read-bps`|Limits the read speed for a specific device in units of KB, MB, or GB per second.|
|`--device-write-bps`|Limits the write speed for a specific device in units of KB, MB, or GB per second.|
|`--device-read-iops`|Sets the read speed quota for the container, limiting I/O operations per second (IOPS).  <br>The IOPS quota restricts the number of read operations that can be performed per second.|
|`--device-write-iops`|Sets the write speed quota for the container, limiting I/O operations per second (IOPS).  <br>The data transfer rate per second is calculated as:  <br>**Data Transfer per Second = IOPS × Block Size (Data Unit).**|

`Quota` settings are a method of limiting disk usage per user. They are used in hosting servers and other environments where multiple users share resources, allowing disk usage limits to be set per user or group.

- `docker run -it --rm --blkio-weight 100 ubuntu:latest /bin/bash`    
	Similar to CPU shares, it schedules block I/O operations. The value range is from 100 to 1000 (default: 500). If a specific container is set to `1000`, it will receive twice the I/O resources compared to other containers. If set to `100`, it will receive only 1/5 of the resources.
- `docker run -it --rm --device-write-bps /dev/vda:10mb ubuntu:latest /bin/bash`     
	Limits write speed on `vda` to 10MB.
- `docker run -it --rm --device-write-iops /dev/vda:100 ubuntu:latest /bin/bash`    
	Sets quotas for the container's read/write speed.

-->