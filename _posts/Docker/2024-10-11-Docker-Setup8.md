---
title: WSL Management
description: Let's Manage WSL(ext4.vhdx) properly.
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

We have installed WSL (Windows Subsystem for Linux) on Windows and can now use Ubuntu.    
[WSL, Running Linux on Windows](/posts/Docker-Setup1)
    
When Ubuntu is installed on WSL, a file called `ext4.vhdx` is created. Ubuntu is managed and stored in a virtual disk file called `ext4.vhdx`. Since I plan to conduct various tests, there will be times when I need to revert to a previous version. VirtualBox provides a snapshot feature, but unfortunately, WSL does not. Therefore, I will **use WSL's export and import functions to manage system versions**.    
[Linux(Ubuntu), Export & Import](/posts/Docker-Setup2)
    
When installing various packages on Ubuntu, the packages are deleted, but the size of `ext4.vhdx` does not decrease. To resolve this, you need to use `diskpart` to clean up the files. This program is used for disk cleanup. Enter the following commands in order.    
[(Option) Ollama on Ubuntu](/posts/Docker-Setup7)

I wrote this post as a review of this topic.

<!--
- 우리는 Windows에 WSL(Windows Subsystem for Linux)를 설치하고, Ubuntu를 사용할 수 있게 되었다. [WSL, Running Linux on Windows](/posts/Docker-Setup1)   
-WSL에 Ubuntu를 설치하고 나면 생성되는 파일이 ext4.vhdx 라는 파일이다. Ubuntu is managed and stored in a virtual disk file called `ext4.vhdx`. Since I plan to conduct various tests, there will be times when I need to revert to a previous version. VirtualBox provides a snapshot feature, but unfortunately, WSL does not.Therefore, I will **use WSL's export and import functions to manage system versions**. [Linux(Ubuntu), Export & Import](/posts/Docker-Setup2)   
- When install  various packages on Ubuntu, the packages are deleted, but the size of `ext4.vhdx` does not decrease. To resolve this, you need to use `diskpart` to clean up the files. This program is used for disk cleanup. Enter the following commands in order. [(Option) Ollama on Ubuntu](/posts/Docker-Setup7)    
-->

## *Cleans up the disk*

Run the `diskpart` command. This program cleans up the disk(`ext4.vhdx`).    
`select vdisk file="D:\01.WSL\ext4.vhdx"`   
`attach vdisk readonly`   
`compact vdisk`   
`detach vdisk`   

```bash
# Run the `diskpart` command. This program cleans up the disk.
PS C:\Users\ycjang> diskpart
PS C:\Users\ycjang>

# run C:\Windows\system32\diskpart.exe
Microsoft DiskPart 버전 10.0.19041.3636

Copyright (C) Microsoft Corporation.
컴퓨터: DESKTOP-B7GM3C5

DISKPART> select vdisk file="D:\01.WSL\ext4.vhdx"
DiskPart가 가상 디스크 파일을 선택했습니다.

DISKPART> attach vdisk readonly
  100 퍼센트 완료
DiskPart가 가상 디스크 파일을 연결했습니다.

DISKPART> compact vdisk
  100 퍼센트 완료
DiskPart가 가상 디스크 파일을 압축했습니다.

DISKPART> detach vdisk
DiskPart가 가상 디스크 파일을 분리했습니다.
DISKPART>
```

## *Stop all docker container*

If you proceed with exporting Ubuntu while the container in use is still active, a warning about data consistency issues will appear (though the process will still continue). Therefore, it is recommended to stop the relevant container before proceeding with the export.    
`docker compose -p ds2man stop`   
`docker stop mysql`   

```bash
(base) jaoneol@DESKTOP-B7GM3C5:~$ docker compose -p ds2man stop
[+] Stopping 4/4
 ✔ Container attu               Stopped           0.3s
 ✔ Container milvus-standalone  Stopped          10.3s
 ✔ Container milvus-minio       Stopped           0.9s
 ✔ Container milvus-etcd        Stopped           0.4s

(base) jaoneol@DESKTOP-B7GM3C5:~$ docker stop mysql
mysql
(base) jaoneol@DESKTOP-B7GM3C5:~$
```

## *Export Ubuntu*

Follow the commands below to proceed.     
`wsl --export Ubuntu "D:\01.WSL\ubuntu-1-install_wsl.tar"`  

```bash
#####################################################################
PS C:\Windows\system32> wsl -l -v
  NAME      STATE           VERSION
* Ubuntu    Running         2
PS C:\Windows\system32> wsl --shutdown
PS C:\Windows\system32> wsl -l -v
  NAME      STATE           VERSION
* Ubuntu    Stopped         2
#####################################################################
PS C:\Windows\system32> wsl --export  Ubuntu "D:\01.WSL\ubuntu-1-install_wsl.tar"
내보내기가 진행 중입니다. 이 작업은 몇 분 정도 걸릴 수 있습니다...
작업을 완료했습니다.

PS C:\Windows\system32>
```

## *Import Ubuntu*

Be cautious: **if you use `wsl --unregister Ubuntu`, the `ext4.vhdx` file will also be deleted!!!** Use it carefully.    
`wsl --unregister Ubuntu`  
`wsl --import Ubuntu "D:\01.WSL" "D:\01.WSL\ubuntu-1-install_wsl.tar"`  
`ubuntu config --default-user jaoneol`

```bash
# Very Important, Run the following command to remove the distribution.
# Be extremely careful!, If you run this command, the `ext4.vhdx` file will also be deleted!!!
PS C:\Windows\system32> wsl --unregister Ubuntu
등록 취소 중입니다.
작업을 완료했습니다.
######################################################################
# Running this command will create `ext4.vhdx` in the `D:\01.WSL` directory.
PS C:\Windows\system32> wsl --import Ubuntu "D:\01.WSL" "D:\01.WSL\ubuntu-1-install_wsl.tar"
가져오기가 진행 중입니다. 이 작업은 몇 분 정도 걸릴 수 있습니다.
작업을 완료했습니다.
PS C:\Windows\system32> wsl -l -v
  NAME      STATE           VERSION
* Ubuntu    Stopped         2
#####################################################################
# If you run this command, you will log in with the `jaoneol` account instead of the root account when accessing Ubuntu.
PS C:\Windows\system32> ubuntu config --default-user jaoneol
PS C:\Windows\system32>
```