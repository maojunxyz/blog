---
title: Dubbo简介与原理
date: 2020/04/01 23:02:50
updated: 2020/04/01 23:02:50
categories:
- [技术, Dubbo]
tags:
- Spring
- Dubbo
---

## Dubbo架构体系

### 概述

Dubbo是一个高性能优秀的服务框架，使得应用可通过高性能的 RPC 实现服务的输出和输入功能，可以和 Spring框架无缝集成。同时Dubbo是一款高性能、轻量级的开源Java RPC框架，它提供了三大核心能力：

1. 面向接口的远程方法调用。
2. 智能容错和负载均衡。
3. 服务自动注册和发现。

### 运行架构

![image-ofctlzky2dk2e8v8gaq4i6t](./assets/image-ofctlzky2dk2e8v8gaq4i6t.PNG)

#### 节点角色

| 节点      | 角色说明                               |
| --------- | -------------------------------------- |
| Provider  | 暴露服务的服务提供方                   |
| Consumer  | 调用远程服务的服务消费方               |
| Registry  | 服务注册与发现的注册中心               |
| Monitor   | 统计服务的调用次数和调用时间的监控中心 |
| Container | 服务运行容器                           |



#### 调用关系

1. 服务容器负责启动，加载，运行服务提供者。

2. 服务提供者在启动时，向注册中心注册自己提供的服务。
3. 服务消费者在启动时，向注册中心订阅自己所需的服务。
4. 注册中心返回服务提供者地址列表给消费者，如果有变更，注册中心将基于长连接推送变更数据给消费者。

5. 服务消费者，从提供者地址列表中，基于软负载均衡算法，选一台提供者进行调用，如果调用失败，再选另一台调用。

6. 服务消费者和提供者，在内存中累计调用次数和调用时间，定时每分钟发送一次统计数据到监控中心

> dubbo 的特点有连通性、健壮性、伸缩性、以及向未来架构的升级性。



### 整体设计

![Dubbo调用设计](./assets/image-3gg2d8fkulhczchlfayf754.PNG)

#### 说明

- 图中左边淡蓝背景的为服务消费方使用的接口，右边淡绿色背景的为服务提供方使用的接口，位于中轴线上的为双方都用到的接口。
- 图中从下至上分为十层，各层均为单向依赖，右边的黑色箭头代表层之间的依赖关系，每一层都可以剥离上层被复用，其中，Service 和Config 层为 API，其它各层均为 SPI。
- 图中绿色小块的为扩展接口，蓝色小块为实现类，图中只显示用于关联各层的实现类。
- 图中蓝色虚线为初始化过程，即启动时组装链，红色实线为方法调用过程，即运行时调时链，紫色三角箭头为继承，可以把子类看作父类的同一个节点，线上的文字为调用的方法

#### 各层说明

- config 配置层

  对外配置接口，以 ServiceConfig ,ReferenceConfig 为中心，可以直接初始化配置类，也可以通过spring 解析配置生成配置类。

- proxy 服务代理层

  服务接口透明代理，生成服务的客户端 Stub 和服务器端 Skeleton, 以 ServiceProxy 为中心，扩展接口为ProxyFactory。

- registry 注册中心层

  封装服务地址的注册与发现，以服务 URL 为中心，扩展接口为 RegistryFactory , Registry , RegistryService。

- cluster 路由层

  封装多个提供者的路由及负载均衡，并桥接注册中心，以 Invoker 为中心，扩展接口为 Cluster , Directory ,Router , LoadBalance。

- monitor 监控层

  RPC 调用次数和调用时间监控，以 Statistics 为中心，扩展接口为 MonitorFactory , Monitor , MonitorService。

- protocol 远程调用层

  封装 RPC 调用，以 Invocation , Result 为中心，扩展接口为 Protocol , Invoker , Exporter。

- exchange 信息交换层

  封装请求响应模式，同步转异步，以Request , Response 为中心，扩展接口为 Exchanger ,ExchangeChannel , ExchangeClient , ExchangeServer。

- transport 网络传输层

  抽象 mina 和 netty 为统一接口，以Message 为中心，扩展接口为 Channel , Transporter , Client ,Server , Codec。

- serialize 数据序列化层

  可复用的一些工具，扩展接口为Serialization , ObjectInput , ObjectOutput , ThreadPool。

### 环境搭建

#### 

#### 源码拉取

通过以下命令获取最新的dubbo项目源码

```
1 git clone https://github.com/apache/dubbo.git dubbo
```

![dubbo-2.7.2源码](./assets/image-a0hfuid4bus35o8pjxxxcgd.PNG)

下载源码导入工程后，跳过测试并进行编译。

```java
[ERROR] Failed to execute goal
org.xolstice.maven.plugins:protobuf-maven-
plugin: 0. 5. 1 :compile (default) on project dubbo-
serialization-protobuf: Missing:
[ERROR] - ---------
[ERROR] 1 ) com.google.protobuf:protoc:exe:windows-
x 86 _ 64 : 3. 7. 1
[ERROR]
[ERROR] Try downloading the file manually from the
project website.
[ERROR]
[ERROR] Then, install it using the command:
[ERROR] mvn install:install-file -
DgroupId=com.google.protobuf - DartifactId=protoc -
Dversion= 3. 7. 1 - Dclassifier=windows-x 86 _ 64 -
Dpackaging=exe - Dfile=/path/to/file
[ERROR]
[ERROR] Alternatively, if you host your own
repository you can deploy the file there:
[ERROR] mvn deploy:deploy-file -
DgroupId=com.google.protobuf - DartifactId=protoc -
Dversion= 3. 7. 1 - Dclassifier=windows-x 86 _ 64 -
Dpackaging=exe - Dfile=/path/to/file - Durl=[url] -
DrepositoryId=[id]
[ERROR]
[ERROR] Path to dependency:
[ERROR] 1 ) org.apache.dubbo:dubbo-serialization-
protobuf:jar: 2. 7. 8
[ERROR] 2 ) com.google.protobuf:protoc:exe:windows-
x 86 _ 64 : 3. 7. 1
[ERROR]
[ERROR] - ---------
[ERROR] 1 required artifact is missing.
[ERROR]
[ERROR] for artifact:
[ERROR] org.apache.dubbo:dubbo-serialization-
protobuf:jar: 2. 7. 8
[ERROR]
[ERROR] from the specified remote repositories:
[ERROR] apache.snapshots
(https://repository.apache.org/snapshots,
releases=false, snapshots=true),
[ERROR] alimaven
(http://maven.aliyun.com/nexus/content/groups/public/,
releases=true, snapshots=false)
```
根据报错解决问题：

```java
mvn install:install-file -DgroupId=com.google.protobuf-DartifactId=protoc -Dversion=3.7.1 -Dclassifier=windows-x86_64 -Dpackaging=exe -Dfile=C:\Users\maojun\test\protoc-3.7.1-windows-x86_64.exe
```

#### 源码结构

dubbo源码各个模块的相关作用：

dd![源码结构](./assets/image-ivymjilkeodmg19pyqke383.PNG)

##### 

#### 模块说明

- dubbo-common 公共逻辑模块

  包括 Util 类和通用模型。

- dubbo-remoting 远程通讯模块

  相当于 Dubbo 协议的实现，如果RPC 用 RMI协议则不需要使用此包。

- dubbo-rpc 远程调用模块

  抽象各种协议，以及动态代理，只包含一对一的调用，不关心集群的管理。

- dubbo-cluster 集群模块

  将多个服务提供方伪装为一个提供方，包括：负载均衡, 容错，路由等，集群的地址列表可以是静态配置的，也可以是由注册中心下发。

- dubbo-registry 注册中心模块

  基于注册中心下发地址的集群方式，以及对各种注册中心的抽象。

- dubbo-monitor 监控模块

  统计服务调用次数，调用时间的，调用链跟踪的服务。

- dubbo-config 配置模块

  是 Dubbo 对外的 API，用户通过 Config 使用Dubbo，隐藏 Dubbo 所有细节。

- dubbo-container 容器模块

  是一个 Standlone 的容器，以简单的Main 加载 Spring 启动，因为服务通常不需要 Tomcat/JBoss 等 Web容器的特性，没必要用 Web 容器去加载服务。


#### 环境导入

导入到IDEA中。

#### 测试

1. 安装zookeeper
2. 修改demo案例，配置zookeeper地址
3.  启动服务提供者，启动服务消费者

#### 管理控制台

1. [下载管理控制台](https://github.com/apache/dubbo-admin)
2. 进入源码目录， 进行打包编译

```java
mvn clean package -Dmaven.test.skip=true
```

构建成功提示：

![构建完成](./assets/image-8d61uru53nes6o12oejf5xt.PNG)

3. 启动后台管理服务

```java
1 mvn clean package - Dmaven.test.skip=true
```

![启动服务](./assets/image-exqvt54zjjlud8ngxir9jsl.PNG)

4. 管理后台

地址： http://127.0.0.1:8080/

默认账户名和密码都为root，进入管控台， 可以看到所启动的服务端与消费端。

![管控台](./assets/image-rg710jvtsndtjaeusw132fp.PNG)



### Dubbo实践

#### 与SpringBoot的整合

基于Zookeeper实现Dubbo与Spring Boot的集成整合。

##### 项目POM依赖

```xml
<properties>
<dubbo-version> 2. 7. 8 </dubbo-version>
<spring-boot.version> 2. 3. 0 .RELEASE</spring-
boot.version>
</properties>
<dependencyManagement>
<dependencies>
<!-- Spring Boot - ->
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-
dependencies</artifactId>
<version>${spring-boot.version}</version>
<type>pom</type>
<scope>import</scope>
</dependency>
<!-- Apache Dubbo - ->
<dependency>
<groupId>org.apache.dubbo</groupId>
<artifactId>dubbo-dependencies-
bom</artifactId>
<version>${dubbo-version}</version>
<type>pom</type>
<scope>import</scope>
</dependency>
<dependency>
<groupId>org.apache.dubbo</groupId>
<artifactId>dubbo</artifactId>
<version>${dubbo-version}</version>
<exclusions>
<exclusion>
<groupId>org.springframework</groupId>
<artifactId>spring</artifactId>
</exclusion>
<exclusion>
<groupId>javax.servlet</groupId>
<artifactId>servlet-
api</artifactId>
</exclusion>
<exclusion>
<groupId>log 4 j</groupId>
<artifactId>log 4 j</artifactId>
</exclusion>
</exclusions>
</dependency>
</dependencies>
</dependencyManagement>
<dependencies>
<!-- Dubbo Spring Boot Starter - ->
<dependency>
<groupId>org.apache.dubbo</groupId>
<artifactId>dubbo-spring-boot-
starter</artifactId>
<version>${dubbo-version}</version>
</dependency>
<!-- Dubbo核心组件 - ->
<dependency>
<groupId>org.apache.dubbo</groupId>
<artifactId>dubbo</artifactId>
</dependency>
<!--Spring Boot 依赖 - ->
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-
web</artifactId>
<version>${spring-boot.version}</version>
</dependency>
<!-- Zookeeper客户端框架 - ->
<dependency>
<groupId>org.apache.curator</groupId>
<artifactId>curator-framework</artifactId>
<version> 4. 0. 1 </version>
</dependency>
<!-- Zookeeper dependencies - ->
<dependency>
<groupId>org.apache.dubbo</groupId>
<artifactId>dubbo-dependencies-
zookeeper</artifactId>
<version>${dubbo-version}</version>
<type>pom</type>
<exclusions>
<exclusion>
<groupId>org.slf 4 j</groupId>
<artifactId>slf 4 j-log 4 j 12 </artifactId>
</exclusion>
</exclusions>
</dependency>
</dependencies>
```
Dubbo采用2.7.8版本， Spring Boot采用的是2.3.0.RELEASE版本。

如果依赖下载出现问题， 可以指定具体的仓库：

```xml
<repositories>
<repository>
<id>apache.snapshots.https</id>
<name>Apache Development Snapshot
Repository</name>
 <url>https://repository.apache.org/content/repositories/s
napshots</url>
<releases>
<enabled>false</enabled>
</releases>
<snapshots>
<enabled>true</enabled>
</snapshots>
</repository>
</repositories>
```



#### 

##### 公用RPC接口工程

为便于客户端与服务端的RPC接口引用， 定义了一个订单服务接口， 用于测试验证。

```java
public interface OrderService {
/**
* 获取订单详情
* @param orderId
* @return
*/
String getOrder(Long orderId);
}
```



##### 生产者POM依赖

```xml
<dependencies>
<!-- Dubbo Spring Boot Starter - ->
<dependency>
<groupId>org.apache.dubbo</groupId>
<artifactId>dubbo-spring-boot-
starter</artifactId>
<version>${dubbo-version}</version>
</dependency>
<!-- Dubbo 核心依赖 - ->
<dependency>
<groupId>org.apache.dubbo</groupId>
<artifactId>dubbo</artifactId>
</dependency>
<!-- 公用RPC接口依赖 - ->
<dependency>
<groupId>xyz.maojun</groupId>
<artifactId>dubbo-spring-
interface</artifactId>
<version>${project.version}</version>
</dependency>
</dependencies>
```

###### RPC服务接口

```
@DubboService(version =
"${dubbo.spring.provider.version}")
public class OrderServiceImpl implements
OrderService {
/**
* 服务端口
*/
@Value("${server.port}")
private String serverPort;
@Value("${dubbo.spring.provider.version}")
private String serviceVersion;
/**
* 获取订单详情
* @param orderId
* @return
*/
public String getOrder(Long orderId) {
String result = "get order detail
,orderId="+orderId +",serverPort="+serverPort
+",serviceVersion="+serviceVersion;
System.out.println(result);
return result;
}
}
```
通过DubboService注解， 声明为RPC服务，version可以标识具体的版本号， 消费端需匹配保持一致。

###### 工程配置

```yaml
server.port= 18081
# 应用程序名称
spring.application.name=dubbo-spring-provider
# Dubbo服务扫描路径
dubbo.scan.base-packages=xyz.maojun
# Dubbo 通讯协议
dubbo.protocol.name=dubbo
# Dubbo服务提供的端口， 配置为- 1 ，代表为随机端口 默认
20880
dubbo.protocol.port=- 1
## Dubbo 注册器配置信息
dubbo.registry.address=zookeeper:// 127. 0. 0. 1 : 2181
dubbo.registry.file = ${user.home}/dubbo-
cache/${spring.application.name}/dubbo.cache
dubbo.spring.provider.version = 1. 0. 0
```

###### Spring Boot启动程序

```java
# 服务端口
@SpringBootApplication
@ComponentScan(basePackages = {"xyz.maojun"})
public class DubboSpringProviderApplication {
public static void main(String[] args) {
SpringApplication.run(DubboSpringProviderApplicatio
n.class, args);
}
}
```
#### 

##### 消费者POM依赖

```xml
<dependencies>
<!-- Dubbo Spring Boot Starter - ->
<dependency>
<groupId>org.apache.dubbo</groupId>
<artifactId>dubbo-spring-boot-
starter</artifactId>
<version>${dubbo-version}</version>
</dependency>
<!-- 公用RPC接口依赖 - ->
<dependency>
<groupId>xyz.maojun</groupId>
<artifactId>dubbo-spring-
interface</artifactId>
<version>${project.version}</version>
</dependency>
</dependencies>
```
######  消费端调用

```java
@Controller
@RequestMapping("/order")
public class OrderController {
private final Logger logger =
LoggerFactory.getLogger(getClass());

/**
* 订单服务接口
*/
@DubboReference(version ="${dubbo.spring.provider.version}")
private OrderService orderService;

 /**
* 获取订单详情接口
* @param orderId
* @return
*/
@RequestMapping("/getOrder")
@ResponseBody
public String getOrder(Long orderId) {
String result = null;
try {
result =
orderService.getOrder(orderId);
}catch(Exception e) {
logger.error(e.getMessage(), e);
}
return result;
}
```
##### 


###### 工程配置

```properties
# 服务端口
server.port= 18084
#服务名称
spring.application.name=dubbo-spring-consumer
#服务版本号
dubbo.spring.provider.version = 1. 0. 0
#消费端注册器配置信息
dubbo.registry.address=zookeeper:// 127. 0. 0. 1 : 2181
dubbo.registry.file = ${user.home}/dubbo-
cache/${spring.application.name}/dubbo.cache
```
###### Spring Boot启动程序

```java
@SpringBootApplication
@ComponentScan(basePackages = {"xyz.maojun"})
public class DubboSpringConsumerApplication {
    public static void main(String[] args) {
        SpringApplication.run(DubboSpringConsumerApplicatio
n.class, args);
}
}
```



##### 工程调用验证

1.  启动ZK注册中心
2. 启动服务端， 运行DubboSpringProviderApplication
3. 启动消费端， 运行DubboSpringConsumerApplication
4. 请求获取订单接口， 地址： http://127.0.0.1:18084/order/getOrder?orderId=1001

调用成功：

![调用成功](./assets/image-udh8pihyio84whkpyqspo5b.PNG)


### Dubbo高阶配置

#### 覆盖关系

官方文档： https://dubbo.apache.org/zh/docsv2.7/user/configuration/

1.  通过ProviderConfig配置全局超时（可通过yml配置覆盖）

2.  在@DubboService注解上配置接口超时

3.  在@DubboService注解上配置接口方法超时

4.  在消费方进行配置，查看覆盖情况


#### 覆盖规则

![覆盖规则](./assets/image-03ngyrfq45zdgvrrpsbwuty.PNG)

####  配置规则

1. 方法级优先;
2. 接口级次之;
3. 全局配置再次之。
4. 如果级别一样，则消费者优先，生产者次之。

#### 

#### 服务端超时

增加配置类：

```java
@Configuration
public class DubboCustomConfig {
/**
* 服务端
* @return
*/
@Bean
public ProviderConfig registryConfig() {
ProviderConfig providerConfig = new
ProviderConfig();
// 设定超时时间为 5 S
providerConfig.setTimeout( 5000 );
return providerConfig;
}
}
```
修改服务接口实现：

```java
/**
* 获取订单详情
* @param orderId
* @return
*/
public String getOrder(Long orderId) {
try {
// 休眠1.5秒
Thread.sleep(1500L);
} catch (InterruptedException e) {
e.printStackTrace();
}
return "Get Order Detail, Id: " + orderId +
", serverPort: " + serverPort;
    }
```

客户端调用验证

请求地址： http://127.0.0.1:18084/order/getOrder?orderId=123

 服务端全局超时设为 2 秒（不触发超时）， 消费端设定超时时间为 1 秒（触发超时）。

 修改调用代码：

```java
/**
* 订单服务接口
*/
@DubboReference(version =
"${dubbo.spring.provider.version}", timeout =
1000)
private OrderService orderService;
```
调用结果， 触发超时：

![验证超时](./assets/image-6deg835f0x11eo2w85t796f.PNG)

表明消费端配置优先。

服务端全局超时设定为1秒（触发超时）， 消费端去掉超时时间配置。触发超时， 表明服务提供方优先级次之。

![验证超时](./assets/image-z8w65atrb412ff8vk8a9pcs.PNG)





#### 属性配置优先级

优先级规则

![优先级规则](./assets/image-w23jrl0ykmrf8wpqf7xv4ok.PNG)

优先级从高到低：

- JVM -D 参数；

```java
-Ddubbo.protocol.port=20881 1
```

- XML（application.yml/application.properties）配置会重写dubbo.properties 中的配置。

```yaml
dubbo:
protocol:
name: dubbo # 通讯协议
#port: 20880 # dubbo服务提供方的端口，该值
是默认值
port: 20882
```

- Properties默认配置（dubbo.properties），仅仅作用于以上两者没有配置时，一般配置全局公共配置。

```properties
dubbo.protocol.port=20883
```

#### 

### 重试与容错处理机制

#### 容错机制

- Failfast Cluster

快速失败，只发起一次调用，失败立即报错。通常用于非幂等性的写操作，比如新增记录。

- Failsafe Cluster

失败安全，出现异常时，直接忽略。通常用于写入审计日志等操作。

- Failback Cluster

失败自动恢复，后台记录失败请求，定时重发。通常用于消息通知操作。

- Forking Cluster

并行调用多个服务器，只要一个成功即返回。通常用于实时性要求较高的读操作，但需要浪费更多服务资源。可通过forks="2" 来设置最大并行数。

- Broadcast Cluster

广播调用所有提供者，逐个调用，任意一台报错则报错。通常用于通知所有提供者更新缓存或日志等本地资源信息。



调整客户端重试次数,重试次数设定为3次。

```java
/**
 * 订单服务接口
 */
 @DubboReference(version =
"${dubbo.spring.provider.version}",retries = 3)
 private OrderService orderService;
```

 修改服务端超时时间模拟超时， 让客户端触发重试。

```java
 /**
 * 服务端全局配置
 * @return
 */
 @Bean
 public ProviderConfig registryConfig() {
 ProviderConfig providerConfig = new
ProviderConfig();
 providerConfig.setTimeout(1000);
 return providerConfig;
 }
```

客户端调用（单个服务）

http://127.0.0.1:18084/order/getOrder?orderId=123

查看服务端控制台，可以看到触发重试机制，加上第一次的调用和3次重试， 共4次。

![超时重试](./assets/image-k7kwuhcckwpijc5yalcee86.PNG)

允许多个实例运行， 开启多个服务，再次访问。

![集群重试](./assets/image-4ueexpcob5sw60ixk4z137o.PNG)

第一个服务实例，访问两次， 其他每个服务访问一次。

### 多版本控制

启动三个服务端。第一个服务端版本为1.0.0， 第二、三个服务端版本分别为：2.0.0 和3.0.0
主要是修改application.properties配置：

```properties
#服务版本号
dubbo.spring.provider.version = 2.0.0
```

启动三个服务提供者，通过 -Ddubbo.spring.provider.version =2.0.0 -Dserver.port=18082 来启动三个相关的端口不能重复。

 消费端指定版本号。同样修改application.properties配置：

```properties
#服务版本号
dubbo.spring.provider.version = 2.0.0
```

仍然通过 -Ddubbo.spring.provider.version = 2.0.0 来调用不同版本的服务测试时，注释掉超时的代码

仍是采用超时配置， 通过重试测试验证测试验证结果,请求只会访问至版本号为2.0.0的服务节点上面。

### 本地存根调用

![实现流程](./assets/image-bydm1ddiojjppwtu69hzuy0.PNG)

把 Stub 暴露给用户，Stub 可以决定要不要去调 Proxy。



 客户端存根实现，增加service接口。

```java
public class OrderServiceStub implements
OrderService {
private final OrderService orderService;
// 构造函数传入真正的远程代理对象
public OrderServiceStub(OrderService
orderService){
this.orderService = orderService;
    }
/**
* 获取订单详情
* @param orderId
* @return
*/
public String getOrder(Long orderId){
// 在客户端执行, 预先验证参数是否合法，等等
try {
if(null != orderId) {
return
orderService.getOrder(orderId);
}
return "参数校验错误！";
} catch (Exception e) {
//容错处理
return "出现错误：" + e.getMessage();
}
}
}
```

修改客户端调用配置

```java
/**
* 订单服务接口
*/
@DubboReference(version =
"${dubbo.spring.provider.version}",retries = 3, stub
=
"com.itheima.dubbo.spring.consumer.service.OrderServ
iceStub")
private OrderService orderService;
```

stub要配置存根接口的完整路径。

访问不带参数的地址： http://127.0.0.1:18084/order/getOrder

![存根调用](./assets/image-efmckq9g1rrri3d9y2yapql.PNG)

会进入存根接口的处理逻辑， 提示校验异常。





### 负载均衡机制

默认负载策略

Dubbo默认采用的是随机负载策略。

开启三个服务节点，通过消费端访问验证： http://127.0.0.1:18084/order/getOrder?orderId=123

通过控制后台日志输出， 可以看到每个服务节点呈现不规则的调用。

Dubbo 支持的负载均衡策略，可用参看源码： AbstractLoadBalance

- Random LoadBalance：默认

随机，按权重设置随机概率。

在一个截面上碰撞的概率高，但调用量越大分布越均匀，而且按概率使用权重后也比较均匀，有利于动态调整提供者权重。

- RoundRobin LoadBalance

加权轮询负载均衡，按公约后的权重设置轮询比率。存在慢的提供者累积请求的问题，比如：第二台机器很慢，但没挂，当请求调到第二台时就卡在那，久而久之，所有请求都卡在调到第二台上。

- LeastActive LoadBalance

最少活跃调用数，相同活跃数的随机，活跃数指调用前后计数差。活跃数其实就是在当前这个服务调用者中当前这个时刻 某个invoker（某个服务提供者的某个接口）某个方法的调用并发数，在调用之前+1 调用之后-1的一个计数器，如果出现多个活跃数相等invoker的时候使用随机算法来选取一个

- ConsistentHash LoadBalance

一致性 Hash，相同参数的请求总是发到同一提供者。当某一台提供者挂时，原本发往该提供者的请求，基于虚拟节点，平摊到其它提供者，不会引起剧烈变动。



> 一致性Hash负载均衡涉及到两个主要的配置参数为
>
> **hash.arguments** 与**hash.nodes**。
>
> 1. **hash.arguments** ： 当进行调用时候根据调用方法的哪几个参数生成key，并根据key来通过一致性hash算法来选择调用结点。
> 2. **hash.nodes**： 为结点的副本数



- ShortestResponseLoadBalance

最短响应时间负载均衡
从多个服务提供者中选择出调用成功的且响应时间最短的服务提供者，由于满足这样条件的服务提供者有可能有多个。所以当选择出多个服务提供者后要根据他们的权重做分析，如果权重一样，则随机。



源码实现：

org.apache.dubbo.rpc.cluster.loadbalance.AbstractLoadBalance

 Dubbo提供了四种实现：

![四种实现](./assets/image-wog3j8ay7sm7xrsj6smyk16.PNG)



四种配置方式：

优先级从下至上：

- 服务端的服务级别

```java
<dubbo:service interface="..."
loadbalance="roundrobin" />
```

- 客户端的服务级别

```java
<dubbo:reference interface="..."
loadbalance="roundrobin" />
```

> 注意：服务提供者配置后最终也只是同步到消费者端。故一般在消费端配置

- 服务端方法级别

```java
<dubbo:service interface="...">
<dubbo:method name="..."
loadbalance="roundrobin"/>
</dubbo:service>
```

- 客户端方法级别

```java
<dubbo:reference interface="...">
 <dubbo:method name="..."
loadbalance="roundrobin"/>
 </dubbo:reference>
```

调用验证

修改客户端的负载策略， 改为轮询策略：

> 注意很多配置都有三个级别，针对方法的，针对接口的，全局的。

```java
@DubboReference(version =
"${dubbo.spring.provider.version}",retries = 3,
2 loadbalance = "roundrobin",
3 stub =
"xyz.maojun.dubbo.spring.consumer.service.OrderServiceStub")
```

开启三个服务节点， 进行访问验证： http://127.0.0.1:18084/order/g

etOrder?orderId=123

会依次轮询进行调用。

 注意：测试时将服务提供者的版本号都调成一致（1.0.0），超时配置注

释掉！

动态权重调整验证

管理后台地址： http://127.0.0.1:8080/#

通过管理后台修改服务的权重配置：

![管理后台配置权重](./assets/image-uge7f3cfqhp7omwtdzrau8r.PNG)

将两台节点的权重降低至20：

![权重规则](./assets/image-0nainutsgxbd1b21ysg0tcm.PNG)

调整后可以看到权重配置已经生效：

![查看权重](./assets/image-xu4fu6stfeanc3e0eynsydb.PNG)

通过消费者接口访问， 会发现第一台权重较大的节点， 访问次数会明显增多。



### 服务降级运用

服务动态禁用

启动单个服务节点，进入Dubbo Admin， 创建动态配置规则：

```yaml
configVersion: v2.7
enabled: true
configs:
- side: provider
addresses:
- '0.0.0.0:20880'
parameters:
timeout: 3000
disabled: true
```

将disabled属性设为true， 服务禁用， 可以错误提示

![服务禁用](./assets/image-ap7lali9xsuwd8v8iayp9ns.PNG)

将disabled属性改为false，则恢复正常访问。

![服务启用](./assets/image-mu14jewsnvpux223sq7hgxm.PNG)

 服务降级，配置规则。

```java
RegistryFactory registryFactory =ExtensionLoader.getExtensionLoader(RegistryFactory.class).getAdaptiveExtension();
Registry registry =registryFactory.getRegistry(URL.valueOf("zookeeper://10.20.153.10:2181"));
registry.register(URL.valueOf("override://0.0.0.0/com.foo.BarService?category=configurators&dynamic=false&application=foo&mock=force:return+null"));
```

mock=force:return+null 表示消费方对该服务的方法调用都直接返回 null 值，不发起远程调用。用来屏蔽不重要服务不可用时对调用方的影响。

还可以改为 mock=fail:return+null 表示消费方对该服务的方法调用在失败后，再返回 null值，不抛异常。用来容忍不重要服务不稳定时对调用方的影响。



降级测试（force方式）进入Dubbo Admin进行配置：



```yaml
configVersion: v2.7
enabled: true
configs:
- side: consumer
addresses:
- 0.0.0.0
parameters:
timeout: 3000
mock: 'force:return null'
```

客户端调用， 会直接屏蔽， 并且服务端控制台不会有任何调用记录：

![返回null](./assets/image-rxl2doctkav830fp84p54ai.PNG)

降级测试（fail方式）进入Dubbo Admin配置：

```yaml
configVersion: v2.7
enabled: true
configs:
- side: consumer
addresses:
- 0.0.0.0
parameters:
timeout: 1000
mock: 'fail:return null'
```

这里为了触发调用异常， 超时时间缩短为1秒。

注意这里的超时时间可能不会起作用，最终的超时时间还是得看项目中配置，故在服务提供方将线程休眠时间延长，造成调用超时。客户端调用， 会出现降级显示为空,同时服务端会有调用记录显示（请求会进入服务端，但由于超时， 调用是失败）：

![调用失败](./assets/image-i59ctmpgtl1warig85mwupa.PNG)



### 并发与连接控制

实际运用， 会碰到高并发与峰值场景， Dubbo是可以做到并发与连接数控制。可使用jmeter进行测试。

**并发数控制**

1. 服务端控制

   服务级别

   ```xml
   <dubbo:service interface="com.foo.BarService" executes="10" />
   ```

   服务器端并发执行（或占用线程池线程数）不能超过 10 个。

   方法级别

   ```xml
   <dubbo:service
   interface="com.foo.BarService">
    <dubbo:method name="sayHello"
   executes="3" />
   </dubbo:service>
   ```

   限制具体的方法，服务器端并发执行（或占用线程池线程数）

   不能超过3 个。

2. 客户端控制

调用的服务控制

```xml
<dubbo:reference
interface="com.foo.BarService" actives="10"
/>
```

每客户端并发执行（或占用连接的请求数）不能超过 10 个。

调用的服务方法控制

```xml
<dubbo:reference
interface="com.foo.BarService">
2 <dubbo:method name="sayHello"
actives="10" />
3 </dubbo:service>
```

> 注意：dubbo:reference比dubbo:service优先。

3. 客户端负载配置

```xml
<dubbo:reference interface="com.foo.BarService"
loadbalance="leastactive" />
```

负载策略为最小连接数时， Loadbalance 会调用并发数最小的Provider。



连接数控制

1. 服务端连接控制

```xml
<dubbo:provider protocol="dubbo" accepts="10" />
```

限制服务器端接受的连接不能超过 10 个

2. 客户端连接控制

```xml
<dubbo:reference interface="com.foo.BarService"
connections="10" />
```

限制客户端服务使用连接不能超过 10 个

> 注意：如果 dubbo:service 和 dubbo:reference 都配了 connections，dubbo:reference 优先。



（完）
