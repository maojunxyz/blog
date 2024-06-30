---
title: Git客户端工具GitUi安装及使用
date: 2024/03/25 11:44
updated: 2024/03/25 11:44
categories:
- [技术, Git]
tags:
- Git
- GitUi
---

## GitUi介绍

> GitUI provides you with the comfort of a git GUI but right in your terminal.  
> GitUI是一个Git GUI客户端，但是它直接在终端中运行。

### 特性

- 快速而直观的纯键盘操作控制  
提供了一个针对高效用户优化的流线型界面，所有操作都可以通过键盘快捷键高效完成，从而最大程度减少对鼠标或触控板的依赖。

- 基于上下文的帮助  
无需记忆大量热键，应用会根据当前操作场景自动提供相关帮助信息。

- 查看、提交与修订变更  
（包含钩子功能：pre-commit, commit-msg, post-commit, prepare-commit-msg）：能够查看工作区状态，提交版本更新，并在必要时修订已提交的变更，同时支持预提交和提交后的各种脚本钩子功能。

- 暂存、取消暂存、还原与重置文件、块和行  
可以灵活管理暂存区，实现文件部分或全部内容的暂存、取消暂存，以及回滚到某个历史版本的状态。

- 储藏功能  
（保存、弹出、应用、丢弃及检查）：支持临时存储工作区未提交的变化，并能在之后随时恢复或应用这些变化。

- 向远程仓库推送与从远程仓库拉取  
进行版本同步操作，包括将本地分支的变更推送到远程仓库，以及从远程仓库获取最新的提交。

- 分支列表管理  
（创建、重命名、删除、检出、处理远程分支）：提供全面的分支管理功能，包括新建、改名、删除分支，切换分支以及与远程分支的交互。

- 浏览与搜索提交日志、比较已提交的差异  
允许用户查看项目的历史提交记录，搜索特定提交，并能可视化比较不同提交之间的差异。

- 响应式终端用户界面  
界面设计为适应不同终端尺寸和分辨率，确保良好的用户体验。

- 异步 Git API 实现流畅控制  
通过异步处理 Git 操作来提升应用性能，使得用户操作更加流畅无阻。

- 子模块支持  
内置对 Git 子模块的支持，能够方便地管理和跟踪项目中的嵌套 Git 仓库。

- GPG 提交签名  
支持使用 GPG 对提交进行数字签名以保证提交者的身份验证和数据完整性，但同时也指出在某些情况下可能存在问题或限制。


## 安装使用

进入GitUi的下载地址，根据操作系统选择对应的安装包进行安装。
[GitUi下载](https://github.com/extrawurst/gitui/releases)

以Windows为例：  
![gitui制品包](./assets/image-16768202869359616.png)

下载gitui.msi文件，双击安装即可。安装完成后新建一个终端，进入一个Git仓库目录，在命令行中输入`gitui`打开GitUi界面。
![gitui界面](./assets/image-16768202914071552.png)

### GitUi常用快捷键

GItUi默认的快捷键是基于键盘上下左右键的，可以通过`F1`键查看快捷键列表。你可以自定义快捷键，具体操作可以参考[GitUi文档](https://github.com/extrawurst/gitui/blob/master/KEY_CONFIG.md)。

这里将GitUi快捷键修改为Vim风格为例：

下载[vim_style_key_config.ron](https://raw.githubusercontent.com/extrawurst/gitui/master/vim_style_key_config.ron)文件修改名称为`key_bindings.ron`。

打开资源管理器目录，在地址栏输入`%appdata%\gitui`，找到`key_bindings.ron`文件，将文件复制到`%appdata%\gitui`目录下。

![key_bindings.ron](./assets/image-16768202949673984.png)

配置完成后，在GitUi中按`F1`键查看快捷键列表，可以看到快捷键已经修改为Vim风格。


### 编辑文件

在GitUi中，可以通过`I`键进入编辑模式，此时可以编辑文件。默认的编辑器为`vi`，但在Windows下，可能会出现`vi`命令无法使用的情况。

![vi命令报错](./assets/image-16768202733110272.png)

需要手动指定编辑器。可以通过设置`GIT_EDITOR`、 `VISUAL`或 `EDITOR`环境变量的方式生效。

设置临时环境变量:
```cmd
set EDITOR=nvim
```

设置永久环境变量:
```cmd
setx EDITOR nvim
```

设置完环境变量后，新建一个终端，进入一个Git仓库目录，在命令行中输入`gitui`打开GitUi界面。再次编辑文件，可以看到编辑器已经切换为`nvim`。

### 提交到暂存区

当修改了一个已经被Git追踪的文件，该文件处于"modified"（已修改）状态。如果想要将这些更改包含在下一次提交中，需要先将它们添加到暂存区域（也称为索引），此时文件就进入了"staged"状态。在GitUi中，可以通过`回车`键将选中的文件添加到暂存区，通过`a`键将所有文件文件添加到暂存区。对应的Git命令为`git add <file>`。

![暂存区](./assets/image-16768202668049408.png)

### 提交本地历史

在GitUi中，可以通过`c`键提交本地历史，此时会弹出提交信息输入框，输入提交信息后，可以通过`ctrl+d`键提交，也可以通过`esc`键取消提交。对应的Git命令为`git commit -m <message>`。
![commit](./assets/image-16768202776347648.png)

### 查看提交日志

在GitUi中，可以通过数字`2`键查看提交日志，此时会切换到log栏，可以数据`1`键回到Status栏。对应的Git命令为`git log`。

![Log](./assets/image-16768202814080000.png)

### 推送到远程仓库

在GitUi中，可以通过`p`键推送到远程仓库。对应的Git命令为`git push <remote> <branch>`。

在推送的时候可能会遇到认证失败的问题。

![push failed](./assets/image-20240331120735204.png)



需要配置ssh代理。参考配置[Adding your SSH key to the ssh-agent](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent#adding-your-ssh-key-to-the-ssh-agent)。

步骤：

1. 打开命令行窗口，执行下面的命令生成ssh key。

```cmd
ssh-keygen -t ed25519 -C "your_email@example.com"
```

2. 以管理员身份运行powershell,并执行下面的命令。

```ps1
# start the ssh-agent in the background
Get-Service -Name ssh-agent | Set-Service -StartupType Manual
Start-Service ssh-agent
```
3. 新建一个普通命令行窗口，并执行下面的命令。

```ps1
ssh-add c:/Users/YOU/.ssh/id_ed25519
```

4. 新建一个命令行窗口，打开GitUi，通过`p`键推送到远程仓库。

（完）