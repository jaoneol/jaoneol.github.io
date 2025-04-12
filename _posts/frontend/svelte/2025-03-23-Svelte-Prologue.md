---
title: Svelte Prologue
description: Let’s Use Svelte Like This
author: DS2Man
date: 2025-03-24 11:00:00 +0000
categories: [Frontend, Svelte-Prologue]
tags: [Frontend]
math: true
pin: true
---
The reason I started this blog is because of LLMs, a form of generative AI. I believe that the manufacturing industry will evolve beyond simple automation toward autonomous manufacturing, and LLMs will be the most crucial technology driving this change.

To integrate LLMs into semiconductor manufacturing, models like ChatGPT, Claude, and Grok are essential. However, before deploying such models, it is critical to have a user interface (UI) that enables them to be used as actual services. No matter how powerful a model may be, if it isn’t easily accessible and usable on the shop floor, real-world adoption becomes difficult.

In late 2024, Jensen Huang introduced the concept of "Physics AI." But I believe that before we get there, we first need to establish an interface where humans and LLMs can communicate naturally.

While I was exploring these ideas, I came across an open-source project called [OpenWebUI](https://openwebui.com/). After trying it out, I found that it already included most of the features I had envisioned. It inspired me to consider building a service of my own based on it.

The only problem was that I had little to no experience in frontend development. So, starting on March 24, I began diving into the related technologies. I learned that OpenWebUI is built with Svelte, Vite, TypeScript, and Tailwind CSS. Among these four, I decided to first explore the frontend framework Svelte (including Vite), which forms the core of the UI layer.

<!-- 
내가 블로그를 시작하게 된 계기는 LLM이라는 생성형 AI 때문이다. 앞으로의 제조 산업 생태계는 단순한 자동화를 넘어 자율제조로 나아갈 것이고, 이때 가장 핵심적인 기술이 바로 LLM이라고 생각한다. 반도체 제조에 LLM을 도입하기 위해서는 ChatGPT, Claude, Grok과 같은 모델들이 필요하지만, 그 이전에 이런 모델들을 실제로 서비스하기 위한 UI가 필수적이다. 아무리 뛰어난 모델이 있어도, 현장에서 쉽게 접근하고 사용할 수 없다면 실질적인 도입은 어렵다. 2024년 말, 젠슨 황은 ‘Physics AI’라는 개념을 제시했는데, 나는 그 이전 단계에서 사람과 LLM이 자연스럽게 소통할 수 있는 인터페이스가 선행되어야 한다고 생각한다. 

그렇게 고민하던 중 오픈소스로 공개된 [OpenWebUI](https://openwebui.com/)를 알게 되었고, 실제로 사용해보니 내가 구상하던 기능들이 대부분 들어 있었다. 이걸 바탕으로 직접 서비스를 만들어보면 좋겠다는 생각이 들었다. 하지만 문제는 나에게 프론트엔드 개발 경험이 거의 없다는 점이었다. 3월 24일부터 관련 내용을 본격적으로 파악하기 시작했고, OpenWebUI가 Svelte, Vite, TypeScript, 그리고 Tailwind CSS로 구성되어 있다는 것을 알게 되었다. 4가지 항목 중에서 프론트엔드 프레임워크인 Svelte(Vite 포함)에 관련된 내용부터 파악해 보고자 한다. 
-->




