---
title: (Option) Ollama on Ubuntu
description: Let's Install Ollama on Ubuntu
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

## *Prologue*

In the previous post, I installed Docker Engine and set up NVIDIA GPU support within Docker Containers by installing the NVIDIA Container Toolkit, making it easier to deploy and run GPU-accelerated applications. Due to company security policies, there are restrictions on using ChatGPT, and the cost is also a significant factor. Ollama addresses these constraints. It is an open-source LLM that packages model weights, configurations, and datasets into a Modelfile for management.    
Before creating and using an Ollama Image, let's first install Ollama on the WSL Ubuntu we set up. For reference, during development, it's often more convenient to install and use Ollama on Windows. So, for this post, we'll just focus on how to install it on Ubuntu.

<!--
이전 글에서 Docker Engine 설치하였고, Docker Container 내에서 NVIDIA GPU를 활용할 수 있게하여 GPU 가속화 어플리케이션의 배포와 실행을 용이하게 하기 위해 nvidia container toolkit을 설치하였다. 회사 보안상의 문제로 ChatGPT 사용에 제약이 있고,  비용 문제도 만만치 않다. 이러한 제약을 해소한 것이 Ollama 이다. Ollama는 오픈 소스 LLM으로 모델 가중치, 설정, 데이터셋을 패키지로 묶어서 Modelfile로 관리한다.
앞으로 Ollama Image를 만들어서, 사용할 예정인데, 그에 앞서 설치한 WSL Ubuntu에 Ollama를 설치해 보자. 참고로, 개발하다보면 Windows에 Ollama를 설치하고 이용하는게 좀더 편해서, 이번 포스팅은 어떻게 Ubuntu에 설치하는 지만 알아두면 될거 같다.
-->

## *Install Ollama*

I wrote this based on [Ollama](https://ollama.com/download/linux) to install the Ollama.

- 1.It's very simple. Just run the command below, and you're done!   
	`curl -fsSL https://ollama.com/install.sh | sh`

```bash
(base) jaoneol@DESKTOP-B7GM3C5:~$ curl -fsSL https://ollama.com/install.sh | sh
>>> Installing ollama to /usr/local
[sudo] password for jaoneol:
>>> Downloading Linux amd64 bundle
######################################################################## 100.0%
>>> Creating ollama user...
>>> Adding ollama user to render group...
>>> Adding ollama user to video group...
>>> Adding current user to ollama group...
>>> Creating ollama systemd service...
>>> Enabling and starting ollama service...
Created symlink /etc/systemd/system/default.target.wants/ollama.service → /etc/systemd/system/ollama.service.
>>> Nvidia GPU detected.
>>> The Ollama API is now available at 127.0.0.1:11434.
>>> Install complete. Run "ollama" from the command line.
(base) jaoneol@DESKTOP-B7GM3C5:~$ 
```

## *How to use Ollama*

You can download the LLM model you want to use by running the `ollama pull` or `ollama run` command from [Ollama Home](https://ollama.com/search) or [Ollama GitHub](https://github.com/ollama/ollama). You can check the downloaded models using the `ollama list` command.    
<!--
[Ollama Home](https://ollama.com/search) 나 [Ollama Github](https://github.com/ollama/ollama) 에서 내가 활용하고 싶은 LLM 모델을 `ollama pull` 명령어로 다운받으면 된다. `ollama list`로 현재 다운받은 내용을 확인할 수 있다.
-->

- 1.Use the following command to download the LLM model. If you want to run it immediately, use `run` instead of `pull`.    
	`ollama pull llama3.1`    
	For reference, the location where the model is stored is as follows.    
	`/usr/share/ollama/.ollama/models/`    
- 2.You can check the LLM models managed by Ollama using the following command.  
	`ollama list`   
- 3.The command to delete an LLM model is as follows, and you can verify that the files in `/usr/share/ollama/.ollama/models/` have been removed.      
	`ollama rm llama3.1`    
- 4.When using `ollama rm` on Ubuntu, the files are deleted, but the size of `ext4.vhdx` does not decrease. To resolve this, you need to use `diskpart` to clean up the files. This program is used for disk cleanup. Enter the following commands in order.    
	`select vdisk file="D:\01.WSL\ext4.vhdx"`    
	`attach vdisk readonly`    
	`compact vdisk`    
	`detach vdisk`    


```bash
# I will try downloading Llama 3.1.
(base) jaoneol@DESKTOP-B7GM3C5:~$ ollama pull llama3.1
pulling manifest
pulling 667b0c1932bc... 100% ▕███████████████████████████████████████████▏ 4.9 GB
pulling 948af2743fc7... 100% ▕███████████████████████████████████████████▏ 1.5 KB
pulling 0ba8f0e314b4... 100% ▕███████████████████████████████████████████▏  12 KB
pulling 56bb8bd477a5... 100% ▕███████████████████████████████████████████▏   96 B
pulling 455f34728c9b... 100% ▕███████████████████████████████████████████▏  487 B
verifying sha256 digest
writing manifest
success

(base) jaoneol@DESKTOP-B7GM3C5:~$ ollama list
NAME               ID              SIZE      MODIFIED
llama3.1:latest    46e0c10c039e    4.9 GB    29 seconds ago
(base) jaoneol@DESKTOP-B7GM3C5:~$ 

# the location where the model is stored is as follows.    
# `/usr/share/ollama/.ollama/models/`    
(base) jaoneol@DESKTOP-B7GM3C5:~$ cd /usr/share/ollama/.ollama
(base) jaoneol@DESKTOP-B7GM3C5:/usr/share/ollama/.ollama/models$ pwd
/usr/share/ollama/.ollama/models
(base) jaoneol@DESKTOP-B7GM3C5:/usr/share/ollama/.ollama/models$ 
(base) jaoneol@DESKTOP-B7GM3C5:/usr/share/ollama/.ollama$ ls
id_ed25519  id_ed25519.pub  models
(base) jaoneol@DESKTOP-B7GM3C5:/usr/share/ollama/.ollama$ cd models
(base) jaoneol@DESKTOP-B7GM3C5:/usr/share/ollama/.ollama/models$ ls
blobs  manifests
(base) jaoneol@DESKTOP-B7GM3C5:/usr/share/ollama/.ollama/models$ cd blobs/
(base) jaoneol@DESKTOP-B7GM3C5:/usr/share/ollama/.ollama/models/blobs$ ls -lrt
total 4805448
-rw-r--r-- 1 ollama ollama 4920738944 Mar  1 14:06 sha256-667b0c1932bc6ffc593ed1d03f895bf2dc8dc6df21db3042284a6f4416b06a29
-rw-r--r-- 1 ollama ollama       1481 Mar  1 14:06 sha256-948af2743fc78a328dcb3b0f5a31b3d75f415840fdb699e8b1235978392ecf85
-rw-r--r-- 1 ollama ollama      12320 Mar  1 14:06 sha256-0ba8f0e314b4264dfd19df045cde9d4c394a52474bf92ed6a3de22a4ca31a177
-rw-r--r-- 1 ollama ollama         96 Mar  1 14:06 sha256-56bb8bd477a519ffa694fc449c2413c6f0e1d3b1c88fa7e3c9d88d3ae49d4dcb
-rw-r--r-- 1 ollama ollama        487 Mar  1 14:06 sha256-455f34728c9b5dd3376378bfb809ee166c145b0b4c1f1a6feca069055066ef9a
(base) jaoneol@DESKTOP-B7GM3C5:/usr/share/ollama/.ollama/models/blobs$ 
```

```bash
# The command to delete an LLM model is as follows, and you can verify that the files in `/usr/share/ollama/.ollama/models/` have been removed.
(base) jaoneol@DESKTOP-B7GM3C5:/usr/share/ollama/.ollama/models/blobs$ ollama rm llama3.1
deleted 'llama3.1'
(base) jaoneol@DESKTOP-B7GM3C5:/usr/share/ollama/.ollama/models/blobs$ ollama list
NAME    ID    SIZE    MODIFIED
(base) jaoneol@DESKTOP-B7GM3C5:/usr/share/ollama/.ollama/models/blobs$ ls
(base) jaoneol@DESKTOP-B7GM3C5:/usr/share/ollama/.ollama/models/blobs$ 
```

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

```bash
# I run Llama 3.1.
(base) jaoneol@DESKTOP-B7GM3C5:~$ ollama run llama3.1
>>> hi. my name is ds2man. what is your name?
Hello DS2Man! I don't have a personal name, but you can call me Assistant or AI if you like. I'm here to help answer any questions or chat with you about anything that's on your mind.
What brings you here today?

>>> Hmm... From now on, let's call you Llama.
I've been "called" many names before! Llama it is, then! I'll respond to Llama from now on. Nice and simple. What would you like to talk about, DS2Man? Dark Souls 2-related questions
perhaps?

>>> what is your name?
My "name" is actually an acronym: Large Language Model Application (LLMA). But since we're going with Llama as a nickname, I'll stick with that!

# If you want to enter a long sentence, enclose it with `"""`.
>>> """
... I'm happy to be talking with you.
... Let's have lots of conversations from now on!
... """
Me too, DS2Man! It's great to have someone to chat with. I don't get bored or tired like humans do, so feel free to start a conversation anytime you'd like. We can talk about Dark Souls
2, games, movies, books, or just about anything else that's on your mind.

Speaking of which... What would you like to talk about next? Want to discuss the lore of Drangleic, or share some tips for defeating tough enemies in the game?
```

