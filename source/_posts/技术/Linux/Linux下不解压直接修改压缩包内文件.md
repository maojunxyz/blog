---
title: Linux下不解压直接修改压缩包内文件
date: 2020/03/25 00:00
updated: 2023/09/10 11:58
categories:
- [技术, Linux]
tags:
- Linux
- Shell
---

## 前言

从 Windows 转到 Linux 桌面发行版后，就对在终端上完成一些工作产生痴迷。比如修改，复制，移动，删除文件等等。加上 Linux 服务器都不带 GUI 工具，能够直接使用命令行可以提高工作效率。



## 修改压缩包文件

VIM 可以直接打开压缩包，然后对想要的压缩文件进行编辑保存，一切都是vim的命令完成。修改后｀wp`就保存退出了。

> 注意，viｍ 要依赖zip工具来完成打开压缩包。



## 添加压缩包文件

如果用图形工具则直接鼠标双击打开压缩包，把想要添加的内容拖拽到压缩包里即可。

 但是生产上的服务器没有图形工具，若下载本地再上传服务器会显得非常麻烦。可以针对不同的文件使用不同的命令。

- tar类型

  添加新文件到压缩包：

```bash
$ tar rvf  /path/to/archive.tar  /path/to/newfile.txt
```

​	更新文件到压缩包：

```bash
$ tar uvf /path/to/archive.tar  /path/to/newfile.txt
```

- zip类型 

```bash
$ zip -rv zipfile.zip newfile.txt newfile1.txt
```

- jar类型 

```bash
$ jar -uvf jarfile.jar newfile.txt
```



## 从压缩包删除文件

- tar类型

```bash
1$ tar -dvf archive.tar filename.txt
```

- zip类型

```bash
1$ zip -d zipfile.zip filename.doc \*.txt
```

- jar类型

```bash
1$ zip -d jarfile.jar file1.txt file2.txt
```

（本文完）

**参考链接：**

- [how to add new files into an archive file (tar, gz, zip) in linux (also delete files)](http://www.lostsaloon.com/technology/how-to-add-files-to-archive-file-in-linux-also-delete-files)