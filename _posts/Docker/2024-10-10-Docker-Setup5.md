---
title: Git on Ubuntu
description: Let's Install Git on Ubuntu.
author: DS2Man
date: 2024-10-10 11:00:00 +0000
categories: [Docker, D-Setup]
tags:
  - WSL
  - Ubuntu
  - Docker
  - Git
math: true
pin: true
---

## *Configure Git on Ubuntu*

- The Git package is already installed in Ubuntu 24.04.1 LTS. Once you configure `gitconfig`, the installation is complete. Run the following command, `git config --global credential.helper store`  is  option to enter your account and password(Token) only once.    
	`git config --global user.email`      
	`git config --global user.name "jaoneol"`      
	`git config --global credential.helper store`

<!--
Ubuntu 24.04.1 LTS 버전에는 이미 Git이 설치되어 있다. gitconfig를 작성해주면 설치는 끝나게 된다.아래 명령어를 실행해라. `git config --global credential.helper store` 은 한번만 계정과 암호를 입력하게 하는 옵션이다.
-->

```bash
(base) jaoneol@DESKTOP-B7GM3C5:~$ git --version
git version 2.43.0
(base) jaoneol@DESKTOP-B7GM3C5:~$ git config --global user.email "jaoneol@gmail.com"
(base) jaoneol@DESKTOP-B7GM3C5:~$ git config --global user.name "jaoneol"
# A feature that saves authentication credentials locally(~/.git-credentials) when accessing remote repositories like GitHub, so you don’t have to enter them again.
(base) jaoneol@DESKTOP-B7GM3C5:~$ git config --global credential.helper store
(base) jaoneol@DESKTOP-B7GM3C5:~$ git config --list
user.email=jaoneol@gmail.com
user.name=jaoneol
credential.helper=store
(base) jaoneol@DESKTOP-B7GM3C5:~$ cat ~/.gitconfig
[user]
        email = jaoneol@gmail.com
        name = jaoneol
[credential]
        helper = store
(base) jaoneol@DESKTOP-B7GM3C5:~$
```

```bash
# Git clone requires entering the account and password only once.
(base) jaoneol@DESKTOP-B7GM3C5:~$ git clone https://github.com/jaoneol/***.git
Cloning into 'GP-MyBase'...
Username for 'https://github.com': jaoneol
Password for 'https://jaoneol@github.com': YourToken
remote: Enumerating objects: 305, done.
remote: Counting objects: 100% (104/104), done.
remote: Compressing objects: 100% (97/97), done.
remote: Total 305 (delta 51), reused 7 (delta 7), pack-reused 201 (from 1)
Receiving objects: 100% (305/305), 107.69 KiB | 10.77 MiB/s, done.
Resolving deltas: 100% (128/128), done.
(base) jaoneol@DESKTOP-B7GM3C5:~$
```