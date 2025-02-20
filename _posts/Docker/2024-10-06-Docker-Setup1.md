---
title: WSL, Running Linux on Windows
description: Let's Install Linux on Windows 10 (11) Using WSL Virtualization.
author: DS2Man
date: 2024-10-06 11:00:00 +0000
categories: [Docker, D-Setup]
tags:
  - WSL
  - Ubuntu
  - Docker
math: true
pin: true
---

**WSL (Windows Subsystem for Linux)** is a technology that allows you to <ins>use Linux on Windows without creating a virtual machine by leveraging Windows' virtualization feature, **Hyper-V**(WSL operates based on Hyper-V, but it does not use Hyper-V.!)</ins>.   
WSL lets developers run a GNU/Linux environment -- including most command-line tools, utilities, and applications -- directly on Windows, unmodified, without the overhead of a traditional virtual machine or dual-boot setup.  
This document is based on Microsoft's WSL documentation ([https://learn.microsoft.com/en-us/windows/wsl/](https://learn.microsoft.com/en-us/windows/wsl/)).

![WSL Intro](/assets/img/docker/2024-10-07-Docker-Setup1_1.png)
_WSL Intro(Source: [WikiDocs](https://wikidocs.net/252140))_

<!--
WSL(Windows Subsystem for Linux)란 윈도우의 가상화 기능(Hyper-V)을 사용해, 윈도우 위에서 가상머신을 생성 없이 리눅스를 사용할 수 있도록 도와주는 기술입니다. (WSL는 Hyper-V 기반으로 동작하는 거지 Hyper-V를 사용하는 것은 아닙니다.!)
WSL(Linux용 Windows 하위 시스템)을 사용하면 개발자가 기존 가상 머신의 오버헤드 또는 듀얼 부팅 설정 없이 대부분의 명령줄 도구, 유틸리티 및 애플리케이션을 비롯한 GNU/Linux 환경을 수정하지 않고 Windows에서 직접 실행할 수 있습니다.

MS의 WSL문서(https://learn.microsoft.com/en-us/windows/wsl/) 기준으로 작성되었습니다.
-->

## *Key Features of WSL*

1. You can download and install various distributions such as Ubuntu, Debian, and Fedora.  
2. You can use Linux commands like `bash`, `grep`, and `awk`.  
3. It is possible to share the file system between Linux and Windows.  
4. Developers can install essential tools and applications (e.g., Docker, Python) in a Linux environment while simultaneously taking advantage of Windows' convenience.

<!--
Ubuntu, Debian, Fedora 등 다양한 배포판을 다운로드하여 설치할수 있음.
bash, grep, awk와 같은 linux 명령어를 사용할수 있음.
Linux와 Windows 간에 파일 시스템을 공유할 수 있음.
개발자들이 Linux 환경에서 필요한 툴과 애플리케이션(예. Docker, Python등)을 설치하고, Windows의 편리함을 동시에 활용 가능
-->

## *Install WSL*

- Install WSL(default OS : Ubuntu)   
	To use the command below, you must be running Windows 10 version 2004 or later (build 19041 or higher) or Windows 11([Installation page](https://learn.microsoft.com/en-us/windows/wsl/install)). If you're running an older build, or just prefer not to use the install command and would like step-by-step directions~~(in my case, I also had to install it manually due to company security policies, and additionally, proxy settings needed to be configured)~~, see **[WSL manual installation steps for older versions](https://learn.microsoft.com/en-us/windows/wsl/install-manual)**.

	`wsl --install(To provide more details, wsl --install -d Ubuntu`        
	`wsl --set-default-version 2(if the version is not 2)`

<!--
아래 명령을 사용하려면 Windows 10 버전 2004 이상(빌드 19041 이상) 또는 Windows 11을 실행해야 합니다. 이전 버전을 사용 중인 경우 [수동 설치 페이지](https://learn.microsoft.com/ko-kr/windows/wsl/install-manual)를 참조하세요.
-->

```bash
# Check list of installable items
PS C:\Users\ycjang> wsl -l -o
다음은 설치할 수 있는 유효한 배포판 목록입니다.
'wsl.exe --install <Distro>'를 사용하여 설치합니다.

NAME                            FRIENDLY NAME
Ubuntu                          Ubuntu
Debian                          Debian GNU/Linux
kali-linux                      Kali Linux Rolling
Ubuntu-18.04                    Ubuntu 18.04 LTS
Ubuntu-20.04                    Ubuntu 20.04 LTS
Ubuntu-22.04                    Ubuntu 22.04 LTS
Ubuntu-24.04                    Ubuntu 24.04 LTS
OracleLinux_7_9                 Oracle Linux 7.9
OracleLinux_8_7                 Oracle Linux 8.7
OracleLinux_9_1                 Oracle Linux 9.1
openSUSE-Leap-15.6              openSUSE Leap 15.6
SUSE-Linux-Enterprise-15-SP5    SUSE Linux Enterprise 15 SP5
SUSE-Linux-Enterprise-15-SP6    SUSE Linux Enterprise 15 SP6
openSUSE-Tumbleweed             openSUSE Tumbleweed
# Install WSL, default linux(Ubuntu)
PS C:\Users\ycjang> wsl --install
# Check the running WSL version
PS C:\Users\ycjang> wsl -l -v
  NAME      STATE           VERSION
* Ubuntu    Stopped         2
# Set WSL version 2 if the version is not 2.
PS C:\Users\ycjang> wsl --set-default-version 2
```

![WSL Installation](/assets/img/docker/2024-10-07-Docker-Setup1_2.png)
![WSL Installation](/assets/img/docker/2024-10-07-Docker-Setup1_3.png)
_WSL Installation_

- Once Ubuntu installation on WSL is complete, running `uname -a` usually displays the Linux kernel version. To check the exact Ubuntu version, use the following command:    
	`lsb_release -a`    
	`cat /etc/os-release`

<!--
WSL로 Ubuntu 설치가 완료되면 보통 uname -a 를 통해서는 Linux의 커널 버전이 나온다. 정확한 Ubuntu 버전을 알기 위해서는 아래 명령어를 사용하자.
-->

```bash
jaoneol@DESKTOP-B7GM3C5:~$ lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 24.04.1 LTS
Release:        24.04
Codename:       noble
jaoneol@DESKTOP-B7GM3C5:~$ cat /etc/os-release
PRETTY_NAME="Ubuntu 24.04.1 LTS"
NAME="Ubuntu"
VERSION_ID="24.04"
VERSION="24.04.1 LTS (Noble Numbat)"
VERSION_CODENAME=noble
ID=ubuntu
ID_LIKE=debian
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
UBUNTU_CODENAME=noble
LOGO=ubuntu-logo
```

## *Change the default path of WSL*

The default path for the virtual environment that constitutes WSL is the C drive. If the C drive has limited space, it can restrict Docker image storage. Therefore, I want to change the default path to a specific directory on the D drive.

<!--
WSL을 구성하는 가상 환경의 기본 경로가 C드라이브이다. C드라이브의 용량이 작으면 Docker 이미지 저장에 한계가 있다. 그래서 기본 경로를 D드라이브의 특정 디렉토리로 변경하고자 한다.
-->

- The default path for WSL is as follows. Let's assume that Ubuntu 24.04 is being used in WSL.

<!--
WSL의 기본 경로는 다음과 같다. WSL에서 Ubuntu-24.04 버전을 사용한다고 가정한다.
-->

```bash
C:\Users\ycjang\AppData\Local\Packages\CanonicalGroupLimited.Ubuntu_79rhkp1fndgsc\LocalState\ext4.vhdx
```

- Ideally, it would be best to simply move the VHDX file to the D drive, but since Ubuntu OS was installed while enabling WSL, it was installed as a Windows App and may have other dependencies. Therefore, after moving the files to a different path, additional configurations need to be updated. <ins>Since manually checking and handling all these tasks can be complex, there is a project on [GitHub](https://github.com/pxlrbt/move-wsl) that provides a script for this process.</ins>    
	`wsl --shutdown`    
	`git clone https://github.com/pxlrbt/move-wsl.git`    
	`Set-ExecutionPolicy Unrestricted`    
	`.\move-wsl.ps1`    
	`ubuntu config --default-user jaoneol`    

<!--
단순히 vhdx 파일만 D드라이브로 옮기면 좋겠지만, WSL을 활성화하면서 우분투 OS를 설치했다.
즉, 윈도우 App 형태로 설치한 것이며 다른 의존성이 존재할 수 있으므로, 파일을 다른 경로로 옮기고 나서 부가적인 설정들을 변경해야 한다. 이러한 작업들을 모두 체크해서 진행하긴 어렵고, GitHub에 스크립트 파일을 제공하는 프로젝트가 있다.
https://github.com/pxlrbt/move-wsl
-->

```bash
PS C:\Users\ycjang> wsl -l -v
  NAME      STATE           VERSION
* Ubuntu    Running         2
# You can use the command below, 
# or if you close all WSL Ubuntu windows and wait for a while, 
# it will change to "Stopped."
PS C:\Users\ycjang> wsl --shutdown
PS C:\Users\ycjang> wsl -l -v
  NAME      STATE           VERSION
* Ubuntu    Stopped         2
PS C:\Users\ycjang>
```

```bash
PS C:\Users\ycjang> cd d:
PS D:\> cd .\01.WSL\
PS D:\01.WSL> git clone https://github.com/pxlrbt/move-wsl.git
Cloning into 'move-wsl'...
remote: Enumerating objects: 216, done.
remote: Counting objects: 100% (51/51), done.
remote: Compressing objects: 100% (32/32), done.
remote: Total 216 (delta 24), reused 40 (delta 17), pack-reused 165 (from 1)
Receiving objects: 100% (216/216), 116.42 KiB | 8.96 MiB/s, done.
Resolving deltas: 100% (108/108), done.
PS D:\01.WSL> cd .\move-wsl\
PS D:\01.WSL\move-wsl> .\move-wsl.ps1
.\move-wsl.ps1 : 이 시스템에서 스크립트를 실행할 수 없으므로 D:\01.WSL\move-wsl\move-wsl.ps1 파일을 로드할 수 없습니다
. 자세한 내용은 about_Execution_Policies(https://go.microsoft.com/fwlink/?LinkID=135170)를 참조하십시오.
위치 줄:1 문자:1
+ .\move-wsl.ps1
+ ~~~~~~~~~~~~~~
    + CategoryInfo          : 보안 오류: (:) [], PSSecurityException
    + FullyQualifiedErrorId : UnauthorizedAccess

# Since PowerShell's Execution Policy is set to Restricted, 
# changing it to Unrestricted allows scripts and configuration files to be executed.
PS D:\01.WSL\move-wsl> Set-ExecutionPolicy Unrestricted
```

```bash
# Running it again will proceed normally.
PS D:\01.WSL\move-wsl> .\move-wsl.ps1
Getting distros...
Select distro to move:
1: Ubuntu
1
Enter WSL target directory:
d:\01.WSL
Move Ubuntu to "d:\01.WSL"? (Y|n): Y
Exporting VHDX to "d:\01.WSL\Ubuntu.tar" ...
내보내기가 진행 중입니다. 이 작업은 몇 분 정도 걸릴 수 있습니다.
작업을 완료했습니다.
Unregistering WSL ...
Importing Ubuntu from d:\01.WSL...
가져오기가 진행 중입니다. 이 작업은 몇 분 정도 걸릴 수 있습니다.
작업을 완료했습니다.
Cleaning up ...
Done!
PS D:\01.WSL\move-wsl>
```

![Change the default path of WSL](/assets/img/docker/2024-10-07-Docker-Setup1_7.png)
_Change the default path of WSL_

```bash
PS C:\Users\ycjang> wsl -d Ubuntu
root@DESKTOP-B7GM3C5:/mnt/c/Users/ycjang# exit
logout
PS C:\Users\ycjang> ubuntu
root@DESKTOP-B7GM3C5:~# exit
logout
# If you run this command, you will log in with the `jaoneol` account instead of the root account when accessing Ubuntu.
PS C:\Users\ycjang> ubuntu config --default-user jaoneol
PS C:\Users\ycjang> ubuntu
jaoneol@DESKTOP-B7GM3C5:~$
```

## *Ubuntu Settings*

- Let's change the root account password in Ubuntu.    
	`wsl -u root`

<!--
ubuntu의 root 계정의 Password를 변경하도록 하자.
-->

```bash
PS C:\Users\ycjang> wsl -u root
root@DESKTOP-B7GM3C5:/mnt/c/Users/ycjang# passwd
New password:
Retype new password:
passwd: password updated successfully
root@DESKTOP-B7GM3C5:/mnt/c/Users/ycjang#
```

-  Let's omit the `sudo` command for your account (`jaoneol`), proceed as follows.    
	Add `jaoneol ALL=(ALL:ALL) ALL` to `/etc/sudoers`.

<!--
나의 계정(jaoneol)에서 sudo 명령어를 생략하기를 원하면 다음과 같이 진행하면 됩니다.
-->

```bash
jaoneol@DESKTOP-B7GM3C5:~$ su -
Password:
root@DESKTOP-B7GM3C5:~# chmod +w /etc/sudoers
root@DESKTOP-B7GM3C5:~# vi /etc/sudoers
root@DESKTOP-B7GM3C5:~# vi /etc/sudoers
root@DESKTOP-B7GM3C5:~# chmod -w /etc/sudoers
root@DESKTOP-B7GM3C5:~#
```

```bash
##################
# vi /etc/sudoers
##################
# User privilege specification
root    ALL=(ALL:ALL) ALL
jaoneol ALL=(ALL:ALL) ALL # Add this part.

# Members of the admin group may gain root privileges
%admin ALL=(ALL) ALL

# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL
```

- [Option]Let's Change the APT repository (`ubuntu.sources`) to a domestic server.     
	Modify the file(`etc/apt/sources.list.d/ubuntu.sources`)'s contents using the following command.    
	`:%s/archive.ubuntu.com/mirror.kakao.com/g`    
	`:%s/security.ubuntu.com/mirror.kakao.com/g`

	Below is a list of servers operating in our country.

|Provider|URL|Remarks|
|---|---|---|
|Ubuntu (basic)|[archive.ubuntu.com](https://archive.ubuntu.com/)||
|Ubuntu (KOR basic)|[kr.archive.ubuntu.com](http://kr.archive.ubuntu.com/)|Provided by KAIST|
|Kakao|[mirror.kakao.com](http://mirror.kakao.com/)|Fastest|    

<!--
apt repository(source.list) 국내서버로 변경하기
아래는 우리나라에서 운영중인 서버 리스트 임.
-->

```bash
# Connect to WSL - Ubuntu using an Ubuntu command.
PS C:\Users\ycjang> ubuntu
# Grant root privileges.
jaoneol@DESKTOP-B7GM3C5:~$ su -
Password:
# Open ubuntu.sources using the vi editor.
root@DESKTOP-B7GM3C5:~# vi /etc/apt/sources.list.d/ubuntu.sources
# Update apt repository
root@DESKTOP-B7GM3C5:~# apt update
```

```bash
############################################
# vi etc/apt/sources.list.d/ubuntu.sources
# Change url in ubuntu.sources using this command
# :%s/archive.ubuntu.com/mirror.kakao.com/g 
# :%s/security.ubuntu.com/mirror.kakao.com/g 
############################################
Types: deb
URIs: http://mirror.kakao.com/ubuntu/
Suites: noble noble-updates noble-backports
Components: main universe restricted multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg

## Ubuntu security updates. Aside from URIs and Suites,
## this should mirror your choices in the previous section.
Types: deb
URIs: http://mirror.kakao.com/ubuntu/
Suites: noble-security
Components: main universe restricted multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg
```

- Let's check the wsl's configure(`/etc/wsl.conf`) to use 'systemd'
	`systemd(system daemon)` is <ins>a daemon that replaces the init process</ins> by being the first to be created after a Unix system boots and then executing other processes.    
	In Ubuntu 24.04, systemd is enabled by default, but it is not in older versions(Version 22.04 and older). This was included for verification purposes. If `ps -p 1 -o comm=` does not return `systemd`, the `systemctl` command will not work. Keep this in mind.    		
	Add `[boot] systemd=true` to `/etc/wsl.conf`.

<!--
systemd(system daemon)은 Unix 시스템이 부팅후에 가장 먼저 생성된 후에 다른 프로세스를 실행하는 init 역할을 대체하는 데몬입니다
Ubuntu-24.04에는 기본적으로 systemd가 활성화 되어 있으나, 과거 버전(22.04 포함 과거 버전)은 안되어 있다. 확인차 넣어둔 내용이다. 만약 ps -p 1 -o comm= 에서 systemd가 안되어 있으면 systemctl 명령어가 동작하지 않는다. 유의해라.
-->

```bash
##################
# vi /etc/wsl.conf
##################
# systemd is enable
[boot]
systemd=true
```

```bash
jaoneol@DESKTOP-B7GM3C5:~$ ps -p 1 -o comm=
systemd
jaoneol@DESKTOP-B7GM3C5:~$
```

```bash
jaoneol@DESKTOP-B7GM3C5:~$ ps -ef | grep systemd
root          50       1  0 20:37 ?        00:00:00 /usr/lib/systemd/systemd-journald
root          80       1  0 20:37 ?        00:00:00 /usr/lib/systemd/systemd-udevd
systemd+     102       1  0 20:37 ?        00:00:00 /usr/lib/systemd/systemd-resolved
systemd+     103       1  0 20:37 ?        00:00:00 /usr/lib/systemd/systemd-timesyncd
message+     146       1  0 20:37 ?        00:00:00 @dbus-daemon --system --address=systemd: --nofork --nopidfile --systemd-activation --syslog-only
root         153       1  0 20:37 ?        00:00:00 /usr/lib/systemd/systemd-logind
jaoneol      322       1  0 20:37 ?        00:00:00 /usr/lib/systemd/systemd --user
jaoneol@DESKTOP-B7GM3C5:~$
```

## *Install Windows Terminal*

Microsoft recommends installing Windows Terminal. The installation manual provides various methods, but we will proceed using the CLI([Installing and running Windows Terminal](https://github.com/microsoft/terminal#other-install-methods)).

<!--
MS사에서는 Windows Terminal 설치를 권장하고 있다. 설치메뉴얼에는 다앙한 방법이 있는데, CLI 통해 진행하겠다.
-->

```bash
winget install --id Microsoft.WindowsTerminal -e
```

![Windows Terminal Installation](/assets/img/docker/2024-10-07-Docker-Setup1_4.png)
_Windows Terminal Installation_

## *Use MobaXterm(Recommended)*

Microsoft recommended installing Windows Terminal, but I plan to use **MobaXterm**, a highly useful tool for working in a Linux environment. The connection is simple.

<!--
MS사에서는 Windows Terminal 설치를 권장했지만, Linux 환경을 사용할 때 매우 유용한 Tools인 MobaXterm으로 활용하고자 한다.접속은 간단하다. 
-->

![Connection method](/assets/img/docker/2024-10-07-Docker-Setup1_6.png)
_Connection method_

## *Use VSCode(Recommended)*

Most developers are probably using **VSCode** for development. I'm no exception. So, I will share how to connect to WSL using VSCode for development.    
1. Install the `WSL` extension in VSCode.
2. In PowerShell, navigate to the desired directory and run `code .`. This will automatically launch VSCode.

<!--
아마도 대부분의 개발자들이 VScode로 개발을 많이 하고 있을 것이다. 나도 마찬가지다. 그래서 VSCode로 WSL 접속하여 개발하는 내용을 공유한다.
1. VSCode에 Extension에서 WSL을 설치한다.
2. PowerShell에서 원하는 디렉토리로 이동후 'Code .' 을 명령하면 자동으로 VSCode가 실행된다.
-->

![Connection method](/assets/img/docker/2024-10-07-Docker-Setup1_8.png)
_Connection method_

## *Remove the Ubuntu Installed on WSL*

To remove Ubuntu installed on WSL, simply enter the following command, and it will be easily removed. Additionally, you can search for "Ubuntu" in **Windows > Settings > Apps**, where the app will be listed—make sure to uninstall it from there as well.

A very important point to note is that if you remove the Ubuntu app, recovery will be impossible. The virtual disk file, **ext4.vhdx**, will be completely deleted, so be sure to back it up before proceeding.(In the next post, I will explain Import & Export.)

`wsl --unregister Ubuntu`        

<!--
WSL에서 설치된  Ubuntu를 제거하려면, 아래 명령만 입력하면 쉽게 제거가 됩니다. 그리고 'Windows - 설정 - 앱'에서 Ubuntu를 검색하면 앱이 조회되는데 이 앱을 제거한다.
매우 주의할 점은 앱에서 Ubuntu 앱을 제거하면 복구 자체가 불가능하다는 점입니다. 가상 디스크 파일인 ext4.vhdx 이 바로 완전히 삭제되므로 반드시 백업해주시고 진행바랍니다.(다음 글에서 Import & Export 설명할께요.)
-->

```bash
# Check the list of installed distributions.
PS C:\Windows\system32> wsl -l -v
  NAME      STATE           VERSION
* Ubuntu    Running         2
PS C:\Windows\system32> wsl --shutdown
PS C:\Windows\system32> wsl -l -v
  NAME      STATE           VERSION
* Ubuntu    Stopped         2
# Very Important, Run the following command to remove the distribution.
# Be extremely careful!, If you run this command, the `ext4.vhdx` file will also be deleted!!!
PS C:\Windows\system32> wsl --unregister Ubuntu
등록 취소 중입니다.
작업을 완료했습니다.
PS C:\Windows\system32> wsl --shutdown
PS C:\Windows\system32> wsl -l -v
Linux용 Windows 하위 시스템에는 설치된 배포판이 없습니다.

'wsl.exe --list --online'을 사용하여 사용 가능한 배포판을 나열하고
 'wsl.exe --install <Distro>'를 사용하여 설치하세요.

배포판은 Microsoft Store
(https://aka.ms/wslstore)를 방문하여 설치할 수도 있습니다.
오류 코드: Wsl/WSL_E_DEFAULT_DISTRO_NOT_FOUND
PS C:\Windows\system32>
```