---
title: Docker Engine on Ubuntu
description: Let's Install Docker Engine on Ubuntu.
author: DS2Man
date: 2024-10-08 11:00:00 +0000
categories: [Docker, D-Setup]
tags:
  - WSL
  - Ubuntu
  - Docker
math: true
pin: true
---

Due to Docker Desktop's monetization policy (as of August 31, 2021), companies with more than 250 employees or annual revenue exceeding $10 million cannot use it. Since I fall into this category, I plan to install Docker using a different method. (If I get the chance, I will write a post on utilizing Docker Desktop.) In the previous post, we installed WSL, and now we aim to set up a Docker environment without Docker Desktop.    
This document is based on docker documentation ([https://docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/)).

<!--
도커 데스크톱(Docker Desktop) 유료화 정책(2021년 8월 31일)으로 250명 이상의 직원을 보유하거나 연 매출이 1,000만 달러를 초과하는 기업에서는 사용이 불가능하다. 나의 경우도 여기에 해당되어 다른 방법으로 Docker 설치하려한다.(기회가 된다면 Docker Desktop을 활용하는 글을 쓰겠다.)
앞서 나는 WSL을 설치했는데 Docker Desktop 없이 Docker 환경을 구축하고자 한다.

docker의 문서(https://docs.docker.com/engine/install/ubuntu/) 기준으로 작성되었습니다.
-->

## *Install Docker Engine on Ubuntu*

- According to the documentation ([Installation methods](https://docs.docker.com/engine/install/ubuntu/)), there are three ways to install the Docker Engine. In this post, I will install it using the script method.    
	`curl -sSL https://get.docker.com | sh`        

<!--
문서(https://docs.docker.com/engine/install/ubuntu/)에 따르면 도커 엔진을 설치하는 방법은 3가지가 있다. 이번 글에서는 script를 활용하는 방법으로 설치하겠다.
-->

```bash
# curl -fsSL https://get.docker.com -o get-docker.sh
# `-f`: Fails silently without displaying an error message if the download fails.
# `-s`: Executes in silent mode without displaying the progress status.
# `-S`: Used together with `-s`, it displays an error message if the operation fails.
# `-L`: Automatically follows redirects if they are present.
# sudo sh get-docker.sh
jaoneol@DESKTOP-B7GM3C5:~$ curl -sSL https://get.docker.com | sh
# Executing docker install script, commit: 4c94a56999e10efcf48c5b8e3f6afea464f9108e

WSL DETECTED: We recommend using Docker Desktop for Windows. 
Please get Docker Desktop from https://www.docker.com/products/docker-desktop

You may press Ctrl+C now to abort this script.
+ sleep 20
```

![docker engine Installation](/assets/img/2024-10-08-Docker-Setup2_1.png)
_docker engine Installation_

## *Docker Settings*

- Let's use 'iptables-legacy' instead of 'iptables-nft'.
	Docker is only compatible with iptables-nft and iptables-legacy. Firewall rules created with nft are not supported on a system with Docker installed. Make sure that any firewall rulesets you use are created with iptables or ip6tables, and that you add them to the DOCKER-USER chain, see Packet filtering and firewalls.    
	`sudo update-alternatives --set iptables /usr/sbin/iptables-legacy`

```bash
# sudo update-alternatives --set iptables /usr/sbin/iptables-legacy
jaoneol@DESKTOP-B7GM3C5:~$ sudo update-alternatives --config iptables
[sudo] password for jaoneol:
There are 2 choices for the alternative iptables (providing /usr/sbin/iptables).

  Selection    Path                       Priority   Status
------------------------------------------------------------
* 0            /usr/sbin/iptables-nft      20        auto mode
  1            /usr/sbin/iptables-legacy   10        manual mode
  2            /usr/sbin/iptables-nft      20        manual mode

Press <enter> to keep the current choice[*], or type selection number: 1
```

- If a "permission denied" error occurs for `/var/run/docker.sock` after installing Docker, change the file's permissions to `666` to allow other users in the group to access it.    
	`sudo chmod 666 /var/run/docker.sock`

<!--
docker 설치 후 /var/run/docker.sock의 permission denied 발생하는 경우 /var/run/docker.sock 파일의 권한을 666으로 변경하여 그룹 내 다른 사용자도 접근 가능하게 변경
-->

```bash
jaoneol@DESKTOP-B7GM3C5:~$ docker version
Client: Docker Engine - Community
 Version:           27.5.1
 API version:       1.47
 Go version:        go1.22.11
 Git commit:        9f9e405
 Built:             Wed Jan 22 13:41:48 2025
 OS/Arch:           linux/amd64
 Context:           default
permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get "http://%2Fvar%2Frun%2Fdocker.sock/v1.47/version": dial unix /var/run/docker.sock: connect: permission denied
# Solution for "permission denied"
jaoneol@DESKTOP-B7GM3C5:~$ sudo chmod 666 /var/run/docker.sock
# Very important!
jaoneol@DESKTOP-B7GM3C5:~$ docker version
Client: Docker Engine - Community
 Version:           27.5.1
 API version:       1.47
 Go version:        go1.22.11
 Git commit:        9f9e405
 Built:             Wed Jan 22 13:41:48 2025
 OS/Arch:           linux/amd64
 Context:           default

Server: Docker Engine - Community
 Engine:
  Version:          27.5.1
  API version:      1.47 (minimum version 1.24)
  Go version:       go1.22.11
  Git commit:       4c9b3b0
  Built:            Wed Jan 22 13:41:48 2025
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.7.25
  GitCommit:        bcc810d6b9066471b0b6fa75f557a15a1cbf31bb
 runc:
  Version:          1.2.4
  GitCommit:        v1.2.4-0-g6c52b3f
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
jaoneol@DESKTOP-B7GM3C5:~$ 
```

- Manage Docker as a non-root user
	The Docker daemon binds to a Unix socket, not a TCP port. By default it's the root user that owns the Unix socket, and other users can only access it using sudo. The Docker daemon always runs as the root user.    
	`usermod -a -G docker jaoneol`

<!--
Docker 데몬은 TCP 포트가 아닌 Unix 소켓에 바인딩됩니다. 기본적으로 Unix 소켓의 소유자는 root 사용자이며, 다른 사용자는 `sudo`를 사용해야만 접근할 수 있습니다. Docker 데몬은 항상 root 사용자로 실행됩니다.
-->

```bash
jaoneol@DESKTOP-B7GM3C5:~$ su - 
Password:
root@DESKTOP-B7GM3C5:~# usermod -a -G docker jaoneol
root@DESKTOP-B7GM3C5:~#
```

- [Check]Configure Docker to start on boot with systemd    
	`systemctl status docker.service`, `systemctl status containerd.service`    
	`systemctl enable docker.service`, `systemctl enable containerd.service`    
	`systemctl disable docker.service`, `systemctl disable containerd.service`    

```bash
jaoneol@DESKTOP-B7GM3C5:~$ systemctl status docker.service
● docker.service - Docker Application Container Engine
     Loaded: loaded (/usr/lib/systemd/system/docker.service; enabled; preset: enabled)
     Active: active (running) since Sat 2025-02-01 21:43:54 KST; 1h 2min ago
TriggeredBy: ● docker.socket
       Docs: https://docs.docker.com
   Main PID: 253 (dockerd)
      Tasks: 15
     Memory: 90.5M ()
     CGroup: /system.slice/docker.service
             └─253 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock

Feb 01 21:43:54 DESKTOP-B7GM3C5 dockerd[253]: time="2025-02-01T21:43:54.574207734+09:00" level=info msg="Default bridge (docker0) is assigned with an IP a>
Feb 01 21:43:54 DESKTOP-B7GM3C5 dockerd[253]: time="2025-02-01T21:43:54.639275980+09:00" level=info msg="Loading containers: done."
Feb 01 21:43:54 DESKTOP-B7GM3C5 dockerd[253]: time="2025-02-01T21:43:54.647986556+09:00" level=warning msg="WARNING: No blkio throttle.read_bps_device sup>
Feb 01 21:43:54 DESKTOP-B7GM3C5 dockerd[253]: time="2025-02-01T21:43:54.648005314+09:00" level=warning msg="WARNING: No blkio throttle.write_bps_device su>
Feb 01 21:43:54 DESKTOP-B7GM3C5 dockerd[253]: time="2025-02-01T21:43:54.648007850+09:00" level=warning msg="WARNING: No blkio throttle.read_iops_device su>
Feb 01 21:43:54 DESKTOP-B7GM3C5 dockerd[253]: time="2025-02-01T21:43:54.648009935+09:00" level=warning msg="WARNING: No blkio throttle.write_iops_device s>
Feb 01 21:43:54 DESKTOP-B7GM3C5 dockerd[253]: time="2025-02-01T21:43:54.648018842+09:00" level=info msg="Docker daemon" commit=4c9b3b0 containerd-snapshot>
Feb 01 21:43:54 DESKTOP-B7GM3C5 dockerd[253]: time="2025-02-01T21:43:54.648175311+09:00" level=info msg="Daemon has completed initialization"
Feb 01 21:43:54 DESKTOP-B7GM3C5 dockerd[253]: time="2025-02-01T21:43:54.682162247+09:00" level=info msg="API listen on /run/docker.sock"
Feb 01 21:43:54 DESKTOP-B7GM3C5 systemd[1]: Started docker.service - Docker Application Container Engine.
```
