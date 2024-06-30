---
title: 解决Git在终端中文乱码的问题
date: 2023/11/02 20:48
updated: 2023/11/02 20:48
categories:
- [技术, Git]
tags:
- Git
---

## 解决Git在命令行中文显示乱码

将git配置文件 `core.quotepath`项设置为`false`。`quotepath`表示引用路径，加上`--global`表示全局配置:

```bash
git config --global core.quotepath false
```



（本文完）