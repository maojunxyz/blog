---
title: Docker安装Mysql
date: 2023/11/04 09:40
updated: 2023/11/04 09:40
categories:
- [技术, Docker]
tags:
- Docker
- Mysql
---



mysql官方DockerHub地址：https://hub.docker.com/_/mysql

可选的环境变量：

```sh
MYSQL_ROOT_PASSWORD
MYSQL_DATABASE
MYSQL_USER, MYSQL_PASSWORD
MYSQL_ALLOW_EMPTY_PASSWORD
MYSQL_RANDOM_ROOT_PASSWORD
MYSQL_ONETIME_PASSWORD
MYSQL_INITDB_SKIP_TZINFO
```

创建一个环境变量配置文件，`vim env_file`，添加以下内容：

```ini
MYSQL_ROOT_PASSWORD=root
MYSQL_DATABASE=hello_world
MYSQL_USER=foo
MYSQL_PASSWORD=bar
```

创建一个docker运行脚本，`vim docker_run.sh`,添加以下内容:

```sh
docker run --name=mysql -d -p 3306:3306 --env_file=./env_file mysql
```

编写完脚本运行:

```sh
sh docekr_run.sh
```

（完）