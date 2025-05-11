---
title: Depends in FastAPI
description: Let's learn Depends in FastAPI.
author: DS2Man
date: 2125-04-13 11:00:00 +0000
categories: [Backend, FastAPI]
tags: [Backend, FastAPI]
math: true
pin: true
---

## *Why Use Depends?*

`Depends` is FastAPI’s way of declaring dependencies. It allows you to define **shared logic** -such as database sessions, authentication, or reusable components - and inject that logic into multiple endpoints.

