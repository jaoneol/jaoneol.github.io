---
title: Linux(Ubuntu), Export & Import
description: Let's manage Ubuntu history, just like snapshots in VirtualBox.
author: DS2Man
date: 2024-10-07 11:00:00 +0000
categories: [Docker, D-Setup]
tags:
  - WSL
  - Ubuntu
  - Docker
math: true
pin: true
---

In WSL (Windows Subsystem for Linux), Ubuntu is managed and stored in a virtual disk file called `ext4.vhdx`. Since I plan to conduct various tests, there will be times when I need to revert to a previous version. <ins>VirtualBox provides a snapshot feature, but unfortunately, WSL does not.</ins>Therefore, I will **use WSL's export and import functions to manage system versions**.

<!--
WSL(Windows Subsystem for Linux)에서 Ubuntu는 가상 디스크 파일인 ext4.vhdx에 저장이 되어서 관리되고 있다. 나는 앞으로 다양한 테스트를 시도할 것인데, 과거 버전으로 원복이 필요할 때가 발생할 것이다. VirtualBox는 스냅샷 기능을 지원하는데, 안타깝게도 WSL은 그런 기능을 지원하지 않는다. 그래서 wsl의 export와 import 기능을 통해 형상 관리를 하겠다.
-->

## *Export & Import Ubuntu*

Follow the commands below to proceed. Be cautious: **if you use `wsl --unregister Ubuntu`, the `ext4.vhdx` file will also be deleted!!!** Use it carefully.    

`wsl --export  Ubuntu "D:\01.WSL\ubuntu-1-install_wsl.tar"`        
`wsl --unregister Ubuntu`     
`wsl --import Ubuntu "D:\01.WSL" "D:\01.WSL\ubuntu-1-install_wsl.tar"`    
`ubuntu config --default-user jaoneol`

<!--
아래 명령어대로 진행하면 된다. 주의할 점은 `wsl --unregister Ubuntu`  사용하면 the `ext4.vhdx` file will also be deleted!!! 유의해서 사용하기 바란다.
-->

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
######################################################################
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