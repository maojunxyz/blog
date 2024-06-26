---
title: Vifm命令行文件管理器入门
date: 2020/03/28 00:00
updated: 2023/09/10 12:18
categories:
- [技术, 示例]
tags:
- 软件
- Linux
- Vifm
---

Vifm是一个实用的类 Vim 快捷键终端文件管理器，可以用来替代图形文件管理器。

## 快捷键

| 快捷键       | 描述                                   |
| :----------- | :------------------------------------- |
| ctrl-w hjkl  | 切换窗口                               |
| ctrl-w s     | 水平分隔                               |
| ctrl-w v     | 垂直分隔窗口                           |
| w            | 另个窗口预览当前光标文件               |
| e            | 当前窗口预览光标文件                   |
| dd           | 删除文件                               |
| :trashes     | 查看垃圾箱目录                         |
| lstrash      | 查看垃圾桶文件                         |
| :restore     | 还原选中的垃圾箱文件                   |
| j,k          | 上下移动                               |
| h,l          | 在父/子目录之间移动                    |
| gg           | 移动到文件列表首行                     |
| G            | 移动到文件列表末行                     |
| M            | 光标移动到窗口中间                     |
| H,L          | 光标移动到窗口首末文件                 |
| gh           | 返回上级目录                           |
| gl,Enter     | 进入目录或打开文件                     |
| dd,d         | 删除文件(放入回收站)                   |
| DD,D         | 删除文件(不放回收站)                   |
| yy,Y,y       | 复制文件                               |
| p            | 粘贴文件                               |
| u            | 撤销操作                               |
| Space,Tab    | 在两个panel之间切换                    |
| /            | 查找文件                               |
| m[a-zA-Z0-9] | 书签                                   |
| '[a-zA-Z0-9] | 跳转到书签位置                         |
| za           | 切换隐藏文件显隐                       |
| zo           | 显示隐藏文件                           |
| zm           | 不显示隐藏文件                         |
| :fil regex   | 隐藏匹配regex的文件                    |
| zO           | 显示被:fil命令过滤的文件               |
| zM           | 隐藏被:fil命令过滤的文件               |
| cp           | 更改文件属性权限                       |
| cw           | 文件/文件夹重命名                      |
| cW           | 文件/文件夹重命名,不包含扩展名         |
| ga           | 计算文件夹大小                         |
| !prog        | 执行系统命令, %f可以用来当前选中文件名 |