---
title: OpenFeign简介和入门
date: 2020/04/02 23:21:30
updated: 2020/04/02 23:21:30
categories:
- [技术, OpenFeign]
tags:
- OpenFeign
- 微服务
---

## OpenFeign前身

- Feign是Spring Cloud组件中的一个轻量级RESTful的HTTP服务客户端。
- Feign内置了Ribbon，用来做客户端负载均衡，去调用服务注册中心的服务。
- Feign的使用方式是：使用Feign的注解定义接口，调用这个接口，就可以调用服务注册中心的服务。
- Feign支持的注解和用法请参考官方文档：https://github.com/OpenFeign/feign
- Feign本身不支持Spring MVC的注解，它有一套自己的注解。



## OpenFeign介绍

OpenFeign是Spring Cloud 在Feign的基础上支持了Spring MVC的注解，如@RequesMapping等，是一

个轻量级的Http封装工具对象,大大简化了Http请求，使得我们对服务的调用转换成了对本地接口方法的

调用。

OpenFeign 的 @FeignClient 可以解析SpringMVC的 @RequestMapping 注解下的接口，并通过动态代

理的方式产生实现类，实现类中做负载均衡并调用其他服务。

- 集成了Ribbon的负载均衡功能
- 集成Hystrix的熔断器功能
- 支持请求压缩
- 大大简化了远程调用的代码，同时功能还增强啦
- 以更加优雅的方式编写远程调用代码，并简化重复代码



![业务分析](./assets/image-ptms6zvcztuu9ajffq5ht16.PNG)



**需求：**

如上图，我们现在要实现打车用户打车下单，打车下单的时候需要匹配指定司机并更改司机状态，由之

前空闲状态改成接单状态。

**技术支持：**

这时候就涉及到 hailtaxi-order 服务调用 hailtaxi-driver 服务了，此时如果使用HttpClient工具，

操作起来非常麻烦，我们可以使用 SpringCloud OpenFeign 实现调用。



## OpenFeign实践

### 启动项目

使用OpenFeign实现服务之间调用，可以按照如下步骤实现：

1. 导入openfeign依赖
2. 编写openfeign客户端接口-将请求地址写到该接口上
3. 消费者启动引导类开启openfeign功能注解
4. 访问接口测试



1. 导入依赖

```xml
<!--配置feign-->
<dependency>
<groupId>org.springframework.cloud</groupId>
<artifactId>spring-cloud-starter-openfeign</artifactId>
<version>2.2.1.RELEASE</version>
</dependency
```



2. 创建Feign客户端接口

修改 hailtaxi-api 创建 xyz.maojun.driver.feign.DriverFeign 接口，代码如下：

```java
@FeignClient(value = "hailtaxi-driver")//value = "hailtaxi-driver"指定服务的名字
public interface DriverFeign {
/****
* 更新司机信息，该方法和hailtaxi-driver服务中的方法保持一致
*/
@PutMapping(value = "/driver/status/{id}/{status}")
Driver status(@PathVariable(value = "id")String id, @PathVariable(value =
"status")Integer status);
}
```

Feign会通过动态代理，帮我们生成实现类。

- 注解@FeignClient声明Feign的客户端，注解value指明服务名称
- 接口定义的方法，采用SpringMVC的注解。Feign会根据注解帮我们生成URL地址
- 注解@RequestMapping中的/driver，不要忘记。因为Feign需要拼接可访问地址



3. Controller调用

修改 hailtaix-order 的下单方法，在下单方法中调用 DriverFeign 修改司机状态，代码如下：

```java
@Autowired
private DriverFeign driverFeign;
/***
* 下单
*/
@PostMapping
public OrderInfo add(){
//修改司机信息 司机ID=1
Driver driver = driverFeign.status("1",2);
//创建订单
OrderInfo orderInfo = new OrderInfo("No"+((int)(Math.random()*10000)), (int)
(Math.random()*100), new Date(), "深圳北站", "罗湖港", driver);
orderInfoService.add(orderInfo);
return orderInfo;
}
```



4. 启用OpenFeign

上面所有业务逻辑写完了，但OpenFeign还并未生效，我们需要在 hailtaxi-order 中开启OpenFeign ，只需要在 OrderApplication 启动类上添加 @EnableFeignClients(basePackages =
"xyz.maojun.driver.feign") 即可。

测试地址：http://localhost:18082/order

![请求访问](./assets/image-macazyaszgdh6y4zfe5ig3d.PNG)

数据库查询验证：

![数据库验证](./assets/image-sertor17n2m3fkkankwo45k.PNG)

### 日志配置

为了获得服务请求的参数和返回值，我们经常使用的一个做法就是打印日志。你可以在程序中使用 log.info 或者log.debug 方法将服务请求的入参和返回值一一打印出来。如果远程调用组件能自己实现调用参数的日志打印，这个事情不就完美解决了么！下面来看怎么做。

1. 首先，你需要在配置文件中指定 FeignClient 接口的日志级别为 Debug。这样做是因为OpenFeign 组件默认将日志信息以 debug 模式输出，而默认情况下 Spring Boot 的日志级别是Info，因此我们必须将应用日志的打印级别改为 debug 后才能看到 OpenFeign 的日志。
2. 在应用的上下文中使用代码的方式声明 Feign 组件的日志级别。这里的日志级别并不是我们传统意义上的 Log Level，它是 OpenFeign 组件自定义的一种日志级别，用来控制 OpenFeign 组件向日志中写入什么内容

> 因为@FeignClient注解修饰的客户端在被代理时，都会创建一个新的Feign.Logger实例。我们需要额外通过配置类的方式指定这个日志的级别才可以。

#### 实现步骤

1. 在application.yml配置文件中开启日志级别配置

2. 编写配置类，定义日志级别bean。

3. 在接口的@FeignClient中指定配置类

4. 重启项目，测试访问

   

1. 普通日志等级配置

   在 hailtaxi-order 的配置文件中设置包下的日志级别都为debug：\

   ```yaml
   logging:
   	level:
   		xyz.maojun: debug
   ```

   

2. Feign日志等级配置

在 hailtaxi-order 启动类 OrderApplication 中创建 Logger.Level ,定义日志级别：

```java
/***
* 日志级别
* @return
*/
@Bean
public Logger.Level feignLoggerLevel(){
return Logger.Level.FULL;
}
```

Feign支持4种级别：

1. NONE：不记录任何日志，默认值

2. BASIC：只记录服务请求的 URL、HTTP Method、响应状态码（如 200、404 等）和服务调用的执行

   时间

3. HEADERS：在 BASIC 的基础上，还记录了请求和响应中的 HTTP Headers

4. FULL：在 HEADERS 级别的基础上，还记录了服务请求和服务响应中的 Body 和 metadata，FULL

   级别记录了最完整的调用信息。

重启项目，即可看到每次访问的日志。



### 超时判定

超时判定是一种保障可用性的手段。如果你要调用的目标服务的 RT（Response Time）值非常高，那么

你的调用请求也会处于一个长时间挂起的状态，这是造成服务雪崩的一个重要因素。为了隔离下游接口

调用超时所带来的的影响，我们可以在程序中设置一个超时判定的阈值，一旦下游接口的响应时间超过

了这个阈值，那么程序会自动取消此次调用并返回一个异常。

超时配置如下

```yaml
feign:
client:
config:
#全局默认配置
default:
#网络连接阶段1秒超时 单位毫秒
connectTimeout: 1000
#服务请求响应阶段5秒超时
readTimeout: 5000
#针对某个特定服务的超时配置
hailtaxi-driver:
connectTimeout: 1000
readTimeout: 2000
```

### 服务降级

降级逻辑是在远程服务调用发生超时或者异常（比如 400、500 Error Code）的时候，自动执行的一段

业务逻辑。你可以根据具体的业务需要编写降级逻辑，比如执行一段兜底逻辑将服务请求从失败状态中

恢复，或者发送一个失败通知到相关团队提醒它们来线上排查问题。

OpenFeign 支持两种不同的方式来指定降级逻辑，一种是定义 fallback 类，另一种是定义 fallback 工

厂。

譬如这里使用 fallback 工厂，编写xyz.maojun.driver.feign.fallback.DriverFeignFallBackFactory

```java
@Component
public class DriverFeignFallBackFactory implements FallbackFactory<DriverFeign>
{
@Override
public DriverFeign create(Throwable cause) {
return new DriverFeign() {
@Override
public Driver status(String id, Integer status) {
System.out.println("走降级");
return new Driver();
}
};
}
}
```

在feign接口上指定

```java
@FeignClient(
name = "hailtaxi-driver"
,fallbackFactory = DriverFeignFallBackFactory.class
)
```

opfeign默认集成了hystrix，但未开启，需要开启，降级才生效。

```yaml
feign:
	hystrix:
		enabled: true
```



### 数据压缩

用户在网络请求过程中，如果网络不佳、传输数据过大，会造成体验差的问题，我们需要将传输数据压缩提升体验。SpringCloud OpenFeign支持对请求和响应进行GZIP压缩，以减少通信过程中的性能损耗。

通过配置开启请求与响应的压缩功能，在openfeign的客户端配置，即 hailtaxi-order 中

```yaml
feign:
compression:
request:
enabled: true # 开启请求压缩
response:
enabled: true # 开启响应压缩
```

也可以对请求的数据类型，以及触发压缩的大小下限进行设置

```yaml
feign:
compression:
request:
enabled: true # 开启请求压缩
mime-types: text/html,application/xml,application/json # 设置压缩的数据类型
min-request-size: 2048 # 设置触发压缩的大小下限
#以上数据类型，压缩大小下限均为默认值
response:
enabled: true # 开启响应压缩
```



### 拦截器

用 Feign 来调用远程服务，比如远程服务的权限验证，需要在 header 中传递 token 之类的。在方法中显式传递又过于麻烦了，这时候就可以考虑使用 Feign 提供的 RequestInterceptor 接口，只要实现了该接口，那么 Feign 每次做远程调用之前都可以被它拦截下来再进行包装。

1. 在 hailtaxi-api 中创建拦截器

```java
/**
* 1、直接实现接口
* 2、继承 BaseRequestInterceptor
*/
@Slf4j
public class MyRequestInterceptor implements RequestInterceptor {
@Override
public void apply(RequestTemplate template) {
String url = template.url();
Map<String, Collection<String>> headers = template.headers();
String method = template.method();
Map<String, Collection<String>> queries = template.queries();
    Request.Body body = template.requestBody();
log.info("url={},headers={},method={},queries={},body=
{}",url,headers,method,queries,body);
//添加头信息
template.header("GlobalId", UUID.randomUUID().toString());
}
}
```

2. 在 hailtaxi-order 中创建配置类，加入容器即可

```java
@Configuration
public class InterceptorConfiguration {
@Bean
public RequestInterceptor interceptor() {
return new MyRequestInterceptor();
}
}
```


(完)
