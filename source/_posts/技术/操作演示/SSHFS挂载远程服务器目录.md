---
title: SSHFS 挂载远程服务器目录
date: 2020/03/06 18:06
updated: 2023/09/09 14:54
categories:
- [技术, 示例]
tags:
- Windows
- SSHFS
---

##  介绍

`GNU/Linux`系统中经常需要用到目录挂载,比如挂载**U盘**,**移动硬盘**等。目录挂载可以使用`Linux`的`mount`工具完成。

有的时候我们希望能将远程服务器上的某个文件夹挂载到本地目录，比如`tomcat`下的`webapps`目录，这样不再通过`sftp`工具拷贝上传了，直接将本地`war`包拷贝到挂载的`webapps`目录即可。



## SSHFS

`sshfs`走的是`ssh`协议，只需要在本机上安装对应的`sshfs`客户端即可，通过 `sshfs` 挂载远程服务器上的目录到本地。常见的`sshfs`有如下几类：

- Linux平台：[https://github.com/libfuse/sshfs](https://web.archive.org/web/20201019221920/https://github.com/libfuse/sshfs)
- Mac平台: [https://github.com/osxfuse/sshfs](https://web.archive.org/web/20201019221920/https://github.com/osxfuse/sshfs)
- Windows平台:[https://github.com/feo-cz/win-sshfs](https://web.archive.org/web/20201019221920/https://github.com/feo-cz/win-sshfs)

这里以 `pacman` 包管理系统为例，`pacman` 仓库已经提供了 `sshfs` 的二进制包，所以直接安装即可:

```bash
$ pacman -S sshfs
```

安装后先在本地创建一个挂载点:

```bash
# 本机创建一个webapps的挂载目录
$ sudo mkdir /mnt/webapps 
```

> 挂载目录可以指定任意位置



使用`sshfs`命令挂载远程目录:

```bash
$ sshfs -o allow_other,transform_symlinks [email protected]:/home/user /etc/letsencrypt
```

> 参数说明：`allow_other`:提供访问权限 ; `transform_symlinks`:挂载软链接。



可能会出现的错误：

```bash
fusermount3: option allow_other only allowed if 'user_allow_other' is set in /etc/fuse.conf
```

解决方法：在本机的文件`/etc/fuse.conf`中取消注释下面的内容:

```ini
user_allow_other
```

> 如果没有则自行添加即可。



如果是使用账号密码登入远程服务器的则进行目录挂载的时候需要输入密码。如果想通过`ssh密钥`登入，需指定密钥的位置:

```bash
$ sshfs -o allow_other,transform_symlinks -o IdentityFile=~/.ssh/id_rsa [email protected]:/home/user/webapps /mnt/webapps
```

> `IdentityFile`:指定`ssh`密钥位置



创建远程目录挂载后，就可以像操作本地目录一样操作远程目录上的文件了。对挂载目录中文件的修改将直接同步给远程目录。

如果有多个目录需要挂载，重复以上挂载步骤即可。需要注意是,这种挂载仅是临时挂载点，如果本地或者远程机器关机或者重启则需要再次挂载生效。

如果需要设置永久挂载则需要编辑`/etc/fstab`文件,设置自动挂载文件系统:

```bash
$ sudo vim /etc/fstab
```

添加以下内容:

```bash
sshfs#[email protected]:/home/user/webapps /mnt/webapps
```

> 设置完永久挂载点后需要重启生效 最好不要在生产机器上设置永久挂载点，以免本地机器被入侵造成连带损失



设置完远程挂载点后，就可以通过在本地操作挂载目录:除了无法在挂载目录执行程序或使用脚本，其他增删改查操作就可以像本地目录一样操作。

一个典型用法是，如果您在VPS上托管网站，并且需要定期更改网站。在本地挂载文件系统允许您启动您希望编辑站点的任何代码编辑器，IDE或文本编辑器，您所做的任何更改将在本地计算机上生成后立即反映在虚拟服务器上。

类似地，在用于编码项目的测试环境在服务器上，它允许非常简易的代码修改，可以立即测试而无需在本地和远程修改代码。

通过远程目录挂载，我们就可以实现在本地直接通过`IDE`编辑远程服务器上的文件，实现**本地修改，远程更新**的效果。



## 通过跳板机挂载机器

sshfs 通过跳板机挂载远程服务器,配置配置跳板机和需要跳板机登入的服务器。如下：

```bash
Host jump
    HostName xxx.xxx.xxx.xxx
    User xxx
    Port 22
    IdentityFile ~/.ssh/id_rsa

Host foo
    HostName xxx.xxx.xxx.xxx
    Port 22
    User xxx
    IdentityFile ~/.ssh/id_rsa
    ProxyCommand ssh jump -W %h:%p
```

> 将上面的`xxx`替换成实际的服务器`IP`和用户名。 配置了上面的跳板机，服务器就是可以通过`ssh foo`连接了，配置中会已经配置使用跳板机的代理。



因为`sshfs`是基于`SSH`协议的，也可以通过以下的命令挂载远程的服务器目录:

```bash
$ sshfs foo:/app/test ~/test
```

> 将远程服务器中的`/app/test`目录挂载到本地家目录的`test`下。



如果需要卸载挂载点，则可以通过以下命令:

```bash
$ sudo umount /mnt/webapps
```

或者下面的命令，两个命令都可以卸载挂载点。

```bash
$ sudo fusermount -u /mnt/webapps
```

(本文完）
