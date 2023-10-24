---
title: Linux命令每日练习
date: 2023/10/20 22:22
updated: 2023/10/22 21:59
categories:
- [编程, Linux]
tags:
- Linux
- Shell
---

## 文件备份

遍历/test/目录下的txt文件做一个备份，备份的文件名增加一个年月日的后缀，比如将a.txt备份为a.txt20231020

```sh
#!/bin/sh
# author: maojunxyz
# version: 1.0
# date: 2023-10-20
 
suffix=`date +%Y%m%d`
for f in `find ./test -type f -name "*.txt"`
do
  echo "start backup$f"
  cp ${f} ${f}_${suffix}
done 
```



## 打印正方形

```sh
#!/bin/sh
# author: maojunxyz
# version: 1.0
# date: 2023-10-21
 
read -p "please input a number: " count
num=`echo $count |sed 's/[0-9]//g'`
if [ -n "$num" ]
then
  echo "请输入一个纯数字"
  exit 1
fi
 
for m in `seq $count`
do
  for n in `seq $count`
  do
    if [ $n -lt $count ]
    then 
      echo -n "* "
    else
      echo  "*"
    fi
  done
done
```



## 创建多个用户并保存记录

1. 创建10个用户，并给他们设置随机密码，密码记录到一个文件里，文件名userinfo.txt。
2. 用户从user00到user09。
3. 密码要求：包含大小写字母以及数字，密码长度15位。

```sh
#!/bin/sh
# author: maojunxyz
# version: 1.0
# date: 2023-10-22

# 判断文件是否已经存在
if [ -f /tmp/userinfo.txt ];then
  rm -rf /tmp/userinfo.txt;
fi 
   
# seq队列
for i in `seq -w 0 09`
do 
  # 生成不包含特殊符号的密码
  pass=$(openssl rand -base64 15)
   
# 检查用户是否已经存在
if id "user_${i}" &>/dev/null; then
    echo "用户 user_${i} 已存在." >> /tmp/userinfo.txt
else
  useradd user_${i} && echo "${p}" | passwd --stdin user_${i}
  echo "user_${i} ${pass}" >> /tmp/userinfo.txt
fi 
done
```

（本文完）
