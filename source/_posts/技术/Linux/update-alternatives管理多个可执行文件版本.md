---
title: 使用update-alternatives管理多个可执行文件版本
date: 2021/04/09 23:46:41
updated: 2021/04/09 23:46:41
categories:
- [技术, Linux]
tags:
- Java
- Linux
---



## 管理Java版本

要使用 `update-alternatives` 添加 Java，需要先确保已经安装了多个 Java 版本，并且这些版本位于不同的路径下。

`update-alternatives` 允许你设置一个默认的 Java 版本，并在多个版本之间切换。

### 安装

确保多个 Java 版本安装在不同的路径下。使用update-alternatives添加 Java 版本。

```
sudo update-alternatives --install /usr/bin/java java <path-to-java-version> <priority>
```

> 备注:
>
> `/usr/bin/java` 是通用的 Java 命令路径。
>
> `java` 是这个命令的替代名称。
>
> `<path-to-java-version>` Java 版本的完整路径，例如 `/usr/lib/jvm/java-11-openjdk-amd64/bin/java`。
>
> `<priority>` 是一个数字，表示这个版本的优先级。数字越大，优先级越高。

### 配置

要设置某个版本为默认版本，可以使用以下命令：

```
sudo update-alternatives --config java
```

这会列出所有已注册的 Java 版本，并让你选择一个作为默认。

### 验证

可以使用以下命令来验证当前的默认 Java 版本：

```
java -version
```

这将显示当前默认 Java 版本的详细信息。

> 注意，每次添加或删除 Java 版本时，都应该使用 `update-alternatives` 来更新系统配置。

（完）