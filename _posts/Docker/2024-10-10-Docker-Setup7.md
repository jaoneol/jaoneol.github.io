---
title: Ollama on Ubuntu
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

## *About Ollama*

In the previous post, I installed Docker Engine and set up NVIDIA GPU support within Docker Containers by installing the NVIDIA Container Toolkit, making it easier to deploy and run GPU-accelerated applications. Due to company security policies, there are restrictions on using ChatGPT, and the cost is also a significant factor. Ollama addresses these constraints. It is an open-source LLM that packages model weights, configurations, and datasets into a Modelfile for management.    
Moving forward, I plan to use Ollama to build a local LLM. To deploy it via K8s, it must be built as an image. Therefore, I will install Ollama on the Ubuntu system we have set up.

<!--
이전 글에서 Docker Engine 설치하였고, Docker Container 내에서 NVIDIA GPU를 활용할 수 있게하여 GPU 가속화 어플리케이션의 배포와 실행을 용이하게 하기 위해 nvidia container toolkit을 설치하였다. 회사 보안상의 문제로 ChatGPT 사용에 제약이 있고,  비용 문제도 만만치 않다. 이러한 제약을 해소한 것이 Ollama 이다. Ollama는 오픈 소스 LLM으로 모델 가중치, 설정, 데이터셋을 패키지로 묶어서 Modelfile로 관리한다.
앞으로 로컬 LLM 구축을 위해서 Ollama를 사용할 예정인데, K8s통한 배포를 위해서는 반드시 Image로 만들어야 한다. 이를 위해서 우리가 설치한 Unbutu에 ollama를 설치하고자 한다.
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

For reference, since Deepseek became a hot topic after the Ollama post, I updated this article on February 20. The list of LLMs is as follows. You can check the full list on [Ollama GitHub](https://github.com/ollama/ollama).

<!--
참고로 Ollama 포스트 이후에 Deepseek가 핫이슈라 이글 업데이트를 2/20일에 진행했다. LLM 리스트는 다음과 같다. 전체 리스트는 [Ollama Github](https://github.com/ollama/ollama) 확인하면 된다.
-->

|Model|Parameters|Size|Download|
|---|---|---|---|
|**DeepSeek-R1**|7B|4.7GB|`ollama run deepseek-r1`|
|DeepSeek-R1|671B|404GB|`ollama run deepseek-r1:671b`|
|Llama 3.3|70B|43GB|`ollama run llama3.3`|
|Llama 3.2|3B|2.0GB|`ollama run llama3.2`|
|Llama 3.2|1B|1.3GB|`ollama run llama3.2:1b`|
|Llama 3.2 Vision|11B|7.9GB|`ollama run llama3.2-vision`|
|Llama 3.2 Vision|90B|55GB|`ollama run llama3.2-vision:90b`|
|**Llama 3.1**|8B|4.7GB|`ollama run llama3.1`|
|Llama 3.1|405B|231GB|`ollama run llama3.1:405b`|
	
```bash
# I will try downloading Llama 3.1.
(base) jaoneol@DESKTOP-B7GM3C5:~$ ollama pull llama3.1
pulling manifest
pulling 667b0c1932bc... 100% ▕█████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████▏ 4.9 GB
pulling 948af2743fc7... 100% ▕█████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████▏ 1.5 KB
pulling 0ba8f0e314b4... 100% ▕█████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████▏  12 KB
pulling 56bb8bd477a5... 100% ▕█████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████▏   96 B
pulling 455f34728c9b... 100% ▕█████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████▏  487 B
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

## *Testing Deepseek-r1*

Recently, due to **DeepSeek-R1**, the stock prices of American AI companies have dropped significantly. This is because it offers **high performance at a low cost**.    
DeepSeek-R1 is a **fully open-source LLM** licensed by **MIT (Massachusetts Institute of Technology)**. It provides performance similar to the **OpenAI-O1 large language model** but at a much **lower cost**.    
I tried using it—nothing more, nothing less.  
Perhaps starting around **May 2025**, I might begin reading research papers on LLM models.

<!--
최근 DeepSeek-R1 으로인해, 미국 인공지능 회사들의 주가가 크게 떨어졌다. 저렴한 가격으로 높은 성능의 모델을 만들었기 때문이다.
DeepSeek R1은 MIT(매사추세츠 공과대학)에서 라이선스를 취득한 완전 오픈 소스 LLM입니다. OpenAI-o1 대규모 언어 모델과 비슷한 성능을 제공하지만 훨씬 더 저렴한 LLM이다. 
한번 사용해 보았다. 그 이상도 이하도 아니다. 아마 25년 5월 즈음부터 LLM 모델에 대한 논문을 읽어보게 되지 않을까 싶다. 
-->

```bash
(base) jaoneol@DESKTOP-B7GM3C5:~$ ollama pull deepseek-r1
pulling manifest
pulling 96c415656d37... 100% ▕█████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████▏ 4.7 GB
pulling 369ca498f347... 100% ▕█████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████▏  387 B
pulling 6e4c38e1172f... 100% ▕█████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████▏ 1.1 KB
pulling f4d24e9138dd... 100% ▕█████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████▏  148 B
pulling 40fb844194b2... 100% ▕█████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████▏  487 B
verifying sha256 digest
writing manifest
success
(base) jaoneol@DESKTOP-B7GM3C5:~$ ollama list
NAME                  ID              SIZE      MODIFIED
deepseek-r1:latest    0a8c26691023    4.7 GB    13 minutes ago
llama3.1:latest       46e0c10c039e    4.9 GB    30 minutes ago
(base) jaoneol@DESKTOP-B7GM3C5:~$ 
```

```bash
# I run Llama 3.1.
(base) jaoneol@DESKTOP-B7GM3C5:~$ ollama run deepseek-r1
>>> Recommend two places worth visiting in Shanghai, China.
<think>
Okay, so the user has asked for recommendations on two places to visit in Shanghai, China. First off, they've previously asked about the capital of Korea and wanted a response in English,
so now they're switching back to Shanghai.

I should consider why they're interested in Shanghai. Maybe they're planning a trip there soon or looking for ideas for a visit. They probably want well-known attractions that are popular
among tourists.

Shanghai is known for its blend of old and new architecture, rich history, and modernity. Therefore, recommending places that showcase both aspects would be ideal. Huangpu River comes to
mind because it's iconic with its bridges and the Bund district. Oriental Pearl TV Tower is another must-visit for its stunning views.

In terms of cultural experiences, Shanghai Disneyland fits well as a family-friendly attraction with unique rides. For food enthusiasts, Huohe Road is famous for its variety of dumpling
shops, which could be worth visiting if they're interested in local cuisine.

I should present these options clearly and briefly explain why each place is recommended without being too lengthy. Keeping the tone friendly and helpful will make the response effective.
</think>

Two highly recommended places to visit in Shanghai are:

1. **Huangpu River**
   This iconic riverfront offers stunning views of the city's landmarks, including the Oriental Pearl Tower and Pudian Riverside Promenade. It's also a great spot for dining and shopping.

2. **Oriental Pearl Tower (Shanghai Tower)**
   The tallest skyscraper in China, this modern architectural marvel provides panoramic views of the Huangpu River and the city skyline.

If you're looking for something more cultural and family-friendly, you might also enjoy exploring **Shanghai Disneyland**, which offers unique rides and attractions for all ages.

>>> Send a message (/? for help)
```