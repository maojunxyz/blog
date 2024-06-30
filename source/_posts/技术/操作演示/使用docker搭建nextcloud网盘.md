---
title: 使用 docker 搭建 nextcloud 网盘
date: 2020/02/25 00:00
updated: 2023/09/01 07:02
categories:
- [技术, 示例]
tags:
- 对象存储
- Nextcloud
---

## 介绍

Nextcloud 中文有下一代云盘的意思，在这个私有部署网盘上我们可以做很多事情：资料存储、密码管理、存储、离线下载等。有些功能已经自带集成在 Nextcloud 中了，另一些功能可以通过安装插件实现。

下面使用 docker-compose 来安装配置 nextcloud 网盘。



## 环境依赖

- centos 7.x
- docker
- docker-compose

>  使用docker-compose可以快速编排`docker`服务，让容器管理变得简单。



编写 `docker-compose.yaml` 文件，内容如下:

```yaml
version: '3'
services:
  nextcloud:
    image: nextcloud:apache
    container_name: nextcloud_web
    depends_on:
      - redis
    environment:
      - uid=1000
      - gid=1000
      - upload_max_size=10g
      - apc_shm_size=128m
      - opcache_mem_size=128
      - cron_period=15m
      - tz=aisa/shanghai
      - domain=http://exmaple.com


    volumes:
       - /app/docker/nextcloud:/var/www/html
    expose:
      - 7009
    ports:
      - 7009:80/tcp
    restart: always

  redis:
    image: redis:alpine
    container_name: redis
    restart: always
    expose:
      - 6379
    restart: always

  onlyoffice:
    image: onlyoffice/documentserver
    container_name: onlyoffice
    hostname: onlyoffice
    environment:
      - redis_server_host=redis
      - redis_server_port=6379
    volumes:
      - /app/docker/onlyoffice/data:/var/www/onlyoffice/data
      - /app/docker/onlyoffice/logs:/var/log/onlyoffice
    restart: always
    expose:
      - 9000
    ports:
      - 9000:80
```

> 注意:`yaml`文件严格区分缩进和空格。



`docker-compose`参数说明:

- image 镜像地址
- container_name 容器名称
- depends_on 依赖服务（关系到启动顺序,先启动依赖）
- enviroment 环境变量参数，对应相应的软件服务
- volumes 将docker里的目录和宿主机目录映射挂载
- expose 需要暴露的端口
- ports 和宿主机端口映射
- restart:always 设置开机自启
- links 设置连接容器（如web服务连接到db)

> 注意,容器无法保存数据，删除后数据就丢失了，配置volumes有利于数据持久化到硬盘



由于是个人和小团队使用，上述的配置中使用了`nextcloud`默认的`sqlite`数据库。如果你的`nextcloud`需要考虑到一定的并发和一定规模人数使用，建议使用`mysql`等数据库替换内嵌型数据库。

配置redis: 上述的配置我们让docker依赖了redis，这个需要配置才能让docker redis缓存生效。编辑配置文件：

```bash
$ vim /app/docker/nextcloud/config/config.php
```

> 具体路径为`docker-compse`中volume中映射的宿主机路径



找准位置在【'memcache.local' => '\OC\Memcache\APCu',】 这行后面

增加下面的代码：

```php
'memcache.locking' => '\OC\Memcache\Redis',
'redis' => array(
     'host' => 'redis',
     'port' => 6379,
      ),
```

> 注意修改对应的`host`和`port`，`redis`和`nextcloud`同`docekr`网络故可以用`container name`替代。由于修改的是**php**脚本文件，所以保存后无需重启就可生效。



检查redis配置是否生效: 进入`redis`容器环境

```bash
$ docker exec -it redis /bin/sh
```

执行redis监控

```sh
redis-cli monitor
```

然后去访问`nextcloud`页面并进行页面操作。查看`redis`监控日志，如果有日志输出则说明配置成功。

启动服务: 在docker-compose.yaml文件目录使用`docker-compose up -d`即可后台启动服务,启动并设置账号密码登入。

安装插件： 由于服务是安装在内网上，并且是直接通过ip和端口访问的，所以并没有配置https.部门需要https状态才可以使用，故不在这推荐，下面一些插件都是可以在http状态使用的。插件可以在应用里面找到并开启，但是nextcloud插件市场在国内访问是有问题的，无法显示插件信息及下载插件。

所以我们可以通过离线安装的方式使用插件： [all apps - app store - nextcloud](https://web.archive.org/web/20201019230404/https://apps.nextcloud.com/) 在nextcloud应用市场找到插件包并下载，然后将其上传到nextcloud的apps目录,如果是linux系统可以直接借助scp或rsync上传.以scp上传onlyoffice插件为例：

```sh
$ scp onlyoffice.tar.gz [email protected]:/app/docker/nextloud/apps
```

进入apps目录解压并删除压缩包：

```sh
$  tar -zxf onlyoffice.tar.gz && rm -rf onlyoffice.tar.gz
```

> 注意：如果不是root账号，可以会上传失败。(docker目录是root用户组)



以下插件安装步骤同上：

- onlyoffice office 文档在线编辑，支持团队协作。这个插件需要配置本地服务使用（即docker-compose.yaml中的onlyoffice),然后在设置中配置对应的onlyoffice 服务地址和开启对应的在线预览office格式。

- drawio 在线画流程图，uml工具。官方可以通过draw.io直接使用，不过要同步数据的话要登入goole或github等，国内用这些服务容易受限于网络环境。用nextcloud 插件可以让数据保存在网盘上。在设置，其他设置下服务地址，主题和默认语言设置等。

新建drawio文件,选择+创建diagam文件会创建一个xml格式的文件并进入drawio工作空间。 选择导入draw.io创建保存的本地文件

> 注意，在draw.io保存的格式是`.drawio`格式,不能直接用nextcloud点击打开



- javascript xmpp chat 在线即时沟通工具。 安装后进入设置,找到javascript xmpp chat设置即可。

注意，默认消息是不会持久化保存的，如果需要将聊天记录存档，需要通过右侧聊天窗口右下角点击设置

Nextcloud 具有高度的可定制性，上面只是 Nextcloud 的一小部分功能，更多功能可以参考 Nextcloud 官方文档。

(本文完）
