---
title: Linux Container and Docker Container
description: Let's Understand the Features of Linux/Docker Container.
author: DS2Man
date: 2024-10-11 11:00:00 +0000
categories: [Docker, D-Understanding]
tags:
  - WSL
  - Ubuntu
  - Docker
  - Command
math: true
pin: true
---

## The early concept of containers in Linux(chroot).

When a specific directory path is set as the root directory using `chroot`, a **chroot jail** is created. A chroot jail refers to an environment where files and directory resources outside the chrooted root directory cannot be accessed. Because of this isolation, `chroot` was used to minimize data leaks and security risks.    
What was the drawback of `chroot`? The main issue was with libraries. When a process changes its root directory using `chroot`, the libraries it previously referenced may reside in the parent directory of the new root directory, making them inaccessible.

<!--
chroot를 통해 특정 경로의 디렉터리를 루트 디렉터리로 설정하게 되면 chroot 감옥(chroot jail)이 생성되는데, chroot jail은 chroot로 생성된 루트 디렉터리 안에서는 그 밖의 파일 및 디렉터리 자원에 접근할 수 없는 환경을 말합니다. 따라서 chroot는 디렉터리 경로를 격리시킴으로써 데이터 유출 및 피해를 최소화 하는데 사용되었습니다.
chroot의 단점은 무엇이었을 까요? 바로 라이브러리 문제입니다. 프로세스 상에서 chroot를 통해 root directory를 변경하게 된다면 기존에 참조해서 사용하던 library가 변경된 root directory의 상위 directory에 존재하게 된다면 당연히 사용하지 못하게 됩니다.
-->

![chroot](/assets/img/docker/2024-10-11-Docker-Understanding1_1.png)
_chroot Features_

## LXC(LinuX Container)

**Linux Containers(LXC)** were developed to overcome the limitations of `chroot`.  When thinking of virtualization technology, one might consider Virtual Machines, but unlike Virtual Machines, LXC does not virtualize the operating system itself. Instead, it utilizes the host OS as it is.    
LXC isolates elements such as the process tree, user accounts, file system, and IPC for each container, creating a virtual space separate from the host. It also allocates resources like CPU, memory, and network to each container. In this context, `libvirt` is a collection of APIs, daemons, and management tools that support virtualization in Linux.    
In the Linux kernel, <ins>**cgroups(Control Groups) provide a virtualized space where resources such as CPU, memory, storage, and network can be allocated**</ins>. Additionally, <ins>**namespaces(Namespace isolation) isolate elements such as the process tree, user accounts, file system, and IPC, creating a fully isolated environment from the host OS**</ins>.

- **cgroups**: Provides a virtualized space where CPU, memory, disk, and network resources are allocated.
- **namespace**: Separates the file system, user accounts, and other system components from the host OS (the OS installed on the hardware).

LXC enhances `chroot` by leveraging these system-level virtualization technologies.

<!--
Linux Container는 chroot의 단점을 보완하기 위해 나온 기술입니다. 가상화 기술이라고 하면 Virtual Machine을 생각 할 수도 있지만 Virtual Machine과는 다르게 OS자체는 가상화하지 않습니다. LXC는 호스트의 OS를 그대로 이용합니다.

process tree, user account, file system, IPC 등을 컨테이너 마다 격리시켜 호스트와 별개의 가상 공간을 만들고 CPU,메모리,네트워크 등의 자원을 컨테이너마다 할당해줍니다. 이 때 libvirt는 Linux의 가상화를 지원하기 위해 API, 데몬, 그리고 관리 툴들의 집합입니다. 

리눅스 커널에서 cgroups(Control Groups)는 CPU, 메모리, 보조기억장치, 네트워크 등의 자원을 할당되는 가상화 공간을 제공합니다. 그리고 process tree, 사용자 계정, file system, IPC 등을 격리시켜서 Host OS와 완벽하게 격리된 공간을 만드는데 이를 namespaces(Namespace isolation) 이라고 합니다.

- cgroups : CPU, 메모리, 디스크, 네트워크 자원을 할당되는 가상화 공간을 제공한다.
- namespace : 파일시스템, 사용자 계정 등을 호스트 OS(하드웨어에 설치된 OS)와 분리시킨다.

LXC는 이러한 시스템 레벨 가상화 기술을 통해 chroot의 단점을 보완하였습니다.
-->

![LXC](/assets/img/docker/2024-10-11-Docker-Understanding1_2.png)
_LXC Features_


## Docker Container

As mentioned earlier, while LXC provided an isolated environment, it lacked essential features for effectively running services within it. Docker, built on **cgroups** and **namespaces**, extends these capabilities by offering functionalities such as image and container management, making it more practical for real-world use.    
Like LXC, Docker runs multiple containers on a single OS. Therefore, there is no need to install a guest OS for each process (as in Virtual Machines), and the system call process for accessing hardware resources is relatively simple. Since Docker provides a lightweight virtualization technology compared to traditional virtualization, it is highly scalable. Users can create containers as needed and easily remove them when they are no longer required.

While Docker shares similarities with LXC, there are clear differences. The table below summarizes these distinctions.

|**Category**|**LXC**|**Docker**|
|---|---|---|
|**Isolation Level**|Provides a full OS environment similar to a VM|Isolates at the application level|
|**Kernel Sharing**|Shares the host OS kernel|Shares the host OS kernel (**optimized for running containerized applications**)|
|**File System**|Provides a complete root file system (including `/bin`, `/lib`, `/etc`)|Uses a layered file system (OverlayFS) for lightweight operation(`/var/lib/docker/overlay2`)|
|**Networking**|Supports a separate network stack (can assign a physical interface)|Uses NAT-based networking by default|
|**Process Management**|Can run multiple processes similar to the host OS|Typically runs a single main process inside the container|
|**Advantages**|High flexibility, provides a full OS environment|**Lightweight, fast deployment, portability**|
|**Disadvantages**|Relatively heavy, requires VM-like configuration|Limited OS environment, single-process execution model|

<!--
앞서 말한 LXC는 격리된 환경을 제공하기는 했으나, 실질적으로 그 안에서 서비스를 운영하기 위한 기능이 부족했습니다. 도커는 cgroups와 namespaces를 기반으로 하면서도 이미지와 컨테이너 관리 등의 다양한 기능들을 제공함으로써 실질적으로 필요한 기능들을 제공합니다

도커는 LXC와 마찬가지로 하나의 OS를 통해 여러 컨테이너를 구동시킨다. 따라서 각 프로세스마다 게스트 OS를 설치(Virtual Machine)할 필요도 없고, 하드웨어 자원을 사용하기 위한 시스템 콜 절차도 비교적 간단하다. 가상화에 비해 가벼운 가상화 기술을 제공하는 도커는 가볍기 때문에 확장성이 용이하다. 내가 필요한 기능이 있다면 얼마든지 컨테이너로 만들고, 필요없다면 손쉽게 삭제할 수 있기 때문이다.
LXC와 유사하면서도  차이점은 분명히 있다. 아래 표에 정리해 보았다.

|**항목**|**LXC**|**Docker**|
|---|---|---|
|격리 수준|VM과 유사한 전체 OS 환경 제공|애플리케이션 단위로 격리|
|커널 공유|호스트 OS 커널 공유|호스트 OS 커널 공유 (단, **컨테이너화된 애플리케이션 실행에 최적화**)|
|파일 시스템|완전한 루트 파일 시스템 제공 (`/bin`, `/lib`, `/etc` 포함)|레이어드 파일 시스템(OverlayFS) 사용하여 경량화|
|네트워크|별도 네트워크 스택 지원 (물리적 인터페이스 할당 가능)|기본적으로 NAT 기반 네트워크 사용|
|프로세스 관리|호스트 OS와 유사하게 여러 프로세스를 실행 가능|컨테이너 내부에서 하나의 메인 프로세스를 실행하는 방식이 일반적|
|장점|높은 자유도, 전체 OS 환경 제공|**경량, 빠른 배포, 이식성**|
|단점|상대적으로 무거움, VM과 유사한 설정 필요|제한적인 OS 환경, 단일 프로세스 실행 방식|
-->

![Docker](/assets/img/docker/2024-10-11-Docker-Understanding1_3.png)
_Docker containers(Source: [Docker Homepage](https://www.docker.com/resources/what-container/))_
