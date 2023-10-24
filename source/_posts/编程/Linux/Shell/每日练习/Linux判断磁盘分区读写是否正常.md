---
title: Linux判断磁盘分区读写是否正常
date: 2023/10/23 17:25
updated: 2023/10/23 17:25
categories:
- [编程, Linux]
tags:
- Linux
- Shell
- 每日练习
---

## Linux判断磁盘分区读写是否正常

1. 判断磁盘分区读写是否正常，并打印信息

```sh
#!/bin/sh
# author: maojunxyz
# version: 1.0
# date: 2023-10-23

for mount_p in `df -h | sed '1d' | grep -v 'tmpfs'  |awk '{print $NF}'`;do
  touch $mount_p/testfile && rm -rf $mount_p/testfile
  if [ $? -ne 0 ];then
     echo "$mount_p 读写有问题" 
  else 
     echo "$mount_p 读写正常" 
  fi
done
```

（本文完）