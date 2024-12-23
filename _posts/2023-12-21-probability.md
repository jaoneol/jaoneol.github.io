---
title: "확률 기초"
description: Statistics
author: Devin
date: 2023-12-21 06:00:00 +0000
categories: [Statistics]
tags: [확률, 조건부 확률, 베이즈 정리]
---

## 확률의 이해

- 표본 공간(𝑺): 모든 관찰 가능한 결과의 집합
- 사건 : 표본 공간(𝑺)의 임의의 부분 집합
- 확률: 어떤 사건이 발생할 가능성을 0 ~ 1 사이의 실수로 표현
          세가지 공리를 만족해야 함

- 확률의 공리
  1. 표본공간(𝑺) 내 임의의 사건 𝑨의 확률은 언제나 0과 1사이의 값이다
      𝟎≤𝑷(𝑨)≤𝟏
  2. 표본공간의 확률은 1이 된다
      𝑷(𝑺)=𝟏
  3. 동시에 발생하지 않는 배반 사건들의 합사건 확률은 각 사건의 확률의 합과 언제나 같다
      𝑨𝒊∩𝑨𝒋=∅ 이면, 𝑷(𝑨𝒊∪𝑨𝒋∪…)=𝑷(𝑨𝟏)+𝑷(𝑨𝟐)+…


## 조건부 확률

- 다른 사건의 결과를 전제로 했을 때 관심 사건의 확률
  즉, 𝑩사건 영역(사전 확률)에서 𝑨사건이 발생할 확률 

