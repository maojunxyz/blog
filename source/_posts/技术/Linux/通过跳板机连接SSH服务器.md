---
title: 通过跳板机连接 SSH 服务器
date: 2020/03/04 00:00
updated: 2023/09/07 7:40
categories:
- [技术, Linux]
tags:
- Linux
- Shell
---

## SSH无法直连

服务器因为安全原因不能直接通过外网访问，要经过中转连接。

服务器提供了两种方式连接：

1. 通过堡垒机调用本地 `SSH` 客户端登入到服务器。
2. 通过跳板机端口转发访问

当：

- 本机可以访问-->跳板机
- 跳板机可以访问-->服务器
- 笔记本不能访问----x服务器

可以使用跳板机代理访问服务器。



## 跳板机

跳板机正如它的名字一样，可以在本机和服务器之间提供一个跳板。本地主机 A 可以直接访问 B 主机，但是无法访问 C 主机，但 B 主机可以访问 C 主机。可以通过将 B 主机作为主机 A 的跳板机来间接访问 C 主机。

服务器提供了跳板机的账号和密码，为了避免每次输入账号密码以及提高登入的安全性。配置生成公钥并上传到跳板机后面就无需输入账号密码了。通过`ssh-keygen`命令直接一路回车生成的两个文件：

- 公钥文件 `~/.ssh/id_rsa.pub`
- 私钥文件 `~/.ssh/id_rsa`

生成的公钥通过以下的命令上传到跳板机:

```bash
$ ssh-copy-id  -i /etc/.ssh/id_rsa user@server
```

> user@server替换为跳板机实际的用户和地址 如果出现`bash: sh-copy-id: command not found`，则安装`openssh-clients`解决



上传公钥后，就可以免密登入了:

```bash
$ ssh user@server
```

完成跳板机的配置后，如果现在直接通过跳板机访问远程服务器的话是需要远程服务器的账号密码的。

由于远程服务器的密码由于安全原因能不能使用了。这里可以采用`2`种方法将本地的公钥上传到远程的服务器。

1. 借助页面堡垒机登入到`sftp`，然后将本地之前生成的公钥追加到远程服务器的`~/.ssh/authorized_keys`文件中。
2. 借助跳板机，先将公钥上传到跳板机，再通过跳板机追加到远程服务器的`~/.ssh/authorized_keys`文件中。

配置完远程服务器的公钥后，就可以实现本机通过跳板机访问服务器免密登入了:

```bash
$ ssh -J user@jumpserver user@remoteserver
```

> `jumpserver`为跳板机，`remoteserver`为远程目的服务器 `ssh`的`-J`参数用来指定跳板机，后面空格然后再跟上远程服务器



带端口例子:

```bash
$ ssh -J user@jumpserver:port user@remoteserver:port
```



如果有多层跳板机，同单一跳板机使用`-J`参数，之间用逗号`,`分隔跳板机。

```sh
$ ssh -J user@jumpserver1,user@jumpserver2:port user@remoteserver
```

通过上面的命令就能通过跳板机访问服务器了。



## 配置管理

可以通过`ssh`的`config`文件将以上信息管理起来，编辑`~/.ssh/config`文件:

对应的`ssh -J user@jumpserver:port  user@remoteserver:port`命令的配置如下:

```bash
# 跳板机
Host jump
    HostName jumpIP
    User user
    Port 22
    IdentityFile ~/.ssh/id_rsa

# 远程服务器
Host remote
    HostName remoteIP
    Port 22
    User user
    IdentityFile ~/.ssh/id_rsa
    ProxyCommand ssh jump -W %h:%p
```

> - Host:定义一个名称
> - HostName:定义服务器的地址
> - User:定义服务器的用户名
> - Port:定义服务器的端口
> - IdentityFile:定义公钥位置
> - proxyCommand:定义代理命令
> - `W`参数:通过`jump`服务器建立隧道连接



如果服务器需要多层跳板机才能访问，可以通过 `ssh` 的配置文件 `config` 中的 `ProxyJump` 来指定跳板机( `openssh>=7.3` )。

```bash
# 跳板机1
Host jump1
    HostName jumpIP1
    User user
    Port 22
    IdentityFile ~/.ssh/id_rsa

# 跳板机2
Host jump2
    HostName jumpIP2
    User user
    Port 22
    IdentityFile ~/.ssh/id_rsa

Host remote
  HostName remoteIP
  ProxyJump jump1,jump2
```

配置完成后就可以通过 `ssh remote` 进行登入，会先经过跳板机 `jump1` ，然后经过跳板机 `jump2` 并登入到 `remote` 服务器。

（本文完）