---
title: 安装 Ubuntu 并做相关配置
date: 2023/07/30 19:44
updated: 2023/07/30 19:44
categories:
- [技术, Linux]
tags:
- Ubuntu
- 操作系统
---
##  折腾

在不断尝试折腾过以下的Linux桌面系统：

- Deepin
- Centos
- Mint
- Manjaro
- ArchLinux
- Fedora
- Xubuntu

最终选择 Ubuntu 做为最终的 Linux 桌面归宿。主要看中以下几个方面：

1. 有商业公司的支撑，笔记本产家对 Ubuntu 相对兼容，软件包大多支持 Ubuntu 系统和 deb 格式，文档资源丰富。
2. 软件兼容性好，很多软件公司的产品就是在 Ubuntu 系统上开发的，比如 Docker。
3. 企业服务器大多是 Centos, 桌面使用 Ubuntu 可以更好的互补。
4. 不想继续折腾了，想以 Ubuntu 做为工作、学习、娱乐的备用机。
5. ...或许是图个新鲜



## 安装系统

### 下载系统

进入 Ubuntu 的官方网站，在下载页面选择桌面版系统下载。快捷链接：[Ubuntu桌面版](https://ubuntu.com/download/desktop)

目前最新的长期稳定版本是：Ubuntu 22.04.2 LTS(Jammy Jellyfish ),长期支持截止日期为：2027 年 4 月。



### 安装系统

首先制作系统启动盘，插入 U 盘后，使用 fdisk 命令查看磁盘分区。注意下面的操作都是在 Linux 上完成的，如果是 Windows 系统，可以使用 refus

 来刻录系统。快捷链接：[refus](https://rufus.ie/zh/)

```shell
$ sudo fdisk -l
```

查看到 U 盘的分区地址后，使用 dd 命令刻录刚下载的系统到 U 盘。

```shell
$ sudo dd if=/Downloads/ubuntu-22.04.2-desktop-amd64.iso of=/dev/sda bs=4M
```

待刻录完后，重启电脑并根据不同的笔记本厂家来通过按键来触发开机引导项，联想 Yoga 笔记本可通过 F12 来进入引导选项。

选择 U 盘作为引导盘后，等待加载系统并根据系统的提示进行设置。Ubuntu 安装时会检测是否存在系统引导和旧的系统，会给予提示是删除、保留或更新的操作，根据个人需求选择即可。后续的磁盘分区设置都可以采取默认，几乎是一键安装，待系统完成安装重起系统，会提示拔掉引导 U 盘，重新完成后系统就安装完了。



### 安装和配置软件

本着不折腾的原则，系统基本上采用了默认配置和软件，优先从 Ubuntu 自带的软件仓库下载，没有再从软件官网下载对应的安装包。为了符合个人的使用习惯，安装了以下软件并相应的进行配置：

- Microsoft Edge浏览器
- Enpass密码管理
- Typora编辑器
- termius终端
- kate编辑器
- Fcitx5+Rime输入法
- Zetero
- Vim
- ...后续会根据使用做适当增减

软件基本上采用了默认的配置，部分软件是有同步功能的，登入账同步后也无需额外配置。仅对输入法做了相应的配置调整，

安装完 Fcitx5+Rime 后，自带的输入方案是不带小鹤双拼输入法的，需要自行配置。同时，部分软件不能调用 Fcitx5 输入法，也需要做环境做些配置。



#### 将 Fcitx5 写入环境变量：

```shell
# 编辑 /etc/systemd/user.conf，找到DefaultEnvironment这行
$ sudo vim /etc/systemd/user.conf
# 取消注释追加以下内容：
DefaultEnvironment=XMODIFIERS="@im=fcitx" XIM=fcitx XIM_PROGRAM=fcitx GTK_IM_MODULE=fcitx QT_IM_MODULE=fcitx
```



#### 增加小鹤双拼输入法

下载小鹤双拼在 Linux 下的配置文件：

```shell
# 克隆仓库地址
$ git clone https://github.com/maojunxyz/flypy-linux.git
# 进入下载后的目录
$ cd flypy-linux/rime-data
# 将目录下的文件内容都复制到fcitx5的rime目录里，然后重启fcitx5
$ cp -r  * ~/.local/share/fcitx5/rime
```

### 使用系统

在做完以上的配置后，就能将 Ubuntu做为辅助生产力来使用了。Ubuntu 中不乏自带了一些好用的软件和小游戏:

- Sudoku
- To Do



## 归宿

折腾的最终归宿就是默认配置。Ubuntu 还有很多值得分享的内容，需要在后续的使用中逐渐去发现和探索。

最终，我将 Ubuntu 作为折腾后的桌面 Linux 归宿了，毕竟年纪大了。

