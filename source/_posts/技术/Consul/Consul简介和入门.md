---
title: Consul简介和入门
date: 2020/04/02 23:00:01
updated: 2020/04/02 23:00:01
categories:
- [技术, Consul]
tags:
- Consul
- 微服务
---

## Consul介绍

Consul 是 HashiCorp 公司推出的开源工具，用于实现分布式系统的服务发现与配置。与其它分布式服务注册与发现的方案相比较，Consul 的方案更“一站式”，内置了服务注册与发现框架、分布一致性协议实现、健康检查、Key/Value 存储、多数据中心方案，不再需要依赖其它工具（比如 ZooKeeper 等）。使用起来也较为简单。Consul 使用 Go 语言编写，因此具有天然可移植性(支持Linux、windows和MacOS X)；安装包仅包含一个可执行文件，方便部署，与 Docker 等轻量级容器可无缝配合。

Consul和其他服务注册和发现框架的对比：

|         对比项         |            euerka             |          Consul          |       zookeeper        |       etcd        |
| :--------------------: | :---------------------------: | :----------------------: | :--------------------: | :---------------: |
|      服务健康检查      |           可配支持            | 服务状态，内  存，硬盘等 | (弱)长连接， keepalive |     连接心跳      |
|       多数据中心       |               /               |           支持           |           /            |         /         |
|       ky存储服务       |               /               |           支持           |          支持          |       支持        |
|         一致性         |               /               |           raft           |         paxos          |       raft        |
|          cap           |              ap               |            Cp            |           Cp           |        Cp         |
| 使用接口(多语  言能力) |             http              |      支持http和dns       |         客户端         |     http/grpc     |
|       watch支持        | 支持long polling/大  部分增量 | 全量/支持long   polling  |          支持          | 支持long  polling |
|        自身监控        |            metrics            |         metrics          |           /            |      metrics      |
|          安全          |               /               |        acl /https        |          acl           | https支持    (弱) |
|   spring cloud  集成   |            已支持             |          已支持          |         已支持         |      已支持       |

#### 特性

- 服务发现

- 健康检查

- Key/Value 存储

- 多数据中心

- 社区活跃


#### 优势

- 使用 Raft 算法来保证一致性, 比复杂的 Paxos 算法更直接. 相比较而言, zookeeper 采用的是 Paxos, 而 etcd 使用的则是 Raft。

- 支持多数据中心，内外网的服务采用不同的端口进行监听。  多数据中心集群可以避免单数据中心的 单点故障,而其部署则需要考虑网络延迟, 分片等情况等。  zookeeper 和 etcd 均不提供多数据中心 功能的支持。

- 支持健康检查。  etcd 不提供此功能。

- 支持 http 和 dns 协议接口。  zookeeper 的集成较为复杂, etcd 只支持 http 协议。

- 官方提供 web 管理界面, etcd 无此功能。

- 综合比较, Consul 作为服务注册和配置管理的新星, 比较值得关注和研究。

- 提供了rest api 便于集成：  https://www.consul.io/api-docs/index

#### 角色

- client: 客户端, 无状态, 将 HTTP 和 DNS 接口请求转发给局域网内的服务端集群。
- server: 服务端, 保存配置信息, 高可用集群, 在局域网内与本地客户端通讯, 通过广域网与其它数据中心通讯。每个数据中心的 server 数量推荐为 3 个或是 5 个。

Consul 客户端、服务端还支持跨中心的使用，更加提高了它的高可用性。



#### 基础架构

- agent

组成 consul 集群的每个成员上都要运行一个 agent，可以通过 consul agent 命令来启动。agent可以运

行在 server 状态或者 client 状态。自然的，运行在 server 状态的节点被称为 server 节点；运行在

client 状态的节点被称为 client 节点。

- server 节点

负责组成 cluster 的复杂工作（选举server 自行选举一个 leader、状态维护、转发请求到 leader），以

及 consul 提供的服务（响应RPC 请求），以及存放和复制数据。考虑到容错和收敛，一般部署 3 ~ 5 个

比较合适。

- client 节点

负责转发所有的 RPC 到 server 节点。本身无状态，且轻量级，因此，可以部署大量的client 节点。

- 数据中心

虽然数据中心的定义似乎很明显，但仍有一些细微的细节必须考虑。我们将一个数据中心定义为一个私

有、低延迟和高带宽的网络环境。这不包括通过公共互联网的通信，但是为了我们的目的，单个EC2 区

域内的多个可用区域将被视为单个数据中心的一部分。

另外，server 自行选举一个 leader。虽然 Consul 可以运行在一台 server ，但是建议使用 3 到 5 台来避免失败情况下数据的丢失。每个数据中心建议配置一个 server 集群。

在基础设施中需要发现其他服务的组件可以查询任何一个 Consul 的 server 或者agent，Agent 会自动

转发请求到 server。每个数据中心运行了一个 Consul server 集群。当一个跨数据中心的服务发现和配置请求创建时，本地Consul Server 转发请求到远程的数据中心并返回结果。

![consul流程图](./assets/image-1db1r20an3eb0kb55xm13r5.PNG)

#### 现服务注册和发现

![服务注册和发现](./assets/image-tpv569baonfctdmlcvmofiw.PNG)

Consul在项目中发挥服务注册与发现的功能，我们讲解下它的工作原理：

1. 当Producer启动的时候，会向Consul发送一个post请求，并向Consul传输自己的IP和Port。
2. Consul 接收到Producer的注册后，每隔10s（默认）会向Producer发送一个健康检查的请求，检验Producer是否健康。
3. 当Consumer以Http的方式向Producer发起请求，会先从Consul中拿到一个存储服务IP和Port的临时表，从表中拿到Producer的IP和Port后再发送请求。
4. 该临时表每隔10s会更新，只包含有通过了健康检查的Producer。

## Consul安装

### 下载

下载地址：https://www.consul.io/downloads.html；

历史版本下载地址：https://releases.hashicorp.com/consul/

![下载安装](./assets/image-cvvvcvvbm9hx838328hk75b.PNG)

下载后解压添加到环境变量到path中。

### 启动

consul server运行，参考如下命令：

```sh
agent -server -bootstrap-expect 1 -data-dir
server -node=n1 -ui -client=0.0.0.0 -bind=192.168.10.11
```

- -server 定义 agent 运行在 server 模式
- -bootstrap-expect 1 在一个 datacenter 中期望提供的 server 节点数目，当该值提供的时候，consul一直等到达到指定 sever 数目的时候才会引导整个集群。这里我们为了方便演示只启动一个服务端
- -data-dir 参数设置 Consul 自己的维护的数据存储路径
- -node=n1 节点在集群中的名称，在一个集群中必须是唯一的，默认是该节点的主机名
- bind=192.168.10.11 该地址用来在集群内部的通讯，集群内的所有节点到地址都必须是可达的，默认是 0.0.0.0
- -datacenter=dc1 指定当前数据中心名字，该参数可以不设置
- -ui 指定可以以 UI 的方式呈现，当前的 UI 访问地址是:http://本机 IP:8500
- -client=0.0.0.0 consul 服务侦听地址，这个地址提供 HTTP、DNS、RPC 等服务，默认是127.0.0.1所以不对外提供服务，如果你要对外提供服务改成 0.0.0.0，我们当前配置为对外提供地址。

完整启动选项参数见官方文档：https://www.consul.io/docs/agent/options

启动后访问控制台UI: http://192.168.10.11:8500/

![控制台UI](./assets/image-flbmbpu8upwukh1i88fudah.PNG)

- Services ：服务信息。
- Nodes ：节点信息，Consul支持集群。
- Key/Value ：存储的动态配置信息。
- ACL ：权限信息。
- Intentions ：通过命令的方式对consul进行管理。

consul client运行，参考命令如下：

```sh
 ./consul agent -data-dir /opt/itcast/springcloud/data/client -node=n2 -ui -client=0.0.0.0 -bind=192.168.10.30 -join 192.168.10.11
```

通过以上两步操作，就启动了一个完整的数据中心。如果为了方便使用，可以使用如下命令启动一个agent server。

```sh
consul agent -dev
```



## Consul实践

项目中要想使用Consul作为服务注册中心，只需要引入如下依赖包，在启动类上添加

@EnableDiscoveryClient 注解，并在 application.yml 中添加Consul服务地址即可：

1. 添加依赖

在项目 hailtaxi-order 添加依赖包：

```xml
<!--consul-->
<dependency>
<groupId>org.springframework.cloud</groupId>
<artifactId>spring-cloud-starter-consul-discovery</artifactId>
<version>2.2.1.RELEASE</version>
</dependency>
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-actuator</artifactId>
<version>2.2.10.RELEASE</version>
</dependency>
```

2. 添加 @EnableDiscoveryClient 注解

在 hailtaxi-order 启动类 OrderApplication 上添加 @EnableDiscoveryClient 注解。

3. 配置Consul服务信息

在 application.yml 中添加Consul服务信息：

![配置信息](./assets/image-vgqy0uuer4lpnoldvmqtig3.PNG)

Yaml配置信息如下：

```yaml
spring:
application:
name: hailtaxi-order
datasource:
driver-class-name: com.mysql.cj.jdbc.Driver
# 使用docker启动MySQL数据库：
# 1. docker run -itd --name mysql-hailtaxi -p 3306:3306 -v
/opt/itcast/spring-cloud/data/mysql:/var/lib/mysql -v /opt/itcast/springcloud/sql/:/opt/sql -e MYSQL_ROOT_PASSWORD=123456 mysql:8.0
# 2. docker exec -it mysql-hailtaxi bash
# 3. 执行sql语句
url: jdbc:mysql://linux30:3306/hailtaxi-order?
useUnicode=true&characterEncoding=UTF-8&serverTimezone=UTC
username: root
password: 123456
cloud:
#Consul配置
consul:
host: localhost
port: 8500
discovery:
#注册到Consul中的服务名字
service-name: ${spring.application.name}
#注册的服务的实例 Id，最好不要重复，这里参考官网建议的方式 带随机数
#instance-id:
${spring.application.name}:${vcap.application.instance_id:${spring.application.i
nstance_id:${random.value}}}
# 自定义实例id为:应用名:ip:port
instance-id: ${spring.application.name}:${spring.cloud.client.ipaddress}:${server.port}
prefer-ip-address: true
# 开启服务注册
register: true
# 开启服务发现
enabled: true
#2 分钟之后健康检查未通过取消注册
health-check-critical-timeout: 2m
#consul 健康检查的轮询周期
health-check-interval: 10s

```

4. Consul服务数据

![服务数据](./assets/image-9gec8m7u1xih4kpfghke7am.PNG)


（完）