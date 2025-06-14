---
title: 통계학의 이해
description: Statistics
author: DS2Man
date: 2125-06-01 11:00:00 +0000
categories:
  - Statistics
tags:
  - 통계학
  - 추론 개론
math: true
pin: true
---

## 통계학의 이해

통계학은 많은 양의 수치 자료를 수집하고, 정리, 요약 및 해석하는 방법을 다루는 학문이라고 본다. 관측 자료를 바탕으로 추론(inference)을 하는 과학의 한 분야로서 **불확실성(uncertainty)** 하에서 보다 합리적인 의사 결정 하는 방법을 제시해 주는 학문이다.

## 통계에서 회귀분석까지의 주요 용어

통계에는 수많은 용어가 존재한다. 의미를 잘 이해해야하고, 어떤 단계로 배워야할 지 막막하다. 내 생각을 기반으로 회귀분석까지의 용어를 배우는 과정을 정리해 보았다.
![Statics](/assets/img/statistics/2024-05-01-stat-prologue_1.png){: width="972" height="589" }
_통계 커리큘럼_

## 통계적 추론

통계적 추론 과정을 통해 모집단의 특성인 모수를 표본의 통계량으로 맞추고자 하는 것이다.
크게 두 분류로 나눠볼 수 있는데, **추정(Estimation, 구간 추정)**과 **가설 검정(Test of Hypotheses, 귀무/대립 가설)**이다.

정확한 통계치를 얻기위해서는 모집단 전체 데이터를 활용하여 통계값을 계산하는 게 정석이다. 그러나 현실적인 한계(시간, 비용 등)으로 우리는 표본을 추출하여 모집단의 모수를 추론한다. 표본은 모집단의 일부이므로 **모집단과 표본 사이 값에 오차는 분명 존재**하게 된다. **표본으로 모수를 맞출때 오차의 크기를 줄이는 것이 통계적 추론의 목표**이다. 
![Statics](/assets/img/statistics/2024-05-01-stat-prologue_2.png){: width="972" height="589" }
_통계적 추론 과정_
