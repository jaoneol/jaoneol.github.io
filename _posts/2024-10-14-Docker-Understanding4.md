---
title: Dockerfile commands
description: Let's learn Dockerfile commands to Write a Dockerfile.
author: DS2Man
date: 2024-10-13 11:00:00 +0000
categories: [Docker, Understanding]
tags:
  - WSL
  - Ubuntu
  - Docker
  - Command
math: true
pin: true
---

## *Basic format*

```bash
# Set Base Image
FROM <base_image>

# Maintainer Information (Optional)
LABEL maintainer="your_email@example.com"

# Set Environment Variables
ENV <key>=<value>

# Set Working Directory
WORKDIR /app

# Copy Files
COPY <src> <dest>
ADD <src> <dest>

# Install Packages
RUN <command>

# Command to Execute When Container Runs
CMD ["executable", "arg1", "arg2"]
ENTRYPOINT ["executable", "arg1", "arg2"]

# Expose Port
EXPOSE <port>

# Configuration for Container Execution
VOLUME ["/data"]

# Change User Permissions
USER <username>

# Command to Execute When Container Starts
CMD ["python", "app.py"]

```


## *Explanation of Key Commands*

- **From**
	Command to specify the base image    
	`FROM <base_image>`
		
```bash
# Full Image(in my case, 440MB)
# Based on Debian with a full set of system libraries and utilities.
# it includes tools like `gcc`, `curl`, and `gi`
FROM python:3.11
################################################
# Lightweight Image(in my case, 377MB)
# Based on Debian Slim, containing only essential packages.
# Lacks many common utilities (`gcc`, `make`, `curl`, `git`, 'vim', etc.), requiring manual installation if needed.
FROM python:3.11-slim
RUN apt-get update && apt-get install -y vim && rm -rf /var/lib/apt/lists/*
```

- **LABEL**
	Adds metadata such as author information and version.    
	`LABEL {key}={value}`
		
```bash
LABEL my-email="ds2man@example.com"
LABEL version="1.0"
```

```bash
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker inspect ds2man/dockertest:v0.1 | grep Label -A 5
            "Labels": {
                "my-email": "ds2man@example.com",
                "version": "1.0"
            }
        },
        "Architecture": "amd64",
```

- **ENV**
	Sets environment variables inside the container.    
	`ENV {key}={value}`
		
```bash
# Set Python environment variable to prevent buffering
ENV PYTHONUNBUFFERED=1
ENV MY_ENV=3
```

```bash
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker exec -it mydocker env
PATH=/usr/local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=2fe44df6540c
TERM=xterm
USE_CONFIGMAP=True
CONFIGMAP_PATH=./config/myconfigmap.yml
MYSQL_USER=userid
MYSQL_PASSWORD=passwd1
PYTHONUNBUFFERED=1
MY_ENV=3
HOME=/root
```

- **WORKDIR**
	Sets the working directory inside the container.    
	`WORKDIR {work_directory}`
		
```bash
WORKDIR /app
```

```bash
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker inspect mydocker | grep "WorkingDir" -A 4
            "WorkingDir": "/app",
            "Entrypoint": [
                "python",
                "dockertest_main.py"
            ],
```

- **COPY & ADD**
	**COPY**: Copies local files to the container. Create a new layer each time a file is copied.    
	**ADD**: Similar to COPY but can also extract compressed files. It copies a compressed file (`.tar.gz`) into the container while automatically extracting it. Create a new layer each time a file is copied.    
	`COPY {host file or directory} {container path}`    
	`ADD {extract compressed files} {container path}`    
		
```bash
COPY requirements.txt . 
ADD config.tar.gz ./config/
```

```bash
#######################################
# If `config.tar.gz` is compressed as shown below, 
config.tar.gz 
├── settings.json 
├── database.yml
#######################################
# it will be extracted into the `./config/` directory inside the container.
./config/ 
├── settings.json 
├── database.yml
```

- **RUN**
	Executes commands during the image build process, typically for installing dependencies.    
	Creates a new layer each time it is executed.
	`RUN {command}`
		
```bash
RUN apt-get update && apt-get install -y curl 
RUN pip install -r requirements.txt
```

- **CMD & ENTRYPOINT**
	**CMD**: Provides a default command, which can be **fully overridden**. Use CMD when you want to provide a default command that users **can override**.    
	**ENTRYPOINT**(In my case, Recommend): Ensures a **fixed command** runs, passing arguments instead of replacing it. Use ENTRYPOINT when the container **must always** run a specific application.    
	For reference, use both when you need a fixed command with flexible default parameters.
	`CMD [{command}, {parameter1}]`    
	`ENTRYPOINT [{command}, {parameter1}, {parameter2}]`    
		
```bash
CMD ["python", "app.py"] 
ENTRYPOINT ["python", "app.py"]
```

|Feature|`CMD ["python", "app.py"]`|`ENTRYPOINT ["python", "app.py"]`|
|---|---|---|
|Default Execution|Runs `python app.py`|Runs `python app.py`|
|Override Behavior|Replaced completely|Treated as additional arguments|
|Example Override|`docker run myapp python other_script.py` (Runs `python other_script.py`)|`docker run myapp --debug` (Runs `python app.py --debug`)|
|Use Case|When users should be able to replace the command|

- **EXPOSE(In my case, Not recommended)**
	Defines the port that the container will use. However, it does not automatically expose it to the host.    
	`EXPOSE {port}`
		
```bash
EXPOSE 8080
```

```
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker run -d -p 8080:8080 myfastapi
```

- **VOLUME(In my case, Not recommended)**
	The `VOLUME` instruction in a **Dockerfile** is used to create a **persistent storage location** inside the container. It ensures that the specified directory (`/data` in this case) is **not lost** when the container stops or is removed.    
	`VOLUME [{container directory}]`   		
	
```bash
VOLUME ["/data"]
```

```
(MyDev) jaoneol@DESKTOP-B7GM3C5:~/GP-MyReference/10.MyDockerTest$ docker run -d --name mydocker -v /home/user/data:/data myimage:latest
```

- **USER**
	Sets the user that will run inside the container for security purposes.   
	`USER {userid}`
		
```bash
# Create a new user "nonroot"
RUN useradd -m nonroot 
# Switch to nonroot user
USER nonroot
# Set working directory (important for non-root users) 
WORKDIR /home/nonroot
```