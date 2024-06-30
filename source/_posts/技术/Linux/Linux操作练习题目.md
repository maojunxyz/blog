---
title: Linux操作练习题目
date: 2023/10/23 17:25
updated: 2024/03/27 09:17:06
categories:
- [技术, Linux]
tags:
- Linux
- Shell
- 数据结构和算法
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
## Linux判断磁盘分区读写是否正常

判断磁盘分区读写是否正常，并打印信息

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


## 修改目录下的文件权限和属组

```sh
#!/bin/sh
# author: maojunxyz
# version: 1.0
# date: 2023-10-24
for f in `find test`
do
# 查看文件权限
f_p=`stat -c %a $f`
# 查看文件所有者
f_u=`stat -c %U $f`
# 查看文件所属组
f_g=`stat -c %G $f`

  # 判断是否为目o
  if [ -d $f ];then
    [ $f_p != '755' ] && chmod 755 $f
  else
    [ $f_p != '644' ] && chmod 644 $f
  fi
  
  # 修改文件的属主和属组
  [ $f_u != 'www' ] && chown  www $f
  [ $f_g != 'root' ] && chown :root $f
done
```

## 判断输入的日期是否符合要求

判断用户输入的日期，要求符合`yyyymmdd`的格式，并输出校验结果。


```sh
#/bin/bash

# author: maojunxyz
# version: 1.0
# date: 2023-10-29

if [ $# -ne 1 ] || [ ${#1} -ne 8 ];then
  echo "Usage: bash $0 yyyymmdd"
  exit 1
fi 

mydate=$1
year=${mydate:0:4}
month=${mydate:4:2}
day=${mydate:6:2}

# 将正确和错误的输出都不打印出来
if cal "$day" "$month" "$year" >/dev/null 2>/dev/null
then
  echo "The data is OK. The date is ${year}年${month}月${day}日"
else 
  echo "The data is Error."
fi
```

## 去掉文件的空行

写一个 bash脚本以去掉一个文本文件nowcoder.txt中的空行
示例:
假设nowcoder.txt 内容如下：

```cpp
abc

567


aaa
bbb



ccc
```

你的脚本应当输出：

```c
abc
567
aaa
bbb
ccc
```



### 题解

```sh
# 方法 1
sed -n '/^$/!p' nowcoder.txt

# 方法2
awk -n '{if ($0 != "") print $0}' nowcoder.txt

# 方法 3
awk '!/^$/ {print $0}' nowcoder.txt

# 方法 4
grep -v '^$' nowcoder.txt



## 备份目录并创建软链接

1. 有一个目录/tmp/data/,该目录下有数百个子目录。
2. 需要将老文件（365天前的）挪到另外一个目录/tmp/data1下，还需要做软链接。
3. 要确保老文件成功挪到/tmp/data1下之后才能做软链接，需要有日志。

```sh
#!/bin/sh
# author: maojunxyz
# version: 1.0
# date: 2023-10-25

main(){
cd /tmp/data
for dir in `ls`
do
  echo $dir
  for old_data in `find $dir -maxdepth 1 -type d -mtime +365`
  do
     rsync -aR $old_data  /tmp/data1/
     if [ $? -eq 0 ];then
       rm -rf $old_data && echo "/tmp/$old_data 移动成功"
       ln -s /tmp/data1/$old_data /tmp/data/$old_data && \
       echo "/tmp/data/$old_data 创建软链成功"
     else
       echo "/tmp/$old_data 未移动成功"
     fi
  done
done
}

main &> /tmp/move_old_data_`date +%F`.log
```


## 打印字母数小于8的单词

写一个bash脚本以统计一个文本文件nowcoder.txt中字母数小于8的单词。
示例:
假设 nowcoder.txt 内容如下：

how they are implemented and applied in computer

你的脚本应当输出：
how
they
are

and

applied

in

说明:
不用担心你输出的空格以及换行的问题



### 题解

```sh
# 方法 1
awk -F " " '{for(i=1;i<=NF;i++) { if(length($i) < 8) { print($i) }}}' nowcoder.txt

# 方法 2
words=$(cat nowcoder.txt)
for i in ${words[*]}; do
    if [ ${#i} -lt 8 ]; then
        echo ${i}
    fi
done

# 方法 3
for i in $(cat nowcoder.txt); do
    if [ ${#i} -lt 8 ]; then
        echo ${i}
    fi
done
```


## 打印文件的最后5行

查看日志的时候，经常会从文件的末尾往前查看，请你写一个bash shell脚本以输出一个文本文件nowcoder.txt中的最后5行。
示例:
假设 nowcoder.txt 内容如下：

```cpp
#include<iostream>
using namespace std;
int main()
{
int a = 10;
int b = 100;
cout << "a + b:" << a + b << endl;
return 0;
}
```

你的脚本应当输出：

```cpp
int a = 10;
int b = 100;
cout << "a + b:" << a + b << endl;
return 0;
}
```

### 题解

```bash
# 方法1
end=$(wc -l < nowcoder.txt)
start=$(( end - 5 + 1))
if [[ $start -lt 1 ]]; then
	start=1
fi	
sed -n "$start,$end p" nowcoder.txt

# 方法 2
tail -n 5 nowcoder.txt

# 方法 3
tac nowcoder.txt | head -n 5 | tac

# 方法 4
awk 'BEGIN {
	s=1
} {
	lines[s]=$0;
	s++
}
END {
    for(i=NR-4; i<=NR; i++) {
        print(lines[i])
	}
}' nowcoder.txt

# 方法 5
function count_lines() {
	number=0
	filename="$1"
    while read p;
        do
            number=$((number + 1))
    	done < $filename
    echo $number
}
# 获取文件的行数
end=`count_lines nowcoder.txt`
start=$((end - 5 + 1))
if [[ $start -lt 1 ]]; then
	start=1
fi	
sed -n "$start,$end p" nowcoder.txt
```


## 打印空行的行号

编写一个shell脚本以输出一个文本文件nowcoder.txt中空行的行号（空行可能连续，从1开始输出）

示例:
假设 nowcoder.txt 内容如下：

```cpp
a
b

c

d

e


f
```

你的脚本应当输出：

```c
3
5
7
9
10
```



### 题解

```sh
# 方法 1
awk -n '/^$/ {print NR}' nowcoder.txt

# 方法 2
sed -n '/^$/=' nowcoder.txt

# 方法 3
i=1
while read line; do
	if [[ $line =~ ^$ ]]; then
		echo $i
	fi		
	i=$((i+1))
done<$1
```


## 抽奖程序给人员分配唯一的数字

1. 多人抽签游戏，给人员分配`1-99`的随机整数。
2. 相同名字的如果之前分配过随机数，则和之前一致。
3. 已经分配过的数字不能重复分配。
4. 循环执行，直到<kbd>Ctrl-C</kbd>键终止运行。



```bash
#!/bin/bash

# author: maojunxyz
# version: 1.0
# date: 2023-10-30

<<'COMMENT'
1. 多人抽签游戏，给人员分配1-99的随机整数。
2. 相同名字的如果之前分配过随机数，则和之前一致。
3. 已经分配过的数字不能重复分配。
4. 循环执行，直到<kbd>Ctrl-C</kbd>键终止运行。
COMMENT

# 生成1-99的数字，并校验已生成的数字
function create_number(){
while :
do
  num=$((RANDOM%99+1))
  n=$(awk -F ':' -v NUMBER="$num" '$2 == NUMBER' /tmp/name.log | wc -l)
  if [ "$n" -gt 0 ];then
    continue
  else
    echo "$num"
    break
  fi
done
}


while :
do
  read -p "Please input a name:" name
  # 若抽奖名单不存在，则根据名称生成数字并保存名单
  if [ ! -f /tmp/name.log ];then
  num=$((RANDOM%99+1))
  echo "Your number is: $num"
  echo "$name:$num" > /tmp/name.log
  else
    # 抽奖名单已存在，判断名称是否存在，存在直接打印抽奖名字和数字;不存在则创建名单。
    n=$(awk -F ':' -v NAME="$name" '$1 == NAME' /tmp/name.log | wc -l)
    if [ "$n" -gt 0 ];then
      echo "The name already exist."
      awk -F ':' -v NAME="$name" '$1 == NAME' /tmp/name.log
      continue
    else
      number=$(create_number)
    fi
    echo "Your number is: $number"
    echo "$name:$number" >> /tmp/name.log
  fi
done
```


## 机器存活状态检测

给定一批服务器IP，判断机器是否存活。

```sh
#!/bin/sh
# author: maojunxyz
# version: 1.0
# date: 2023-10-24
 
# 服务器IP列表
IPS=(baidu.com qq.com)
for IP in ${IPS[@]}
do
n=`ping -c5 $IP | grep "packet" |awk -F '%' '{print $1}' |awk '{print $NF}'`
if [ -z "$n" ];then
	echo "执行失败"
	exit 
else
  n1=`echo $n|sed 's/[0-9]//g'`
  if [ -n "$n1" ];then
    echo "执行失败了"
    exit 1
  fi
fi

if [ $n -ge 20 ];then
  echo "机器${IP}宕机，丢包率是${n}%"
else
  echo "机器${IP}正常，丢包率是${n}%"
fi
done
```


## 查找指定目录5分钟内有修改的文件

1. 使用脚本查找指定目录5分钟内有修改的文件并重定向文件列表到一个文件中。
2. 如果重定向的文件列表内容不为空，则修改文件名称为时间格式的名称。

```sh
#!/bin/bash
# author: maojunxyz
# version: 1.0
# date: 2023-10-26

date=`date +%Y%m%d%H%M`
base_dir=/opt

find $base_dir -type f -mmin -5 > /tmp/newf.txt

if [ -s /tmp/newf.txt ];then
  mv /tmp/newf.txt /tmp/${date}.txt
fi
```


## 检查进程和端口服务是否正常

1. 检查进程服务是否存在
2. 检查进程端口是否存在
3. 当2者都存活时，判定服务正常。


```bash
#!/bin/bash

# author: maojunxyz
# version: 1.0
# date: 2023-10-31

# 定义一个函数，用于检查必需的工具是否已安装
function check_tools(){
    if ! which pgrep &>/dev/null; then
        echo "没有安装pgrep命令"
        exit 1
    fi

    if ! which ss &>/dev/null; then
        echo "没有安装ss命令"
        exit 1
    fi
}

# 定义一个函数，用于检查特定进程是否在运行
function check_ps(){
    if pgrep "$1" &>/dev/null; then
        return 0  # 进程存在，返回0表示真
    else
        return 1  # 进程不存在，返回1表示假
    fi
}

# 定义一个函数，用于检查特定端口是否在监听
function check_port(){
    port_num=$(ss -lnp | grep -c ":$1 ")
    if [ "$port_num" -ne 0 ]; then
        return 0  # 端口在监听，返回0表示真
    else
        return 1  # 端口不在监听，返回1表示假
    fi
}

# 定义一个函数，用于检查特定服务（进程和端口）
function check_srv(){
    check_ps "$1" && check_port "$2"
    if check_ps "$1" && check_port "$2"; then
        echo "$1服务正常"
    else
        echo "$1服务不正常"
    fi
}

# 调用函数检查必需工具是否已安装
check_tools

# 分别检查指定服务的状态
check_srv nginx 443
check_srv mysql 3306
check_srv redis 6379
check_srv java 8080
```


## 监控站点状态

1. 写一个脚本监控网站的可用性，访问网站放回`2xx`，`3xx`状态码则代表可正常访问，其他状态码为不可正常访问。

```sh
#!/bin/sh

# author: maojunxyz
# version: 1.0
# date: 2023-10-28

code=$(curl --connect-timeout 3 -I "$1" 2>/dev/null |grep  'HTTP' |awk '{print $2}')
if echo "$code" |grep -qE '^2[0-9][0-9]|3[0-9][0-9]';then
   echo "$1可正常访问"
else
   echo "$1不可正常访问"
fi
```


## 监控系统负载并定时记录状态信息

1. 监控系统负载，如果系统负载超过10，需要记录系统状态信息。
2. 要求每隔20s监控一次。
3. 系统状态信息需要保存到/opt/logs/下面，保留一个月。

```sh
#!/bin/sh
# author: maojunxyz
# version: 1.0
# date: 2023-10-25

# 如果/opt/logs目录不存在则创建目录
[ -d /opt/logs ] || mkdir -p /opt/logs

while :
do
  # 获取系统1分钟的负责，只保留最前面的值。
  load=`uptime |awk -F 'average:' '{print $2}' |cut -d',' -f1 |sed 's/ //g' |cut -d. -f1`
  if [ $load -gt 0 ];then
  top -bn1 | head -n 100 > /opt/logs/top.`date +%s`
  vmstat 1 10 > /opt/logs/vmstat.`date +%s`
  ss -an > /opt/logs/ss.`date +%s`
  fi
  sleep 20
  find /opt/logs \( -name "top*" -o -name "vmstat*" -o -name "ss*" \) -mtime +30 |xargs rm -rf 
done
```


## 监控网卡流量

监控`eth0`的网卡流量，如果接收量和请求量都为`0`,则重启网卡。



### 题解

```bash
#!/bin/bash

# author: maojunxyz
# version: 1.0
# date: 2023/11/01

# 设置系统语言为英文
LANG=en

# 检查是否已经安装了sar命令
if ! command -v sar &>/dev/null; then
    echo "sar 没有安装在系统中。"
    # 获取系统包管理器类型
    if [ -x "$(command -v apt-get)" ]; then
        # 使用apt安装sar命令
        sudo apt-get update
        sudo apt-get install sysstat
    elif [ -x "$(command -v yum)" ]; then
        # 使用yum安装sar命令
        sudo yum install sysstat
    else
        echo "无法确定系统包管理器类型。请手动安装sar命令。"
        exit 1
    fi

    # 检查安装结果
    if command -v sar &>/dev/null; then
        echo "sar 已成功安装在系统中。"
    else
        echo "sar 安装失败，请手动安装。"
        exit 1
    fi
fi


# 使用sar命令获取网络设备性能统计信息，每秒输出1次，共输出60次，然后筛选eth0的信息并将其保存到文件
sar -n DEV 1 60 | grep eth0 > /tmp/eth0.log

# 从保存的性能统计信息文件中提取平均接收流量数据（第5列），并移除小数点
n1=$(grep -i average /tmp/eth0.log | awk '{print $5}' | sed 's/\.//g')

# 从保存的性能统计信息文件中提取平均发送流量数据（第6列），并移除小数点
n2=$(grep -i average /tmp/eth0.log | awk '{print $6}' | sed 's/\.//g')

# 删除临时文件，清理残留的性能统计信息
rm -f /tmp/eth0.log

# 检查接收流量和发送流量是否都为零
if [ "$n1" == "000" ] && [ "$n2" == "000" ]; then
  # 如果接收和发送流量都为零，显示消息并重启eth0网卡
  echo "eth0网卡没有流量，需要重启网卡"
  ifdown eth0 && ifup eth0
fi
```


## 统计文件的行数

1. 编写一个`shell`脚本以输出一个文本文件`nowcoder.txt`中的行数
   示例:
   假设`nowcoder.txt`内容如下：

```cpp
#include <iostream>
using namespace std;
int main()
{
    int a = 10;
    int b = 100;
    cout << "a + b:" << a + b << endl;
    return 0;
}
```

你的脚本应当输出：
9



### 题解

```bash
# 方法 1
wc -l nowcoder.txt | awk '{print $1}'
# 方法 2-1
cat nowcoder.txt | wc -l
# 方法 2-2
wc -l < nowcoder.txt

# 方法 3-1
cat -n nowcoder.txt | awk '{print $1}' | tail -n 1
# 方法 3-2
cat -n nowcoder.txt | tail -n 1 | awk '{print $1}'

# 方法 4
i=0
while read p;do
  i=$((i + 1))
done < nowcoder.txt
echo $i
```

## 统计文本中每行数字的个数

```bash
#!/bin/bash

# author: maojunxyz
# version: 1.0
# date: 2023-11-03

if [ $# -ne 1 ]; then
  echo "输入的参数个数只能是1" 
  exit 1
fi

sum=0  

while read -r line; do
  # 使用sed命令将每行中的非数字字符替换为空格，然后使用wc -L统计字符数，将结果赋给变量num。
  num=$(echo "$line" | sed 's/[^0-9]//g' | wc -L)
  sum=$((sum + num)) 
done < "$1" 

echo "sum: $sum" 
```

## 菜单脚本根据输入指令来执行命令

1. 编写一个脚本根据输入指令来执行命令。
2. 当输入`1`或`d`时，输出系统时间。
3. 当输入`2`时，列出文件列表。
4. 当输入`3`时，打印当前登入用户。
5. 当输入`4`时，打印当前目录。
6. 当输入`q`时，退出菜单。
7. 当输入指令时，提示错误信息`Wrong input, try again!`并继续。

```sh
#!/bin/bash
# author: maojunxyz
# version: 1.0
# date: 2023-10-26

while :
do
 read -p "please input a number 1-4: " n
 case $n in 
   1|d)
     date
     continue
     ;;
   2)  
     ls
     continue
     ;;
   3)  
     who
     continue
     ;;
   4)  
     pwd
     continue
     ;;
   q)  
     echo "Exit"
     exit 1
     break
     ;;
   *)  
     echo "Wrong input, try again!"
     continue
     ;;
 esac
done
```

## 输入数字并循环执行

1. 提示用户输入数字，并打印出输入的数字。
2. 当用户输入不是数字时提示报错信息。
3. 当用户输入字母`q`时，退出脚本。

```sh
#!/bin/bash
# author: maojunxyz
# version: 1.0
# date: 2023-10-27

while :
do
  read -p "请输入数字，当输入q时退出!" n
  num=$(echo "$n" |sed -r 's/[0-9]//g' |  wc -c)
  if [ "$n" == "q" ]
  then
    exit 0
  fi
  if [ "$num" -ne 1 ]
  then 
     echo "输入的不是数字，请重试!"
  else
     echo "你输入的数字是：$n"
  fi
done
```


## 输出 0 到 500 中 7 的倍数



### 题解

```bash
#!/bin/bash

# author: maojunxyz
# version: 1.0
# date: 2023-11-02

# 方法 1
start=0
end=500
step=7
while [ $start -lt $end ]; do
    if !(( $start % $step )); then
         echo $start
         start=$(( start + 1 ))
    else
         start=$(( start + 1 ))
    fi
done

# 方法 2
for num in {0..500..7}; do 
   echo "${num}"
done

# 方法 3
for num in {0..500};do
   [[ "((num%7))" -eq 0 ]] && echo "${num}"
done
```


## 输出第5行的内容

编写一个bash脚本以输出一个文本文件nowcoder.txt中第5行的内容。

示例:
假设 nowcoder.txt 内容如下：

```c
welcome
to
nowcoder
this
is
shell
code
```

你的脚本应当输出：
is。



### 题解

```sh
# 方法 1
sed -n '5p' nowcoder.txt

# 方法 2 
head -n 5 nowcoder.txt | tail -n 1

# 方法 3 
awk '{ if(NR==5) {print($0)} }' nowcoder.txt
```


**在线题目**

- [去掉文件的空行](https://www.nowcoder.com/practice/0372acd5725d40669640fd25e9fb7b0f)
- [打印字母数小于8的单词](https://www.nowcoder.com/practice/bd5b5d4b93a04226a81afbabf0be797d)
- [打印文件的最后5行](https://www.nowcoder.com/practice/ff6f36d357d24ce5a0eb817a0ef85ee2)
- [打印空行的行号](https://www.nowcoder.com/practice/030fc368e42e44b8b1f8985a8d6ad255)
- [统计文件的行数](https://www.nowcoder.com/practice/205ccba30b264ae697a78f425f276779)
- [输出 0 到 500 中 7 的倍数](https://www.nowcoder.com/practice/8b85768394304511b0eb887244e51872)
- [输出第5行的内容](https://www.nowcoder.com/practice/1d5978c6136d4252904757b4fa0c9296)




（完）