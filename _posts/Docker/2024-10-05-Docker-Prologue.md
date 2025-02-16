---
title: Docker Prologue
description: Let’s Use Docker
author: DS2Man
date: 2024-10-05 11:00:00 +0000
categories: [Docker, Setup]
tags:
  - WSL
  - Ubuntu
  - Docker
math: true
pin: true
---

Since I started working in software at my company, we have been operating in a **Traditional Deployment** model, where applications are installed and managed on a single OS running on a Bare Metal Server. However, starting in 2023, our department suddenly introduced **Kubernetes (K8s)**, making it essential to understand **Docker and K8s** in order to perform daily tasks in a **Container Deployment** environment.    
Apart from **K8s**, other orchestration platforms include **Amazon ECS** and **Docker Swarm**, but K8s seems to dominate the market, accounting for approximately 70% of searches. Kubernetes was officially released in 2015 and began gaining traction in Azure and AWS around 2018. Given that timeline, it seems that our department has adopted K8s approximately five years later than major cloud providers.    
Through this tutorial, I aim to share the knowledge and insights I have gained while learning Docker and Kubernetes.   
To begin, before diving into K8s, I will first **set up a Docker environment, create Docker images, and run containers**. Stay tuned and follow along!

<!-- 
회사에서 S/W 업무를 시작하면서 Bare Metal Server의 하나의 OS에 Applications을 설치해서 운영하는 형태로 사용하여 왔다(Traditional Deployment). 2023년부터 부서에 K8s(Kubernetes)라는 orchestration platform이 갑자기 도입되면서 K8s와 더불어 Docker를 이해해야 업무를 수행할 수 있게 되었다(Container Deployment).
orchestration platform 의 종류는 k8s이외에 Amazon ECS, Docker Swarm 등이 있는데, 압도적인 점유율을 차지하고 있는 것 같다(검색 비율 등을 보면 약 70%?). K8s는 2015년 공식 출시 이후에, Azure, AWS에서는 2018년부터 본격적으로 사용되기 시작했으니, 우리 부서에는 약 5년 정도 늦게 도입된 거 같다.
이번 Tutorial통해서 제가 익히고 필요한 내용들을 공유하고자 합니다.
우선은 K8s에 대한 이해에 앞서, Docker사용을 위한 환경 구축해보고, Docker Image 및 Container 생성해 보겠다. 관심있게 지켜봐 주세요.
-->

|**Platform**|**Market Share<br>(Estimated)**|**Description**|
|---|---|---|
|**Kubernetes**|**~70%**|The dominant open-source container orchestration platform, widely adopted across various cloud environments.|
|Amazon ECS|~10%|AWS’s container orchestration service, tightly integrated with AWS infrastructure.|
|Docker Swarm|~5%|A simple orchestration tool from Docker, easy to set up but limited in scalability and features compared to Kubernetes.|
|Apache Mesos|~5%|A large-scale cluster management platform supporting various workloads but declining in adoption due to complexity.|
|HashiCorp Nomad|~5%|A lightweight orchestration tool known for simplicity and fast deployment, but with a smaller ecosystem compared to Kubernetes.|
|Others|~5%|Includes OpenShift, Rancher, and other platforms used in specific industries or enterprises.|

![Historical context for Kubernetes](/assets/img/2024-10-05-Docker-Prologue1_1.png)
_Historical context for Kubernetes(Source: [Kubernetes](https://kubernetes.io/docs/concepts/overview/))_

## *Recommended For:*

This tutorial is perfect for those who relate to any of the following scenarios ~~(and, to be honest, all of these apply to me!)~~:

- I need to set up a small-scale Kubernetes (K8s) cluster within my department instead of using the company’s existing K8s infrastructure and operate my own services.  
- I want to build Docker images on my computer and deploy them.  
- I also need to utilize a GPU for machine learning (ML) and deep learning (DL).
- I want to learn K8s.

<!-- 
이런 분들께 추천합니다!
아래 내용에 해당되는 분들께서 본Tutorial을 보시면 좋을 것 같아요. (아래는 사실 전부 제 이야기입니다…)

회사에서 운영되는 K8s을 사용하지 않고 부서에서 소규모 단위로 운영할 K8s를 구축하고 나만의 서비스가 필요하다.
Docker를 내 컴퓨터로 빌드하고 싶고, 이미지를 배포하고 싶다.
GPU를 활용하여 ML/DL을 해야한다.
K8s를 배우고 싶다.
-->