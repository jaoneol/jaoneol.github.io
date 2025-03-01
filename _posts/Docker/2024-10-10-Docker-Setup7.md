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

- 1.  It's very simple. Just run the command below, and you're done!   
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
For reference, since Deepseek became a hot topic after the Ollama post, I updated this article on February 20. The list of LLMs is as follows. You can check the full list on [Ollama GitHub](https://github.com/ollama/ollama).

<!--
[Ollama Home](https://ollama.com/search) 나 [Ollama Github](https://github.com/ollama/ollama) 에서 내가 활용하고 싶은 LLM 모델을 `ollama pull` 명령어로 다운받으면 된다. `ollama list`로 현재 다운받은 내용을 확인할 수 있다.

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
```

```bash
# I will try downloading Llama 3.1.
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