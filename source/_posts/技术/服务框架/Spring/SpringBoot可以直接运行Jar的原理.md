---
title: SpringBoot可以直接运行Jar的原理
date: 2023/11/05 13:26
updated: 2023/11/05 13:26
categories:
- [技术, 服务框架]
tags:
- SpringBoot
- Jar
---

## SpringBoot可以直接运行Jar的原理

Spring Boot的Jar包可以直接运行，主要是因为它内置了嵌入式的Servlet容器（例如Tomcat、Jetty或Undertow），并且使用了可执行的JAR文件格式，这使得应用程序可以作为独立的Java进程运行。

那么Spring Boot如何创建可执行的Jar文件以及如何启动应用程序：

1. Spring Boot Maven插件：`spring-boot-maven-plugin`是用于将Spring Boot应用程序打包成可执行的Jar文件的插件。它允许你执行`mvn clean install` 或 `mvn package` 命令来创建一个包含应用程序代码和依赖项的Jar文件。

2. Fat Jar：生成的Jar文件通常被称为"Fat Jar"，因为它包含了应用程序的代码和所有依赖的Jar文件。这使得Jar文件可以独立运行，无需额外的类路径配置。

3. Manifest文件：在生成的Fat Jar中，通常包含一个特殊的Manifest文件，其中包含了应用程序的启动类信息。这是因为Java命令需要知道应用程序的入口点，而Manifest文件指定了启动类。

4. JarLauncher：Spring Boot的Fat Jar中通常包含了一个名为`JarLauncher`的类，它负责创建一个`LaunchedURLClassLoader`来加载应用程序的依赖Jar，然后以一个新线程启动应用程序的Main函数。这个过程使得应用程序可以在独立的Java进程中运行。

（本文完）