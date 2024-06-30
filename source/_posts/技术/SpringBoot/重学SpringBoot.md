---
title: 重学SpringBoot
date: 2016/03/03 14:32:37
updated: 2016/03/03 14:32:37
categories:
- [技术, SpringBoot]
tags:
- SpringBoot
---



## Spring Boot、Spring MVC 和 Spring 有什么区别

- spring是一个IOC容器，用来管理Bean，使用依赖注入实现控制反转，可以很方便的整合各种框架，提供AOP机制弥补OOP的代码重复问题、更方便将不同类不同方法中的共同处理抽取成切面、自动注入给方法执行，比如日志、异常等
- springmvc是spring对web框架的一个解决方案，提供了一个总的前端控制器Servlet，用来接收请求，然后定义了一套路由策略（url到handle的映射）及适配执行handle，将handle结果使用视图解析技术生成视图展现给前端
- springboot是spring提供的一个快速开发工具包，让程序员能更方便、更快速的开发spring+springmvc应用，简化了配置（约定了默认配置），整合了一系列的解决方案（starter机制）、redis、mongodb、es，可以开箱即用



## Spring Boot 自动配置原理

@Import + @Configuration + Spring spi

自动配置类由各个starter提供，使用@Configuration + @Bean定义配置类，放到METAINF/spring.factories下
使用Spring spi扫描META-INF/spring.factories下的配置类
使用@Import导入自动配置类



![Spring Boot 自动配置](./assets/image-20240419064056818.png)



## 如何理解 Spring Boot 中的 Starter

使用spring + springmvc使用，如果需要引入mybatis等框架，需要到xml中定义mybatis需要的bean

starter就是定义一个starter的jar包，写一个@Configuration配置类、将这些bean定义在里面，然后在
starter包的META-INF/spring.factories中写入该配置类，springboot会按照约定来加载该配置类

开发人员只需要将相应的starter包依赖进应用，进行相应的属性配置（使用默认配置时，不需要配
置），就可以直接进行代码开发，使用对应的功能了，比如mybatis-spring-boot--starter，springboot-starter-redis



## 嵌入式servlet服务器

节省了下载安装tomcat，应用也不需要再打war包，然后放到webapp目录下再运行。
只需要一个安装了 Java 的虚拟机，就可以直接在上面部署应用程序了
springboot已经内置了tomcat.jar，运行main方法时会去启动tomcat，并利用tomcat的spi机制加载
springmvc



