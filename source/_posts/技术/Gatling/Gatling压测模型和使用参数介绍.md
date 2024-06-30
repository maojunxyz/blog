---
title: Gatling压测模型和使用参数介绍
date: 2022/03/29 14:33:28
updated: 2022/03/29 14:33:28
categories:
- [技术, Gatling]
tags:
- 软件
- Gatling
- 性能测试
---

## 开放模型

开放模型中我们可以主动的控制用户的到达率。

- 开放模型的好处

  - 了解每阶段用户真实的请求量，与并发的不可控的情况下，这种场景会更加偏向于真实。

  - 每个请求链或者请求都是一个新的连接，复用较少，会发现以前一些因为连接数不够而为发现的问题。

  - 请求持续注入，当系统已经达到性能瓶颈时，不会与并发一样减少请求量，而是会以设置的请求量持续注入。

- 开放模型的坏处
  - 每个虚拟用户都有自己的连接。如果生命周期短，用户创造率很高，那么将不得不打开和关闭大量连接。
  - 与服务端连接数较多，当服务端处理过慢出现堆积的时候，会导致压测脚本崩溃。

### 一次性注入

设置了一次性注入的用户数

```scala
atOnceUsers(4)
```



![一次性注入](./assets/image-jltwc0202rcaywr0t5k09ty.PNG)



### 在指定时间内线性增长用户数到指定数量

设置了总的用户数，然后设置一个分配总用户数的时间，每秒的用户数是一样的。

```scala
rampUsers(4).during(4.seconds)
```

![线性增长用户](./assets/image-utx4scd6kgalt0aotyw4qu6.PNG)



### 恒定速率

### 每秒固定用户数

持续指定时间，设置了每秒注入的用户数，然后设置一个持续的时间，每秒的用户数是一样的。

```scala
constantUsersPerSec(4).during(4.seconds)
```

![每秒固定用户数恒定速率](./assets/image-i7fydusv68ke7gn5fx5xb6e.PNG)





### 每秒用户数从指定值提升到指定值

持续指定时间，每秒的用户数会增加。

```scala
rampUsersPerSec(1).to(4).during(4.seconds)
```

![每秒用户数线性增长恒定速率](./assets/image-hxxs1z8f26jxksyf1c76jm9.PNG)

### 随机速率

### 每秒固定用户数

持续指定时间（每秒的用户数是一样的，但因为是在毫秒级别随机，所有用户数曲线会有波动）。

```scala
constantUsersPerSec(4).during(4.seconds).randomized
```

![每秒固定用户数随机速率](./assets/image-3uv3bwpp4xipfl72jaa9k9q.PNG)



### 每秒用户数从指定值提升到指定值

持续指定时间(每秒的用户数会线性增加到一个指定值，又因为毫秒级别的随机导致曲线会存在波动）。

```scala
rampUsersPerSec(1).to(4).during(4.seconds).randomized
```

![每秒用户数线性增长随机速率](./assets/image-64npwyakojlzdgo8tyl3mkt.PNG)



### 阶跃函数

在指定时间内，以阶跃函数的方式逐步增加用户到指定数量（即每秒递增）（先增长，后衰减）

```scala
heavisideUsers(12).during(5.seconds)
```

![阶跃函数](./assets/image-2lut99xqkt5wofihbjjao0z.PNG)

### 开放模型负载

从指定每秒用户数开始，每持续指定时间，在指定时间段内增加指定每秒用户数，如此循环增长指定次数（阶梯增加用户数）

```scala
incrementUsersPerSec(50)
.times(10)
.eachLevelLasting(120.seconds)
.separatedByRampsLasting(5.seconds)
.startingFrom(100)
```

![开放负载模型](./assets/image-z66h6rbwz535ucw90hd906n.PNG)

## 封闭模型

封闭模型可控制并发用户数，保持一定的连接数并复用，减少因为建立连接所导致的问题，满负荷运行时，新用户只有在另一个用户退出后才能有效地进入系统。

- 封闭模型的好处

  - 可以快速了解到系统的极限值在哪里。
  
  
    - 线程复用，不会每秒打开或者关闭连接导致系统资源出现问题。
  


- 封闭模型的坏处

  - 无法复现因为连接数导致的问题。

  - 无法模拟真实用户的场景。

### 固定并发用户数

指定时间内，固定并发用户数。

```scala
constantConcurrentUsers(1000).during(30.seconds)
```

![固定并发用户数](./assets/image-bk5edfnhwkszeuageh9ghpk.PNG)



### 并发用户数从指定值增加到指定值

指定时间内，并发用户数从指定值增加到指定值。

```scala
rampConcurrentUsers(10).to(50).during(10.seconds)
```

![线性增长固定并发数](./assets/image-2onh0p8s99r3y2dphddqa40.PNG)





### 封闭模型负载

从指定并发开始，每持续指定时间，在指定时间段内增加指定并发数，如此循环增长指定次数。

```scala
incrementConcurrentUsers(100)
.times(10)
.eachLevelLasting(120.seconds)
.separatedByRampsLasting(5.seconds)
.startingFrom(100)
```

![封闭负载模型](./assets/image-hk0wpk5yeowrdr97uyje1cs.PNG)

（完）