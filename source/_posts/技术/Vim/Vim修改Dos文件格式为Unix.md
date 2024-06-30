---
title: Vim修改Dos文件格式为Unix
date: 2023/11/05 13:26
updated: 2023/11/05 13:26
categories:
- [技术, Vim]
tags:
- Vim
- Nvim
---


```vim
# 查看当前文本的文件格式
:set ff
# 修改为unix的文本格式
:set ff=unix
# 修改为dos的文本格式
:set ff=dos
```


（完）