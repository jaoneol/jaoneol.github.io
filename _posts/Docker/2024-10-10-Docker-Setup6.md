---
title: NVIDIA Contrainer Toolkit
description: Let's Use GPU on Docker Container
author: DS2Man
date: 2024-10-10 15:00:00 +0000
categories: [Docker, D-Setup]
tags:
  - WSL
  - Ubuntu
  - Docker
  - GPU
math: true
pin: true
---

<!--
https://maxima-lab.tistory.com/entry/Docker-NVIDIA-Container-Toolkit-Installation
https://dongle94.github.io/docker/docker-nvidia-docker-install/
https://maxima-lab.tistory.com/entry/Docker-NVIDIA-Container-Toolkit-Installation
https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html
-->

## *About NVIDIA Container Toolkit*

In the previous post, we installed Docker Engine. However, with the advancement of AI, the need to use NVIDIA GPUs on Docker has been increasing. To address this, NVIDIA provides a plugin called the **NVIDIA Container Toolkit**. The purpose of installing the NVIDIA Container Toolkit is to enable efficient utilization of NVIDIA GPUs within Docker containers, making it easier to deploy and run GPU-accelerated applications. I referred to the manual provided by NVIDIA ([NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/index.html)).   

<!--
이전 글에서 우리는 Docker Engine을 설치해 보았다. 그러나 AI의 발달로 Docker 위에서 NVIDIA GPU를 사용해야 하는 경우가 증가하고 있다. 이를 위해서 NVIDIA가 제공하는 플러그인이 NVIDIA Container Toolkit 이다. NVIDIA Container Toolkit를 설치하는 목적은 Docker 컨테이너 내에서 NVIDIA GPU를 효율적으로 활용할 수 있게 하여 GPU 가속화 애플리케이션의 배포와 실행을 용이하기 하기 위해 사용합니다. NVIDIA에서 제공하는 Manual([NVIDIA Container Tookit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/index.html))을 참고하였다.
-->

![NVIDIA Container Toolkit](/assets/img/docker/2024-10-10-Docker-Setup6_1.png)
_NVIDIA Container Toolkit(Source: [NVIDIA](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/index.html))_

## *Prerequisites*

Install the NVIDIA GPU driver for your Linux distribution. NVIDIA recommends installing the driver by using the package manager for your distribution. For information about installing the driver with a package manager, refer to the [NVIDIA Driver Installation Quickstart Guide](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html).
This is based on my working environment, so please take it as a reference.

<!--
나의 작업 환경이니 참고하시기 바란다.
-->

|Item|Details|
|:---:|:---:|
|**GPU**|GeForce RTX 4070 Super|
|**NVIDIA GPU Driver**|560.94|
|**OS**|Ubuntu 24.04.1 LTS|
|**Docker Engine**|27.5.1|

- NVIDIA GPU Driver

```bash
(base1) jaoneol@DESKTOP-B7GM3C5:~$ nvidia-smi
Mon Feb 17 12:45:09 2025       
+-----------------------------------------------------------------------------------------+
| NVIDIA-SMI 560.35.02              Driver Version: 560.94         CUDA Version: 12.6     |
|-----------------------------------------+------------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id          Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |           Memory-Usage | GPU-Util  Compute M. |
|                                         |                        |               MIG M. |
|=========================================+========================+======================|
|   0  NVIDIA GeForce RTX 4070 ...    On  |   00000000:01:00.0  On |                  N/A |
|  0%   37C    P8              9W /  220W |     702MiB /  12282MiB |      1%      Default |
|                                         |                        |                  N/A |
+-----------------------------------------+------------------------+----------------------+
                                                                                         
+-----------------------------------------------------------------------------------------+
| Processes:                                                                              |
|  GPU   GI   CI        PID   Type   Process name                              GPU Memory |
|        ID   ID                                                               Usage      |
|=========================================================================================|
|    0   N/A  N/A        26      G   /Xwayland                                   N/A      |
+-----------------------------------------------------------------------------------------+
(base) jaoneol@DESKTOP-B7GM3C5:~$ 

```

- OS

```bash
(base) jaoneol@DESKTOP-B7GM3C5:~$ lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 24.04.1 LTS
Release:        24.04
Codename:       noble
(base) jaoneol@DESKTOP-B7GM3C5:~$ 
```

- Docker Engine

```bash
(base) jaoneol@DESKTOP-B7GM3C5:~$ docker version
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
(base) jaoneol@DESKTOP-B7GM3C5:~$ 
```

## *Install NVIDIA Contrainer Toolkit*

I wrote this based on [NVIDIA](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html) to install the NVIDIA Container Toolkit.
<!--
NVIDIA Contrainer Toolkit 설치 위해 [NVIDIA](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html)를 기반으로 작성하였다.
-->

- 1.Configure the production repository    
	The `nvidia-container-toolkit-keyring.gpg` file stores NVIDIA's official GPG key. The GPG(GNU Privacy Guard) key is used to verify the integrity and origin of packages, ensuring that the software downloaded through the package management system is genuinely provided by NVIDIA.    
	`curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg`    
	
	The file `libnvidia-container/stable/debian11/libnvidia-container.list` contains the list of NVIDIA's APT package repositories.     
	The `sed` command is used to find and replace strings. Through this process, `[signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg]` is added to configure package verification using NVIDIA's official GPG key.    
	The `tee` is a command that saves the output of a command to a file.    
	`curl -s -L https://nvidia.github.io/libnvidia-container/stable/debian11/libnvidia-container.list | \
    sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
    sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list`
  <br>
- 2.Update the packages list from the repository    
	`sudo apt-get update`    

- 3.Install the NVIDIA Container Toolkit packages    
	`sudo apt-get install -y nvidia-container-toolkit`

```bash
# An error occurs indicating that the GPU cannot be used because the NVIDIA Container Toolkit is not installed.
(base) jaoneol@DESKTOP-B7GM3C5:~$ sudo docker run --rm --runtime=nvidia --gpus all ubuntu:24.04 nvidia-smi
[sudo] password for jaoneol: 
docker: Error response from daemon: unknown or invalid runtime name: nvidia.
See 'docker run --help'.
(base) jaoneol@DESKTOP-B7GM3C5:~$ 
```

```bash
# The file `/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg` is created.
# The `nvidia-container-toolkit-keyring.gpg` file stores NVIDIA's official GPG key. 
# The GPG(GNU Privacy Guard) key is used to verify the integrity and origin of packages, ensuring that the software downloaded through the package management system is genuinely provided by NVIDIA.    	
(base) jaoneol@DESKTOP-B7GM3C5:~$ curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg

# The GPG key is recorded in `/etc/apt/sources.list.d/nvidia-container-toolkit.list`.
# The file `libnvidia-container/stable/debian11/libnvidia-container.list` contains the list of NVIDIA's APT package repositories.     
# The `sed` command is used to find and replace strings. 
# Through this process, `[signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg]` is added to configure package verification using NVIDIA's official GPG key.    
(base) jaoneol@DESKTOP-B7GM3C5:~$ curl -s -L https://nvidia.github.io/libnvidia-container/stable/debian11/libnvidia-container.list | \
    sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
    sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://nvidia.github.io/libnvidia-container/stable/deb/$(ARCH) /
deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://nvidia.github.io/libnvidia-container/stable/debian10/$(ARCH) /
#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://nvidia.github.io/libnvidia-container/experimental/deb/$(ARCH) /
#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://nvidia.github.io/libnvidia-container/experimental/debian10/$(ARCH) /

(base) jaoneol@DESKTOP-B7GM3C5:~$ cat /etc/apt/sources.list.d/nvidia-container-toolkit.list
deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://nvidia.github.io/libnvidia-container/stable/deb/$(ARCH) /
deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://nvidia.github.io/libnvidia-container/stable/debian10/$(ARCH) /
#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://nvidia.github.io/libnvidia-container/experimental/deb/$(ARCH) /
#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://nvidia.github.io/libnvidia-container/experimental/debian10/$(ARCH) /
(base) jaoneol@DESKTOP-B7GM3C5:~$ 
```

```bash
(base) jaoneol@DESKTOP-B7GM3C5:~$ sudo apt-get update
Hit:1 http://mirror.kakao.com/ubuntu noble InRelease
Hit:2 http://mirror.kakao.com/ubuntu noble-updates InRelease           
Hit:3 http://mirror.kakao.com/ubuntu noble-backports InRelease         
Hit:4 http://mirror.kakao.com/ubuntu noble-security InRelease          
Hit:5 https://download.docker.com/linux/ubuntu noble InRelease         
Get:6 https://nvidia.github.io/libnvidia-container/stable/deb/amd64  InRelease [1477 B]
Get:7 https://nvidia.github.io/libnvidia-container/stable/debian10/amd64  InRelease [1484 B]
Get:8 https://nvidia.github.io/libnvidia-container/stable/deb/amd64  Packages [16.7 kB]
Get:9 https://nvidia.github.io/libnvidia-container/stable/debian10/amd64  Packages [25.4 kB]
Fetched 45.0 kB in 1s (45.4 kB/s)  
Reading package lists... Done
(base) jaoneol@DESKTOP-B7GM3C5:~$ 
```

```bash
# Install the NVIDIA Container Toolkit packages
(base) jaoneol@DESKTOP-B7GM3C5:~$ sudo apt-get install -y nvidia-container-toolkit
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  libnvidia-container-tools libnvidia-container1 nvidia-container-toolkit-base
The following NEW packages will be installed:
  libnvidia-container-tools libnvidia-container1 nvidia-container-toolkit nvidia-container-toolkit-base
0 upgraded, 4 newly installed, 0 to remove and 76 not upgraded.
Need to get 5805 kB of archives.
After this operation, 27.7 MB of additional disk space will be used.
Get:1 https://nvidia.github.io/libnvidia-container/stable/deb/amd64  libnvidia-container1 1.17.4-1 [925 kB]
Get:2 https://nvidia.github.io/libnvidia-container/stable/deb/amd64  libnvidia-container-tools 1.17.4-1 [20.2 kB]
Get:3 https://nvidia.github.io/libnvidia-container/stable/deb/amd64  nvidia-container-toolkit-base 1.17.4-1 [3672 kB]
Get:4 https://nvidia.github.io/libnvidia-container/stable/deb/amd64  nvidia-container-toolkit 1.17.4-1 [1188 kB]
Fetched 5805 kB in 1s (10.7 MB/s)                  
Selecting previously unselected package libnvidia-container1:amd64.
(Reading database ... 41364 files and directories currently installed.)
Preparing to unpack .../libnvidia-container1_1.17.4-1_amd64.deb ...
Unpacking libnvidia-container1:amd64 (1.17.4-1) ...
Selecting previously unselected package libnvidia-container-tools.
Preparing to unpack .../libnvidia-container-tools_1.17.4-1_amd64.deb ...
Unpacking libnvidia-container-tools (1.17.4-1) ...
Selecting previously unselected package nvidia-container-toolkit-base.
Preparing to unpack .../nvidia-container-toolkit-base_1.17.4-1_amd64.deb ...
Unpacking nvidia-container-toolkit-base (1.17.4-1) ...
Selecting previously unselected package nvidia-container-toolkit.
Preparing to unpack .../nvidia-container-toolkit_1.17.4-1_amd64.deb ...
Unpacking nvidia-container-toolkit (1.17.4-1) ...
Setting up nvidia-container-toolkit-base (1.17.4-1) ...
Setting up libnvidia-container1:amd64 (1.17.4-1) ...
Setting up libnvidia-container-tools (1.17.4-1) ...
Setting up nvidia-container-toolkit (1.17.4-1) ...
Processing triggers for libc-bin (2.39-0ubuntu8.4) ...
(base) jaoneol@DESKTOP-B7GM3C5:~$ 
```

## *Configuring Docker*

- 1.Configure the container runtime by using the `nvidia-ctk` command. The `nvidia-ctk` command modifies the `/etc/docker/daemon.json` file on the host. The file is updated so that Docker can use the NVIDIA Container Runtime.  **This configuration ensures that all runtimes use the NVIDIA runtime.**    
	`sudo nvidia-ctk runtime configure --runtime=docker`    

- 2.Restart the Docker daemon    
	`sudo systemctl restart docker`
	
```bash
# The file `/etc/docker/daemon.json` is created.
# The file is updated so that Docker can use the NVIDIA Container Runtime. 
# This configuration ensures that all runtimes use the NVIDIA runtime.
(base) jaoneol@DESKTOP-B7GM3C5:~$ sudo nvidia-ctk runtime configure --runtime=docker
INFO[0000] Loading config from /etc/docker/daemon.json  
INFO[0000] Wrote updated config to /etc/docker/daemon.json 
INFO[0000] It is recommended that docker daemon be restarted. 

(base) jaoneol@DESKTOP-B7GM3C5:~$ cat /etc/docker/daemon.json
{
    "runtimes": {
        "nvidia": {
            "args": [],
            "path": "nvidia-container-runtime"
        }
    }
}

(base) jaoneol@DESKTOP-B7GM3C5:~$ sudo systemctl restart docker
(base) jaoneol@DESKTOP-B7GM3C5:~$ 
```

## *Running a Sample Workload with Docker*

After you install and configure the toolkit and install an NVIDIA GPU Driver, you can verify your installation by running a sample workload. I wrote this based on [NVIDIA](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/sample-workload.html) to install the NVIDIA Container Toolkit.

- 1.Run a sample CUDA container    
	`sudo docker run --rm --runtime=nvidia --gpus all ubuntu:24.04 nvidia-smi`    

- 2.To verify that PyTorch is running with GPU support, you can run the following Python commands    
	`docker run --rm --gpus all pytorch/pytorch:latest python -c "import torch; print(torch.cuda.is_available())"`
	
```bash
# If `--runtime=nvidia --gpus all` is not specified, an error will occur.
(base) jaoneol@DESKTOP-B7GM3C5:~$ docker run --rm ubuntu:24.04 nvidia-smi
docker: Error response from daemon: failed to create task for container: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: error during container init: exec: "nvidia-smi": executable file not found in $PATH: unknown.
(base) jaoneol@DESKTOP-B7GM3C5:~$ 

# --rm : Automatically deletes the container after it exits.
# --runtime=nvidia : Sets the runtime for Docker to the NVIDIA runtime, enabling the use of NVIDIA GPUs.
# --gpus all : This option allows the use of all GPUs in the container.
# For reference, Previously, we executed `nvidia-ctk runtime configure --runtime=docker` to configure all runtimes to use the NVIDIA runtime.
(base) jaoneol@DESKTOP-B7GM3C5:~$ sudo docker run --rm --gpus all ubuntu:24.04 nvidia-smi
(base) jaoneol@DESKTOP-B7GM3C5:~$ sudo docker run --rm --runtime=nvidia --gpus all ubuntu:24.04 nvidia-smi
[sudo] password for jaoneol: 
Unable to find image 'ubuntu:24.04' locally
24.04: Pulling from library/ubuntu
5a7813e071bf: Pull complete 
Digest: sha256:72297848456d5d37d1262630108ab308d3e9ec7ed1c3286a32fe09856619a782
Status: Downloaded newer image for ubuntu:24.04
Mon Feb 17 02:56:48 2025       
+-----------------------------------------------------------------------------------------+
| NVIDIA-SMI 560.35.02              Driver Version: 560.94         CUDA Version: 12.6     |
|-----------------------------------------+------------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id          Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |           Memory-Usage | GPU-Util  Compute M. |
|                                         |                        |               MIG M. |
|=========================================+========================+======================|
|   0  NVIDIA GeForce RTX 4070 ...    On  |   00000000:01:00.0  On |                  N/A |
|  0%   30C    P8              8W /  220W |     557MiB /  12282MiB |      0%      Default |
|                                         |                        |                  N/A |
+-----------------------------------------+------------------------+----------------------+
                                                                                         
+-----------------------------------------------------------------------------------------+
| Processes:                                                                              |
|  GPU   GI   CI        PID   Type   Process name                              GPU Memory |
|        ID   ID                                                               Usage      |
|=========================================================================================|
|    0   N/A  N/A        26      G   /Xwayland                                   N/A      |
+-----------------------------------------------------------------------------------------+
(base) jaoneol@DESKTOP-B7GM3C5:~$ 
```

```bash
(base) jaoneol@DESKTOP-B7GM3C5:~$ docker run --rm --gpus all pytorch/pytorch:latest python -c "import torch; print(torch.cuda.is_available())"
Unable to find image 'pytorch/pytorch:latest' locally
latest: Pulling from pytorch/pytorch
d66d6a6a3687: Pull complete 
3ad96c6a423a: Pull complete 
3d552c93e873: Pull complete 
4f4fb700ef54: Pull complete 
42a8919f2ed9: Pull complete 
Digest: sha256:11691e035a3651d25a87116b4f6adc113a27a29d8f5a6a583f8569e0ee5ff897
Status: Downloaded newer image for pytorch/pytorch:latest
True
(base) jaoneol@DESKTOP-B7GM3C5:~$ 
```