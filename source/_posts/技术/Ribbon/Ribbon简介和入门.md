---
title: Ribbon简介和入门
date: 2020/04/02 23:42:20
updated: 2020/04/02 23:42:20
categories:
- [技术, Ribbon]
tags:
- Ribbon
- 微服务
---

## Ribbon介绍

Ribbon是Netflix发布的负载均衡器，有助于控制HTTP客户端行为。为Ribbon配置服务提供地址列表
后，Ribbon就可基于负载均衡算法，自动帮助服务消费者请求。
Ribbon默认提供的负载均衡算法：轮询，随机,重试法,加权。当然，我们可用自己定义负载均衡算法。

### Ribbon实践

![业务介绍](./assets/image-asifss1jjk0hiuyywy1mqns.PNG)

如上图，当用户下单调用 hailtaxi-order 服务的时候，该服务会调用 hailtaxi-driver ，此时如果是抢单过程，查询压力也会很大，我们可以为 hailtaxi-driver 做集群，做集群只需要把工程复制多份即可，多个工程如下图：

![本地伪集群配置](./assets/image-9lmfsov6eazrzqaazwai32m.PNG)

在idea中将项目的启动配置复制一份出来，修改启动端口号

![修改启动端口](./assets/image-agvr7hq73x9lg01v3hersj7.PNG)

调用测试，访问：http://localhost:18082/order调用，可以发现已经实现负载均衡了， 18081 和 18085 ， 18086 服务默认是轮询访问。



### Ribbon算法

我们上面没做任何相关操作，只是把服务换成了多个就实现了负载均衡，这是因为OpenFeign默认使用了Ribbon的轮询算法,如下图依赖包，引入OpenFeign的时候会传递依赖Ribbon包：

![OpenFeign依赖](./assets/image-oe6d8tbp0dt7m31a5alyihg.PNG)

![依赖树](./assets/image-fz9srp8zoiundd5a8kqwtl3.PNG)



Ribbon支持多种负载均衡算法，我们可以按照自己的需求使用相关算法，在 hailtaxi-order 配置类中

配置如下规则：

```java
/***
* 负载均衡算法设置
*/
@Bean //要交给容器管理
public IRule randomRule(){
//随机算法
return new RandomRule();
//重试算法
//return new RetryRule();
//加权法
//return new ZoneAvoidanceRule();
}
```

- RoundRobinRule

  轮询

- RandomRule

  随机算法

- RetryRule

  重试算法。该算法先按照轮询的策略获取服务,如果获取服务失败则在指定的时间内会进行重试，获取可用的服务。

- ZoneAvoidanceRule

  加权算法。会根据平均响应时间计算所有服务的权重，响应时间越快服务权重越大被选中的概率越大。刚启动时如果同统计信息不足，则使用轮询的策略，等统计信息足够会切换到自身规则。

（完）