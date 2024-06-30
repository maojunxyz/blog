---
title: Typecho 开启Redis缓存
date: 2020/02/27 00:00
updated: 2023/09/03 10:29
categories:
- [技术, 示例]
tags:
- 博客平台
- Typecho
---

## 延迟问题

 在访问博客的时候会稍有等待。但是博客内容以文字为主，配图为辅。页面的延迟会给用户带来不好的体验，所以想着加一个缓存提高页面的响应速度。

为了统计不同地区访问的速度，使用了站长工具的多个地点Ping服务器[^1]来进行统计分析。检测完发现除了海外地区访问速度善可，国内大部分地区访问速度都不理想。



## 配置缓存


个人博客是属于**读多写少**的业务。为了提高服务的响应速度，决定给`Typecho`增加缓存，选用目前市场上热门的`Redis`作为缓存。

  以`Centos 7.xx`环境为例，首先安装`redis`服务和`php-redis`模块服务:

  ```bash
  $ sudo yum -y install redis
  $ sudo yum -y install php-pecl-redis
  ```

  安装完后添加开机自启服务并启动`redis`:

  ```bash
  $ sudo systemctl enable redis
  $ sudo systemctl start redis
  ```

  检查`redis`服务是否启动成功：

  ```bash
  $ systemctl status redis
  ```

  检查`php-redis`模块是否安装成功：

  ```bash
  php -m |grep redis
  ```

>  有输出内容则说明安装成功。

安装完缓存服务后，需要在`Typecho`中开启缓存。为了让`Typecho`可以支持`Redis`缓存，这里采用安装插件的方式让`Typecho`集成缓存配置功能。下载TpCache插件[^2]，放入到`Typecho`的**plugins**目录下。然后去后台点击**插件管理**按钮并启用**TpCache**插件。接着点击**设置**按钮进入缓存配置页面，填写相应的配置信息，点击保存设置。

配置完缓存后，可以去页面刷新查看效果。

注意配置是否开启了**对登入用户失效**,如果开启则用隐私窗口或者先退出账号再测试。

验证`Redis`的缓存是否在工作，可以进入服务器中输入命令`redis-cli`登入`Redis`客户端：

  ```bash
  $ redis-cli
  127.0.0.1:6379> keys *
  1) "43efe54439fbb9c6f647120ec9e02821"
  2) "fcf92f7cb8f50717bba68d1655b4c9a2"
  3) "a8356448e7efd93fdc4c068d4bcb91fb"
  4) "c9db569cb388e160e4b86ca1ddff84d7"
  5) "04ce4f80d6159435fb9b9362fa19442d"
  6) "30da41efe7963d8e81fc37eaf8b656b1"
  7) "45e1dc83b4f004caf1b43bbceccad223"
  8) "271f8547640193f2e8dcaa8bd6cd4be7"
  ```

如果输入`keys *`有输出则说明`Redis`缓存已经起作用了。或者可以通过`redis-cli`的`info`命令查看缓存命中信息：

  ```bash
  $ redis-cli
  127.0.0.1:6379> info
  # Stats
  ...
  keyspace_hits:26
  keyspace_misses:9
  ...
  ```

> 通过**keyspace_hits/(keyspace_hits+keyspace_misses)**计算命令率。

(本文完）
[^1]:[多个地点Ping服务器](http://ping.chinaz.com/)
[^2]:[TpCache](https://github.com/phpgao/TpCache)
