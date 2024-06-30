---
title: SSH 使用 lrzsz 进行上传和下载
date: 2020/03/05 00:00
updated: 2023/09/08 5:43
categories:
- [技术, Linux]
tags:
- Linux
- Shell
---

## SSH上传下载

通过 `SSH` 命令连接到服务器，当有上传和下载的需求，通常需要借助其他工具来完成，比如`FTP`、`SFTP`工具等。

这时可以借助 Zmodem 文件传输协议，常见的图形工具`Xshell`、`SecureCRT`都是支持`Zmodem`协议的，但`Putty`并不支持。

`zmodem`协议有以下的优点:

1. 通过`rz`、`sz`命令，简化了上传和下载的步骤。
2. 上传和下载不需要再次输入用户密码.

为了使用 `Zmodem` 协议上传和下载文件，首先安装支持该协议的工具。 其中[lrzsz](https://web.archive.org/web/20201024060121/https://wiki.maojun.xyz/#lrzsz)就是该协议的实现工具之一。`lrzsz`这个工具只适合传输小文件，不适合传输大型文件。大文件传输更推荐使用`Rsync`传输，支持增量差异传输。



## zssh 和 lrzsz

如果使用`GNU/Linux`终端则需要安装。这里以的`pacman`包管理 Linux 操作系统为例，在命令行执行以下命令:

```bash
# 让ssh支持zmodem协议
$ sudo pacman -S zssh 
# 提供rz,sz命令
$ sudo pacman -S lrzsz 
```

lrzsz 常用的两个命令来上传和下载文件：

- rz: 接受文件
- sz: 发送文件

> 方便记忆可以将`r`记忆为receive,`s`记忆为send



在使用上要将 `ssh` 替换成 `zssh` 命令，即支持`zmodem`协议的`ssh`。 `zssh`和`ssh`使用上并无差异，例如原先使用`ssh`连接服务器的命令如下：

```bash
ssh user@password
```

使用`zssh`命令替换：

```bash
zssh user@password
```

本地安装`lrzsz`后，再去对应的服务器上安装。这里以 `yum` 包管理为例安装 `lrzsz` ,输入以下命令进行安装：

```bash
$ sudo yum -y install lrzsz
```

安装后就可以模拟测试文件传输，如果使用命令行终端则使用`zssh`连接安装了`lrzsz`的服务器。如果使用`Xshell`等图形工具正常连接即可。

```bash
$ zssh user@password
Press ^@ (C-Space) to enter file transfer mode, then ? for help
```

连接成功后可以看到一行命令提示，如果要使用`zssh`则输入`ctrl+2（@）`快捷键即可切换。(若不是`zssh`登入服务器没有该提示)。

如果需要上传文件到服务器，使用`xshell`等支持`Zmodem`协议的图形工具，直接输入命令`rz`接收文件,`xshell`会打开一个窗口可以方便的选择文件上传。

使用终端模拟工具则在当前服务器使用`ctrl+2`切换到`zssh`窗口：

```bash
# 快捷键输入`ctrl+2`切换到`zssh`
# 查看一下本地目录
zssh>pwd
# 查看本地文件
zssh>ls
# 上传`foo.txt`文件到服务器
zssh>sz foo.txt
```

> 注意：切换到`zssh`后可以使用本地机器的终端命令。



如果需要下载服务器的文件到本地，使用`xshell`等支持`Zmodem`协议的图形工具则可以输入`sz`加上发送的文件名，`xshell`会打开一个窗口可以方便的保存文件位置。

使用终端模拟工具则先用`sz`命令发送对应的文件：

```bash
# 使用`sz`命令发送`bar.txt`
sz bar.txt 
# `sz`命令后会出现一行乱码，然后快捷键`ctrl+2`切换到`zssh`
# 查看本机当前位置
zssh > pwd 
# 接收服务器`sz`发送的文件`bar.txt`
zssh > rz 
```

使用`lrzsz`命令可以帮助我们在不使用`sftp`的情况下传输文件，并且可以方便快捷的上传和下载文件。

(本文完）