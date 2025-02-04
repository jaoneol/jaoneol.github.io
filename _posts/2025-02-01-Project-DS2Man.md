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

Let's install MySQL which are necessary for the LLM service(DS2Man).

<!--
Let's install MySQL which are necessary for the LLM service(DS2Man).
-->

## *Install MySQL on Ubuntu*

- MySQL    
	`docker pull mysql`        
	`docker run -d --restart always --name mysql -e MYSQL_ROOT_PASSWORD=1234 -d -p 3306:3306 mysql:latest`      

<!--
MySQL
-->

```bash
jaoneol@DESKTOP-B7GM3C5:~$ docker pull mysql
Using default tag: latest
latest: Pulling from library/mysql
2c0a233485c3: Pull complete
21577e00f2ba: Pull complete
c294da35c13e: Pull complete
facc8f3107c1: Pull complete
de4342aa4ad8: Pull complete
4643f1cf56c2: Pull complete
139aca660b47: Pull complete
b10e1082570e: Pull complete
26313a3e0799: Pull complete
d43055c38217: Pull complete
Digest: sha256:45f5ae20cfe1d6e6c43684dfffef17db1e1e8dc9bf7133ceaafb25c16b10f31b
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest
jaoneol@DESKTOP-B7GM3C5:~$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
mysql        latest    a52cba19e8cc   13 days ago   797MB
```

```bash
# docker run --name mysql -e MYSQL_ROOT_PASSWORD=1234 -d -p 3306:3306 mysql:latest
# docker update --restart always mysql # docker stop mysql 할 필요없음. 바로 update 됨.
# docker start mysql
jaoneol@DESKTOP-B7GM3C5:~$ docker run -d --restart always --name mysql -e MYSQL_ROOT_PASSWORD=1234 -d -p 3306:3306 mysql:latest
987a0e7deafecd99436ce56629150a2fbe648380620c5230fa24869e22c8b6d1
jaoneol@DESKTOP-B7GM3C5:~$ docker ps -a
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS                                                  NAMES
987a0e7deafe   mysql:latest   "docker-entrypoint.s…"   7 seconds ago   Up 6 seconds   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   mysql
jaoneol@DESKTOP-B7GM3C5:~$
```

- 계정 생성
-
<!--
계정 생성 및 삭제
-->

```sql
-- 외부 접근을 허용하는 사용자 추가
create user 'user_id'@'%' identified by 'passwd';
drop user 'user_id';

-- 내부 접근을 허용하는 사용자 추가
create user 'user_id'@'localhost' identified by 'passwd';
drop user 'user_id';

-- 특정 ip만 접근을 허용하는 사용자 추가
create user 'user_id'@'123.456.789.100' identified by 'passwd';
drop user 'user_id';

-- 특정 ip 대역을 허용하는 사용자 추가
create user 'user_id'@'192.168.%' identified by 'passwd';
drop user 'user_id';
```

- 사용자 권한 부여
-
<!--
사용자 권한 부여
-->

```sql
-- 모든 데이터베이스의 모든 테이블에 모든 권한을 줌
grant all privileges on *.* to 'user_id'@'%';
flush privileges;

-- 특정 데이터베이스의 모든 테이블에 모든 권한을 줌
grant all privileges on 'db_name'.* to 'userid'@'%';
flush privileges;

-- 특정 데이터베이스의 특정 테이블에 모든 권한을 줌
grant all privileges on 'db_name'.'table_name' to 'userid'@'%';
flush privileges;

-- 특정 데이터베이스의 특정 테이블에 select, insert 권한을 줌
grant select, insert on 'db_name'.'table_name' to 'userid'@'%';
flush privileges;

-- 특정 데이터베이스의 특정 테이블의 컬럼1과 컬럼2의 update 권한을 줌
grant update(컬럼1, 컬럼2) on 'db_name'.'table_name' to 'userid'@'%';
flush privileges;
```

- comment
-
<!--
comment
-->

```bash
```

- comment
-
<!--
comment
-->

```bash
```