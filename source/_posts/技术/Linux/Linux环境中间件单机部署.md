---
title: Linux环境中间件单机部署
date: 2020/03/27 22:42:59
updated: 2020/03/27 22:42:59
categories:
- [技术, Linux]
tags:
- JDK
- Zookeeper
- Dubbo
- Mysql
- Redis
- RocketMQ
- Minio
- Elasticsearch
- Nginx
- Linux
---



## 约定项

安装包存放目录/export/soft



## JDK8安装

### 安装

```sh
su - root
mkdir -p /export/soft

#上传包
rz jdk-8u202-linux-x64.tar.gz           
#解压、
tar -zxvf jdk-8u202-linux-x64.tar.gz      
cd jdk1.8.0_202     
```



### 配置

编辑`/etc/profile `文件，追加以下内容

```sh
export JAVA_HOME=/export/middleware/jdk1.8.0_202
export PATH=$PATH:$JAVA_HOME/binH
```

刷新配置

```sh
source /etc/profile
```



### 验证

```sh
java –version            
java version "1.8.0_202"
Java(TM) SE Runtime Environment (build 1.8.0_202-b08)
Java HotSpot(TM) 64-Bit Server VM (build 25.202-b08, mixed mode)
```



## zookeeper3.6.1安装

### 安装

```sh
su - root
mkdir -p /expoer/soft
#上传
rz apache-zookeeper-3.6.1-bin.tar.gz
#解压
tar -zxvf apache-zookeeper-3.6.1-bin.tar.gz
#重命名
mv apache-zookeeper-3.6.1-bin  zk
```



### 配置

```sh
#新建/expoer/soft/zk/data目录
mkdir data
#修改conf
mv zoo_sample.cfg zoo.cfg
```

在`zoo.cfg`中修改以下内容

```sh
dataDir=/export/soft/zk/data
```



### 验证

```sh
cd ..
sh bin/zkServer.sh start
查看状态
sh bin/zkServer.sh status
查看进程
ps -ef|grep zookeeper
sh bin/zkCli.sh -server 127.0.0.1:2181
```



## Dubbo-admin安装

### 安装

```sh
mkdir -p /expoer/soft/dubbo-admin
cd dubbo-admin
rz dubbo-admin-server-0.2.0-SNAPSHOT.jar
```



### 配置

1. 复制一份 application.properties
2. 修改application.properties里的端口和注册地址

```sh
server.port=9880
admin.registry.address=zookeeper://127.0.0.1:2181
admin.config-center=zookeeper://127.0.0.1:2181
admin.metadata-report.address=zookeeper://127.0.0.1:2181
```

启动

```sh
nohup java -jar dubbo-admin-server-0.2.0-SNAPSHOT.jar --spring.config.location=/ expoer/soft/dubbo-admin/application.properties >> dubbo-damin.log 2>&1 &
```



### 验证

登入dubbo管控台验证：

http://127.0.0.1:9880/



## Mysql5.7.29安装

### 安装

下载地址：https://downloads.mysql.com/archives/community/
下载linux通用版本 mysql-5.7.29-linux-glibc2.12-x86_64.tar.gz

> MySQL Community Server 社区版本，开源免费，但不提供官方技术支持，这也是我们通常用的MySQL的版本。
>
> MySQL Enterprise Edition 企业版本，需付费，可以试用30天。
>
> MySQL Cluster 集群版，开源免费。可将几个MySQL Server封装成一个Server。
>
> MySQL Cluster CGE 高级集群版，需付费。



```sh
su - root
mkdir -p /expoer/soft
#检测系统是否自带mysql
rpm -qa|grep mysql     
#如果有进行强行卸载
rpm -e --nodeps mysql-libs-5.1.52-1.el6_0.1.x86_64
#检测系统是否自带mariadb
rpm -qa|grep mariadb         
#有就卸载
rpm -e --nodeps mariadb-*   
# 解压安装
tar -zxvf mysql-5.7.35-linux-glibc2.12-x86_64.tar.gz
mv mysql-5.7.35-linux-glibc2.12-x86_64  mysql-5.7.35
#安装数据库创建data目录 
mkdir /export/soft/mysql-5.7.35/data
cd /export/soft/mysql-5.7.35/support-files
```

编辑`my_default.cnf`配置文件，修改下面的内容：

```cnf
[mysqld]
basedir =/export/soft/mysql-5.7.35            #设置mysql的安装目录
datadir = /export/soft/mysql-5.7.35/data       #设置mysql数据库的数据存放目录
port = 3306                                  #设置端口
socket = /tmp/mysql.sock
character-set-server=utf8                          #设置字符集
log-error = /export/soft/mysql-5.7.35/data/mysqld.log   #日志存放目录
pid-file = /export/soft/mysql-5.7.35/data/mysqld.pid
#允许时间类型的数据为零(去掉NO_ZERO_IN_DATE,NO_ZERO_DATE)
sql_mode= STRICT_TRANS_TABLES,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
#ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
lower_case_table_names=1     #表、字段等不区分大小写备注：0,区分大小写; 1,不区分
max_connections=1000        #设置最大连接数，默认100
max_user_connections=500
[client]
socket = /tmp/mysql.sock
```

新建mysql用户

```sh
#检查mysql组和用户是否存在，如果没有则创建
cat /etc/group|grep mysql
groupadd mysql
```

> 如果报错 groupadd: cannot open /etc/group
>
> 解决方法使用root用户

```sh
chattr -i /etc/gshadow
chattr -i /etc/group
groupadd mysql
#创建mysql用户
#useradd -r参数表示mysql用户是系统用户，不可用于登录系统
useradd -r -g mysql mysql　
chattr -i /etc/shadow
chattr -i /etc/passwd
useradd -r -g mysql mysql
#将/usr/local/mysql-5.7.35的所有者及所属组改为mysql
chown -R mysql.mysql /export/soft/mysql-5.7.35/
cp my_default.cnf /etc/my.cnf
```

初始化Mysql

```sh
cd bin
./mysqld --initialize --user=mysql --user=mysql --basedir=/export/soft/mysql-5.7.35/ --datadir=/export/soft/mysql-5.7.35/data/
```

初始化完成之后查看日志里的是临时密码

```sh
cat /export/soft/mysql-5.7.35/data/mysqld.log 
```

启动Mysql

```sh
#把启动脚本放到开机初始化目录
cp support-files/mysql.server /etc/init.d/mysql
#启动mysql
service mysql start
# 重启
service mysql restart 或 sudo /etc/init.d/mysql restart
```

### 配置

配置MySQL全局环境变量，编辑`/etc/profile`文件，添加以下的内容

```sh
export MySQL_HOME=/export/middleware/mysql-5.7.35
export PATH=$MySQL_HOME/bin:$PATH
```

刷新/etc/profile配置文件

```sh
source /etc/profile
```

更改root密码

```sh
#进入mysql并更改密码
cd /export/soft/mysql-5.7.35/bin
./mysql -uroot  -h127.0.0.1  -p
# 登入mysql后修改root密码
mysql> set password=password('foo@bar');
mysql> grant all privileges on *.* to root@'%' identified by 'foo@bar';
mysql> flush privileges;
```


创建普通账号

```sh
新建mytest账号 远程登录%
mysql> use mysql;
mysql> CREATE USER 'mytest'@'%'  IDENTIFIED BY 'foo@bar'; 
#修改mysql库的user表，将host项，从localhost改为%。%这里表示的是允许任意host访问，如果只允许某一个ip访问，则可改为相应的ip
mysql> update user set host='%' where user = 'xdkj';
#给mytest授予所有权限
mysql> GRANT ALL ON *.* TO 'mytest'@'%';
#刷新系统权限表
mysql>flush privileges;
mysql>quit;

#查看那些用户可远程登录
mysql> select host ,user from user;
+-----------+---------------+
| host      | user             |
+-----------+---------------+
| %         | root             |
| %         | mytest             |
| localhost | mysql.session     |
| localhost | mysql.sys         |
| localhost | root              |
+-----------+---------------+
5 rows in set (0.00 sec)

#重启mysql
service mysql restart 或 sudo /etc/init.d/mysql restart
```



### 验证

```sh
# 普通账号登入
./bin/mysql -umytest -p
foo@bar
```

新建test数据库

```sh
mysql> CREATE DATABASE IF NOT EXISTS test default charset utf8 COLLATE utf8_general_ci;
mysql> SHOW DATABASES;

mysql> use test;
Database changed
mysql> show tables;
Empty set (0.00 sec)
```



## Redis4.0.11安装

### 安装

```sh
#上传解压
tar -xzvf redis-4.0.11.tar.gz
cd redis-4.0.11
#编译
make    或者 make MALLOC=libc
# 编译成功后进入redis-4.0.11/src 目录 可以看到redis-cli redis-server 等执行文件
cd src
# 这里我们只需要用到两个文件就可以了：redis-server和redis-cli 创建一个工作目录，然后将下述的文件copy到工作目录下
cd ../..
mkdir /expoer/soft/redis-4.0
cp /expoer/soft/redis-4.0.11/src/redis-cli /expoer/soft/redis-4.0
cp /expoer/soft/redis-4.0.11/src/redis-server /expoer/soft/redis-4.0
cp /expoer/soft/redis-4.0.11/redis.conf /expoer/soft/redis-4.0
```



### 配置

```sh
cd /expoer/soft/redis-4.0
```

编辑`redis.conf`文件，添加和修改以下内容：

```conf
bind 0.0.0.0
protected-mode yes
port 6379
tcp-backlog 511
#客户端超时时间
timeout 300
tcp-keepalive 300
#守护模式 后台运行
daemonize yes
supervised no
#密码
requirepass foo@bar
masterauth  foo@bar
pidfile /var/run/redis_6379.pid
loglevel notice
logfile ""
databases 16
always-show-logo yes

save 900 1
save 300 10
save 60 10000

stop-writes-on-bgsave-error yes
rdbcompression yes
rdbchecksum yes
dbfilename dump.rdb
dir ./

slave-serve-stale-data yes
slave-read-only yes
repl-diskless-sync no
repl-diskless-sync-delay 5
repl-disable-tcp-nodelay no
slave-priority 100

lazyfree-lazy-eviction no
lazyfree-lazy-expire no
lazyfree-lazy-server-del no
slave-lazy-flush no
#
appendonly no
appendfilename "appendonly.aof"
appendfsync everysec
no-appendfsync-on-rewrite no
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb

aof-load-truncated yes
aof-use-rdb-preamble no
lua-time-limit 5000
slowlog-log-slower-than 10000
slowlog-max-len 128
latency-monitor-threshold 0

notify-keyspace-events ""
hash-max-ziplist-entries 512
hash-max-ziplist-value 64

list-max-ziplist-size -2

list-compress-depth 0
set-max-intset-entries 512
zset-max-ziplist-entries 128
zset-max-ziplist-value 64
hll-sparse-max-bytes 3000
activerehashing yes
client-output-buffer-limit normal 0 0 0
client-output-buffer-limit slave 256mb 64mb 60
client-output-buffer-limit pubsub 32mb 8mb 60
hz 10
aof-rewrite-incremental-fsync yes
```

启动

```sh
./redis-server redis.conf
# 查看进程
ps –ef|grep redis
```



### 测试

```sh
./redis-cli -a foo@bar
127.0.0.1:6379> set name hello
OK
127.0.0.1:6379> get name
"hello"
```



## Rocketmq4.7.1-安装

### 安装

```sh
# 上传
rz rocketmq-all-4.7.1-bin-release.zip
unzip rocketmq-all-4.7.1-bin-release.zip
mv rocketmq-all-4.7.1-bin-release  rocketmq-4.7.1
cd rocketmq-4.7.1
mkdir log
```

### 配置

启动NameServer 配置自动创建topic

```sh
shnohup sh bin/mqnamesrv  >> log/mqnamesrv.log 2>&1 &
```

启动broker  配置自动创建topic

```sh
nohup sh bin/mqbroker  -n “10.100.12.157:9876” autoCreateTopicEnable=true  >> log/broker.log 2>&1 &
```

启动成功后默认端口 9876

```sh
#使用jps命令查看broker
jps –ml
```

broker内存调整

broker 占用内存过大，如果机器内存不足，可以调整`runbroker.sh`脚本里的内存。

```sh
# 默认
# JAVA_OPT="${JAVA_OPT} -server –Xms8g –Xmx8g –Xmn4g"
# 调整为
JAVA_OPT="${JAVA_OPT} -server -Xms4g -Xmx4g -Xmn2g"
```



### 验证

```sh
# 查看进程
ps -ef|grep mq
```



## Rocketmq控制台安装

### 安装

```sh
# 启动
nohup java -jar rocketmq-console-ng-1.0.1.jar --server.port=9890 --rocketmq.config.namesrvAddr=127.0.0.1:9876 >> console.log 2>&1 &
```

### 验证

访问控制台
http://127.0.0.1:9890/



## Minio安装

### 安装

```sh
#新建minio目录
mkdir minio
cd minio
rz minio
```

### 配置

```sh
#添加执行权限
chmod +x minio
#新建data子目录
mkdir data

#linux 后台指定端口启动
./minio server  --address 0.0.0.0:9000 /export/soft/minio/data >minilog.log &

#查看进程
ps -ef|grep minio

#默认地址账号
Endpoint:  http:// 10.100.12.157:9000/
AccessKey: minioadmin
SecretKey: minioadmin
```



### 验证

浏览器访问
http://127.0.0.1:9000/





## Elasticsearch安装

### 安装

```sh
#解压
unzip elasticsearch-7.6.0.zip               
cd elasticsearch-7.6.0                       
mkdir data
```

### 配置

```sh
cd config  
```

编辑`elasticsearch.yml`文件,添加或修改下面的内容。

```yml
cluster.name: znjc
node.name: node-1
path.data: /export/soft/elasticsearch-7.6.0/data
path.logs: /export/soft/elasticsearch-7.6.0/logs
network.host: 0.0.0.0
http.port: 9200
#单节点开启此项
discovery.type: single-node
# 增加新的参数，这样head插件可以访问es
http.cors.enabled: true
http.cors.allow-origin: "*"
http.max_initial_line_length: "64k"
http.max_header_size: "64k"
#开启密码认证
http.cors.allow-headers: Authorization
xpack.security.enabled: true
xpack.security.transport.ssl.enabled: true
```

启动

```sh
# es不能使用root用户启动
cd elasticsearch-7.6.0/bin
#检查es组和用户是否存在，如果没有则创建
cat /etc/group|grep es
groupadd es
useradd -r -g es es
chown -R es elasticsearch-7.6.0
# 切到es用户
su es
# -d后台启动
bash-4.2$ ./elasticsearch –d
#初始化密码foo@bar
./elasticsearch-setup-passwords interactive
```



### 验证

```sh
 curl --user elastic:foo@bar http://127.0.0.1:9200
{
  "name" : "node-1",
  "cluster_name" : "znjc",
  "cluster_uuid" : "lzB7hS5OTnS00xLbcbcDgQ",
  "version" : {
    "number" : "7.6.0",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "7f634e9f44834fbc12724506cc1da681b0c3b1e3",
    "build_date" : "2020-02-06T00:09:00.449973Z",
    "build_snapshot" : false,
    "lucene_version" : "8.4.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```



## Nginx安装 

```sh                   
# 上传
rz nginx-1.14.2.tar.gz
tar zxvf nginx-1.14.2.tar.gz
#安装依赖
yum install -y gcc gcc-c++ pcre pcre-devel zlib zlib-devel openssl openssl-devel
cd nginx-1.14.2
#指定安装目录
./configure --prefix=/export/soft/nginx
#编译安装
make && make install 
# 进入执行完后出现nginx目录
cd nginx
#检查
./nginx -t
```

### 配置

```sh
cd nginx/conf
```

编辑`nginx.conf`文件。

启动

```sh
# 进入执行目录
cd nginx/sbin
# 启动
./nginx
# 停止
./nginx -s stop
# 重启
./nginx -s reload
```

### 验证

验证
http://127.0.0.1:80/ 



（完）