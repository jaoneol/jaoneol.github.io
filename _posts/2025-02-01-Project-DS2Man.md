---
title: MySQL Installation and Configuration
description: Let's install MySQL which are necessary for the LLM service(DS2Man).
author: DS2Man
date: 2025-02-01 17:00:00 +0000
categories:
  - Project-DS2Man
tags:
  - DS2Man
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
# sudo sh get-docker.sh
jaoneol@DESKTOP-B7GM3C5:~$ curl -sSL https://get.docker.com | sh
# Executing docker install script, commit: 4c94a56999e10efcf48c5b8e3f6afea464f9108e

WSL DETECTED: We recommend using Docker Desktop for Windows. 
Please get Docker Desktop from https://www.docker.com/products/docker-desktop

You may press Ctrl+C now to abort this script.
+ sleep 20
```

![docker engine Installation](/assets/img/2025-01-28-Docker2_1.png)
_docker engine Installation_
