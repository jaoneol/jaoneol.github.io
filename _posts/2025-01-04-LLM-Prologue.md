---
title: LLM Prologue
description: Let’s Use RAG Like This
author: DS2Man
date: 2025-01-04 11:00:00 +0000
categories:
  - Journey into Generative AI
tags:
  - LLM
  - RAG
math: true
pin: true
---

Hello, my name is Yongcheol Jang, a engineer/researcher at the Semiconductor Division of Samsung Electronics.
Since the public release of ChatGPT on November 30, 2022, large language models (LLMs) have driven revolutionary advancements in artificial intelligence, gaining attention across various industries.
LLMs are models trained on vast amounts of text data, enabling them to understand and generate human-like language. These models are widely applied in tasks such as natural language processing, translation, and text summarization.
In 2023, we witnessed the development of various LLMs, including OpenAI’s GPT-4, Google’s BERT, and Meta’s LLaMA. Each of these models has continued to evolve, leveraging unique features and strengths. Notably, Meta’s LLaMA series offers versions with 7 billion, 13 billion, and 70 billion parameters, showcasing exceptional performance in diverse natural language processing tasks.
The advancements in LLMs have brought about transformative changes in areas such as customer service, content creation, and education.
Through this tutorial, I aim to share the knowledge and insights I have gained. I will cover topics such as using publicly available models on Hugging Face, performing LLM inference with Ollama, applying various techniques with LangChain, and exploring AI agents and LangGraph. Please stay tuned and join me on this journey!

<!-- 안녕하세요, 삼성전자 반도체사업부에서 근무하는 장용철 연구원입니다.
2022년 11월 30일 ChatGPT의 공개 이후, 대규모 언어 모델(Large Language Model, LLM)은 인공지능 분야에서 혁신적인 발전을 이끌며 다양한 산업에서 주목받고 있습니다. 
LLM은 방대한 텍스트 데이터를 학습하여 인간과 유사한 언어 이해 및 생성 능력을 갖춘 모델로, 자연어 처리, 번역, 텍스트 요약 등 여러 작업에서 활용되고 있습니다. 
2023년에는 OpenAI의 GPT-4, Google's BERT, Meta의 LLaMA 등 다양한 LLM이 개발되어 각자의 특성과 강점을 바탕으로 발전해왔습니다. 
특히, Meta의 LLaMA 시리즈는 7억, 130억, 700억 파라미터 버전으로 제공되며, 다양한 자연어 처리 작업에서 뛰어난 성능을 보여주고 있습니다. 
이러한 LLM의 발전은 고객 서비스, 콘텐츠 생성, 교육 등 다양한 분야에서 혁신적인 변화를 가져오고 있습니다.
이번 Tutorial통해서 제가 익히고 필요한 내용들을 공유하고자 합니다.
Huggingface에 공개된 모델 사용법, Ollama를 활용한 LLM 추론, LangChain을 활용한 다양한 기법, AI Agent, LangGraph 등을 공유할 예정이니, 관심있게 지켜봐 주세요. -->

## Recommended For:

This tutorial is perfect for those who relate to any of the following scenarios ~~(and, to be honest, all of these apply to me!)~~:

- Your company has restricted the use of ChatGPT due to security concerns.

- You want to run an LLM on your own computer but don’t know how to get started.

- You feel that ChatGPT gives irrelevant or nonsensical answers to slightly more technical questions, and you’re wondering if this can be improved.

- You want to build a dataset tailored to your domain and create your own version of ChatGPT.

- You’d like to share a ChatGPT model infused with your domain knowledge with others.

<!-- 이런 분들께 추천합니다!
아래 내용에 해당되는 분들께서 본Tutorial을 보시면 좋을 것 같아요. (아래는 사실 전부 제 이야기입니다…)

회사에서 보안상 문제로 Chat GPT 사용에 제약을 걸었다.
LLM을 내 컴퓨터로 돌려보고 싶은데 어떻게 하는지 모르겠다.
조금만 전문적인 내용을 물어봐도 헛소리를 하는 것 같은데, 개선할 수 없을까?
나의 도메인에 해당되는 데이터셋을 구축해서 나만의 Chat GPT를 갖고 싶다.
나의 도메인 지식이 담긴 Chat GPT를 다른 사람에게 공유하고 싶다. -->