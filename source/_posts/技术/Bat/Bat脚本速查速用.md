---
title: Bat脚本速查速用
date: 2011/09/01 07:20:00
updated: 2011/09/01 07:20:00
categories:
- [技术, Bat]
tags:
- Bat
- Windows
---

## 简介
bat作为一种轻量级Windows脚本语言,易于入门与使用。

bat程序是Windows系统内置的批处理文件解释器,用于执行.bat或.cmd命令脚本文件。它起源于DOS时期,随Windows发展被深度集成到系统中。Bat脚本使用简单的命令行语法,可以调用各种Windows系统命令与工具,实现简单的自动化任务。

bat不需要安装任何软件,直接创建并运行.bat文件即可。其语法简单明了,适合初学者,也能满足轻量级脚本需求。通过组合语句与命令.Bat可以创建矩阵风格动画、启动菜单项目、定时执行任务等小工具。



## 常用命令

### pause-暂停

```
# bat脚本执行完后不关闭窗口(按任意键结束)
pause
```

### >-覆盖重定向

```
# 覆盖重定向到a.txt
dir > a.txt
```

### >>-追加重定向

```
# 追加重定向到a.txt
dir >> a.txt
```

### *?通配符

```
# 以txt结尾的
*.txt
```

### md-创建目录

```
# 创建 a b c三个目录
md a b c
# 创建 "a b c"一个目录
md "a b c"
```

### dir-打印目录

```
# 打印当前目录的信息
dir
# 打印当前目录名称
dir /b
# 打印当前目录和子文件名
dir /b /s
```

### REN-重命名

```
# 重命名文件
ren old new
```

### COPY-拷贝命令

```
# 拷贝单文件
copy filea dir/fileb
```

### XCOPY-增强拷贝

```
xcopy C:\source D:\destination
```

### MOVE-移动命令

```
move filea dir/fileb
```

### DEL-删除命令

```
# 删除选项 /s 递归删除  /q 静默删除
del a.txt
```

### Type-查看文件内容

```
type test.txt
```



## 实践

### 设置系统代理

```
@echo off
set ie_proxy=127.0.0.1:1080
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\Internet Settings" /v ProxyEnable /t REG_DWORD /d 1 /f >nul 2>nul
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\Internet Settings" /v ProxyServer /d "%ie_proxy%" /f >nul 2>nul
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\Internet Settings" /v ProxyOverride /t REG_SZ /d " 
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\Internet Settings" /v "ProxyOverride" /t REG_SZ /d "10.*.*.*;11.*.*.*;example.com;google.com;maojun.xyz" /f 1>nul
echo proxy set ok！
```

（完）