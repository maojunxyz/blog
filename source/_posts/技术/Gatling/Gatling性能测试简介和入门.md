---
title: Gatling性能测试工具简介和入门
date: 2022/03/28 17:51:15
updated: 2022/03/28 17:51:15
categories:
- [技术, Gatling]
tags:
- 软件
- Gatling
- 性能测试
---

## 简介
**Gatling**是一款基于**Scala**开发的高性能服务器端性能测试工具。具备以下特点:

- 支持Akka Actors和Async IO；
- 支持实时生成html动态轻量级报表；
- 支持DSL脚本；
- 支持插件的开发和使用；
- 支持脚本的录制和导入；
- 社区版开源免费，可以二次开发。

## 下载

[下载地址](https://gatling.io/products)

![开源版下载](./assets/image-20240405174953012.png)

## 使用

1. 解压下载的工具包后进入根目录

   ![目录结构](./assets/image-v0zxc0uvyf9nptxyonz8drm.PNG)

- bin目录

  启动脚本及基本配置 gatling.bat（或gatling.sh），通过执行它来开启压测；

- conf目录

  些配置项，包括一些目录的配置，基础协议的配置

- lib目录

  依赖的第三方jar包，自己开发的插件或压测脚本依赖的第三方jar也可以放入该目录下

- results目录

  每次压测的执行结果包括结果记录和生成的html报表

- user-files目录

  有两个目录，resources和simulations，resources目录下存放压测参数化数据文件和脚本中用到的其他配置文件；simulations目录下存放脚本源码，当simulations目录下存在脚本源码时，每次启动压测都会先进行脚本编译，编译后的脚本会存放到target/test-classes目录下

2. 运行脚本

- Windows下执行bin\gatling.bat
- Linux下执行bin/gatling.sh

工具会先对user-files/simulations下的脚本进行编译，然后展示target/test-classes下的所有已经编译的脚本。

![压测脚本列表](./assets/image-nank3fhc9se38fugqp64lic.PNG)

3. 选择一个脚本编号，然后回车，输入压测描述，再次回车开始发压

![选择脚本执行](./assets/image-53a9vt9djmvyb665uk4g7pj.PNG)

压测结束后，会在控制台会展示压测结果信息。

![压测结果](./assets/image-ztumab65rcjy32htve2m7xz.PNG)

- 包括请求总数（成功/失败）
- 最小响应时间（成功/失败）
- 最大响应时间（成功/失败）
- 平均响应时间（成功/失败）
- 标准差（成功/失败）
- 响应时间TP50（成功/失败）
- 响应时间TP75（成功/失败）
- 响应时间TP90（成功/失败）
- 响应时间TP99（成功/失败）
- QPS （成功/失败）
- 响应时间分布（800ms-1200ms）

同时在控制台中也会显示生成的html报表地址。



## 压测报告

- 压测概览（包括响应时间的大体分布、成功/失败占比、QPS、响应时间等）

![压测概览](./assets/image-u41kqaxn6wp5mcheifh9vdv.PNG)

- 活跃用户数（描述在时间轴上每一秒的实际并发用户数）

  ![活跃用户数](./assets/image-c61i4i2onrjfttx34e6pbcm.PNG)

- 响应时间详细分布（描述每个响应时间的请求数）

  ![相应时间分布](./assets/image-31h00mqopcm39zsrd7fn01q.PNG)

- 响应时间百分位数（响应时间在时间轴上的各个百分位数）

  ![相应时间百分比](./assets/image-y0nlx9kc1tjmnxs7ql9t5ge.PNG)

- 每秒请求数（每秒发送请求在时间轴上的个数）

  ![每秒请求数](./assets/image-5duojboh0e04pmw3a4yaw7r.PNG)

- 每秒响应数（每秒收到响应在时间轴上的个数）

  ![每秒响应数](./assets/image-25xe0ohek4uiizqxcy8z3c8.PNG)

- RPS的响应时间分布（展示了每个RPS下的响应时间分布）

  ![RPS响应时间分布](./assets/image-z43yepcf23yktkgnfdmqyoo.PNG)

（完）



**参考**

[并发用户、RPS、TPS的解读](https://help.aliyun.com/zh/pts/interpretation-of-concurrent-users-and-rps-and-tps)