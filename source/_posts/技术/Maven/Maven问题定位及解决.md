---
title: Maven问题定位及解决
date: 2023/11/04 09:40
updated: 2023/11/04 09:40
categories:
- [技术, Maven]
tags:
- Maven
- Java
---



## Since Maven 3.8.1 http repositories are blocked

在使用Idea时，已经默认带了Maven插件，所以无需单独安装Maven即能使用。

进入Idea的Maven插件目录：

```vbnet
C:\Program Files\JetBrains\IntelliJ IDEA Community Edition 2023.1\plugins\maven\lib\maven3\conf
```

编辑setting.xml文件，并注释以下内容：

```xml
	  <!-- 
    <mirror>
      <id>maven-default-http-blocker</id>
      <mirrorOf>external:http:*</mirrorOf>
      <name>Pseudo repository to mirror external repositories initially using HTTP.</name>
      <url>http://0.0.0.0/</url>
      <blocked>true</blocked>
    </mirror>
	 -->
```

保存后，在Idea刷新并下载Maven依赖不再报错。

（完）