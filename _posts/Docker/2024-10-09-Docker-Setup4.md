---
title: MiniConda3 on Ubuntu
description: Let's Install MiniConda3 on Ubuntu.
author: DS2Man
date: 2024-10-09 11:00:00 +0000
categories: [Docker, Setup]
tags:
  - WSL
  - Ubuntu
  - Docker
  - MiniConda3
math: true
pin: true
---

Python virtual environment management tools such as `venv`, `virtualenv`, `conda`, `pipenv`, and `poetry` are widely used. Each tool has its own strengths and weaknesses, and the choice depends on the user's needs and the characteristics of the project. While it is difficult to determine exact statistics on the most commonly used tool, `conda` is popular in the data science field, whereas `poetry` is gaining traction for general Python projects. Since I work as a Data Scientist and AI Engineer, I plan to use `conda`.   
Miniconda is a software distribution that helps easily install and manage Python and related packages. It is a lightweight version of the Anaconda distribution, allowing users to install only the minimal necessary packages and add additional packages as needed later.   

<!--
파이썬 가상환경 관리 도구로는 `venv`, `virtualenv`, `conda`, `pipenv`, `poetry` 등이 널리 사용됩니다. 각 도구는 고유한 장단점을 지니며, 사용자의 필요와 프로젝트의 특성에 따라 선택됩니다.
현재 가장 많이 사용되는 도구에 대한 정확한 통계는 확인하기 어렵지만, 데이터 과학 분야에서는 `conda`가, 일반적인 파이썬 프로젝트에서는 `poetry`가 많이 사용되는 추세입니다. 나는 Data Scientist, AI Engineer로 활동하고 있어서 conda 로 진행하려 합니다.

미니콘다(Miniconda)는 파이썬과 관련 패키지를 쉽게 설치하고 관리할 수 있도록 도와주는 소프트웨어 배포판입니다. 아나콘다(Anaconda) 배포판의 경량 버전으로, 필요에 따라 최소한의 패키지만 설치하고 원하는 추가 패키지를 나중에 설치할 수 있습니다.
-->
## *Key Features of Miniconda*

1. **Lightweight Installation**: Miniconda includes only the essential packages, making installation fast and simple. By default, it comes with the Conda package manager and Python.   
2. **Flexible Package Management**: With Conda, users can easily install and manage various Python packages. It supports packages for diverse fields such as data science, machine learning, and web development.   
3. **Virtual Environment Management**: Conda allows users to create and manage multiple virtual environments, enabling independent development environments for different projects.   

<!--
### 미니콘다의 주요 특징

4. **경량 설치**: 미니콘다는 최소한의 필수 패키지만 포함하고 있어 설치가 빠르고 간편합니다. 기본적으로 Conda 패키지 관리자와 파이썬만 포함되어 있습니다.
5. **유연한 패키지 관리**: Conda를 통해 사용자는 다양한 파이썬 패키지를 손쉽게 설치하고 관리할 수 있습니다. 데이터 과학, 머신러닝, 웹 개발 등 다양한 분야의 패키지를 지원합니다.
6. **가상환경 관리**: Conda를 사용하면 여러 개의 가상환경을 생성하고 관리할 수 있습니다. 이를 통해 프로젝트별로 독립적인 개발 환경을 유지할 수 있습니다.
-->

## *Install Miniconda on Ubuntu*

- According to the documentation ([Installation methods](https://docs.anaconda.com/miniconda/install/#quick-command-line-install)), you need to run the following command. For more details, please refer to the official documentation.    
	`wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh`      
	`bash ~/Miniconda3-latest-Linux-x86_64.sh`      
	`source ~/.bashrc`

<!--
문서(https://docs.anaconda.com/miniconda/install/#quick-command-line-install)에 따르면 아래 명령어를 실행하면 된다. 자세한 사항은 공식 문서를 참고 바란다.
-->

```bash
jaoneol@DESKTOP-B7GM3C5:~$ wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
Miniconda3-latest-Linux-x86_64.sh                100%[=======================================================================================================>] 140.94M  58.7MB/s    in 2.4s

2025-02-05 22:20:49 (58.7 MB/s) - ‘Miniconda3-latest-Linux-x86_64.sh’ saved [147784736/147784736]
jaoneol@DESKTOP-B7GM3C5:~$ ls
Miniconda3-latest-Linux-x86_64.sh
```

```bash
jaoneol@DESKTOP-B7GM3C5:~$ bash ~/Miniconda3-latest-Linux-x86_64.sh

Welcome to Miniconda3 py312_24.11.1-0

In order to continue the installation process, please review the license
agreement.
Please, press ENTER to continue
>>> 
```

```bash
3. Intellectual Property Notice. You acknowledge that, as between You and Anaconda, Anaconda owns all right, title, and interest, including all intellectual property rights, in and to Miniconda
(R) and, with respect to third-party products distributed with or through Miniconda(R), the applicable third-party licensors own all right, title and interest, including all intellectual proper
ty rights, in and to such products.


Do you accept the license terms? [yes|no]
>>> yes

Miniconda3 will now be installed into this location:
/home/jaoneol/miniconda3

  - Press ENTER to confirm the location
  - Press CTRL-C to abort the installation
  - Or specify a different location below

[/home/jaoneol/miniconda3] >>>
```

```bash

Preparing transaction: done
Executing transaction: done
installation finished.
Do you wish to update your shell profile to automatically initialize conda?
This will activate conda on startup and change the command prompt when activated.
If you'd prefer that conda's base environment not be activated on startup,
   run the following command when conda is activated:

conda config --set auto_activate_base false

You can undo this by running `conda init --reverse $SHELL`? [yes|no]
[no] >>> yes
no change     /home/jaoneol/miniconda3/condabin/conda
no change     /home/jaoneol/miniconda3/bin/conda
no change     /home/jaoneol/miniconda3/bin/conda-env
no change     /home/jaoneol/miniconda3/bin/activate
no change     /home/jaoneol/miniconda3/bin/deactivate
no change     /home/jaoneol/miniconda3/etc/profile.d/conda.sh
no change     /home/jaoneol/miniconda3/etc/fish/conf.d/conda.fish
no change     /home/jaoneol/miniconda3/shell/condabin/Conda.psm1
no change     /home/jaoneol/miniconda3/shell/condabin/conda-hook.ps1
no change     /home/jaoneol/miniconda3/lib/python3.12/site-packages/xontrib/conda.xsh
no change     /home/jaoneol/miniconda3/etc/profile.d/conda.csh
modified      /home/jaoneol/.bashrc

==> For changes to take effect, close and re-open your current shell. <==

Thank you for installing Miniconda3!
```

```bash
# The `source` command is useful for running scripts while maintaining the current shell environment.
jaoneol@DESKTOP-B7GM3C5:~$ source ~/.bashrc
(base) jaoneol@DESKTOP-B7GM3C5:~$   
```

```bash
(base) jaoneol@DESKTOP-B7GM3C5:~$ conda --version
conda 24.11.1
(base) jaoneol@DESKTOP-B7GM3C5:~$ conda env list

# conda environments:
#
base                 * /home/jaoneol/miniconda3

(base) jaoneol@DESKTOP-B7GM3C5:~$   
```