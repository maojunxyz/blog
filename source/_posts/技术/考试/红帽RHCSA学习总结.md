---
title: 红帽RHCSA学习总结
date: 2022/2/11 16:52
updated: 2022/2/11 16:52
categories:
- [技术, 考试]
tags:
- Linux
- podman
---

## 考试信息

考试时间：3小时
满分：300分
及格分：210分
携带物品：身份证，签字笔(考试会发）

做题注意：
如果是服务，都设置下开机启动`systemctl enable xxx`。 同时设置开启防火墙。 考试是在`/etc/selinuxconfig`为**enforce**下进行的,使用命令`getenforce`可查询状态。
Linux命令如果不存在，使用`yum whatprovides */semanage`,semanage可替换成其他命令。在查询结果中找到*.noarch的包，在使用`yum install *.noarch -y`。
所有编写的脚本都设置x执行权限。
做完题目后重启机器检查。

## 网络设置

- 主机名：node1.domain250.example.com

查看现在的主机名：`hostname`
修改主机名: `hostnamectl set-hostname node1.domain250.example.com`
验证主机名：`hostname`

> *此题还可以通过编辑*`*/etc/hostname*`*来配置，需要重启才能生效*

- IP 地址：172.25.250.100

方法一：
查看现在IP地址：`ifconfig`，记下网卡名称比如:`eth0`
修改IP地址:`nmtui-edit`,删除刚才的网卡名称`eth0`,再点击**Add**按钮添加一个新的连接，选择**Ethernet**， **Profile name**和**Device**填入对应的网卡名称`eth0`,再**IPV4 CONFIGURATION**将**Automatic**修改为**Manual**，然后点击**Show**，在**ADddresses**填入`172.25.250.100/24`,**Gateway**,**DNS servers**依次配置，最后点击**OK**并退出。
验证IP地址：`ifconfig`

> *注意新增无需刷新生效，如果是新修改则需要*`*nmcli device reapply eth0*`*刷新生效*

方法二：
查看网络设置信息：`nmcli connection show`
设置网络：`nmcli connection eth0 ipv4.address 172.25.250.100/24 ipv4.getway 172.25.250.254 ipv4.dns 172.25.250.254 ipv4.method manual`
重启生效:`nmcli connection up eth0`

方法三：
通过vim编辑配置文件 `vim /etc/sysconfig/network-scripts/ifcfg-eth0`

```
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=static
IPADDR=172.25.250.100
PREFIX=24
GATEWAY=172.25.250.254
DNS1=172.25.250.254
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=eth0
UUID=e5d912ab-45ae-4d2d-85fa-0ea82150b3bb
DEVICE=eth0
ONBOOT=yes
```

> *需要刷新网络（考试环境无法刷新）或重启生效*

- 子网掩码：255.255.255.0

无需配置，默认。
验证:`ifconfig`

- 网关：172.25.250.254

同IP配置。
验证:`ifconfig`

- DNS服务器：172.25.250.254

同IP配置。
验证：`netstat -nr`或`route -n`或`nslookup node1`

## 配置您的系统以使用默认存储库

YUM 存储库已可以从 http://foundation0.ilt.example.com/dvd/BaseOS 和 http://foundation0.ilt.example.com/dvd/AppStream 使用配置您的系统，以将这些位置用作默认存储库

进入`/etc/yum.repos.d`，任意起名一个文件，以`.repo`结尾，在文件中增加以下配置内容:

```
[BaseOS]
name=BaseOS
baseurl=http://foundation0.ilt.example.com/dvd/BaseOS
enabled=1
gpgcheck=0[AppStream]
name=AppStream
baseurl=http://foundation0.ilt.example.com/dvd/AppStream
enabled=1
gpgcheck=0
```

检查：

```
yum clean all
yum repolist all
```

能在命令行输出中看到配置的两个仓库地址即配置成功。

> *可以使用*`*yum list all*`*来校验仓库地址是否有配错*

## 调试 SELinux

非标准端口 82 上运行的 Web 服务器在提供内容时遇到问题。根据需要调试并解决问题，使其满足以下条件：

- 系统上的 Web 服务器能够提供 /var/www/html 中所有现有的 HTML 文件（注：不要删除或以其他方式改动现有的文件内容）
- Web 服务器在端口 82 上提供此内容
- Web 服务器在系统启动时自动启动

82端口启动失败，发现是semanage中并没有加入http的82端口

```
semanage port -l |grep http
http_cache_port_t              tcp      8080, 8118, 8123, 10001-10010
http_cache_port_t              udp      3130
http_port_t                    tcp      80, 81, 443, 488, 8008, 8009, 8443, 9000
pegasus_http_port_t            tcp      5988
pegasus_https_port_t           tcp      5989
```

添加82端口：

```
semanage port -a -t http_port_t  -p tcp 82
```

如果配置错了，可以使用修改命令：

```
semanage port -m -t http_port_t  -p tcp 82
```

> *semanager可以参考
> grep semanage /etc/ssh/sshd_config
> \#semanage port -a -t ssh_port_t -p tcp #PORTNUMBER*

开启访问墙端口放行：

```
firewall-cmd --add-port=82/tcp --permanent
# reload
firewall-cmd --reload
```

> *查看httpd端口*`*vim /etc/httpd/conf/httpd.conf*`*
> 检查httpd服务状态:*`*systemctl status httpd*`*
> 启动httpd服务:*`*systemctl start httpd*`*
> 重启httpd服务:*`*systemctl restart httpd*`*
> 开启启动httpd:*`*systemctl enable httpd*`

验证：

```
semanage port -l |grep http
```

在http里面查看到82端口即可，同时curl访问文件有返回内容`curl 127.0.0.1:82/file`

额外的练习： 以下内容考试不需要做:

查看静态资源文件下的file1文件权限：

```
ls -Zd /var/www/html/*
              system_u:object_r:etc_t:s0 /var/www/html/file1
system_u:object_r:httpd_sys_content_t:s0 /var/www/html/file2
system_u:object_r:httpd_sys_content_t:s0 /var/www/html/file3
```

修改file1的文件权限同file2和file3:

```
semanage fcontext -a -t httpd_sys_content_t /var/www/html/file1
# 刷新配置
restorecon -R -v /var/www/html/
```

## 创建用户帐户

创建下列用户、组和组成员资格：

- 名为 sysmgrs 的组

```
groupadd sysmgrs
```

验证:

```
grep sysmgrs /etc/group
```

- 用户 natasha ，作为次要组从属于 sysmgrs

```
useradd -G sysmgrs natasha
```

验证：

```
id natasha
```

- 用户 harry ，作为次要组还从属于 sysmgrs

```
useradd -G sysmgrs harry
```

验证：

```
id harry
```

- 用户 sarah ，无权访问系统上的交互式 shell 且不是 sysmgrs 的成员

```
useradd  sarah -s /sbin/nologin
```

> *非交互式shell可以是*`*/bin/false*`*或*`*/sbin/nologin*`

验证：
sarah无法使登入ssh或者查看shell: `grep sarah /etc/passed`

- natasha 、 harry 和 sarah 的密码应当都是 flectrag

```
echo flectrag | passwd --stdin natasha
echo flectrag | passwd --stdin harry
echo flectrag | passwd --stdin sarah
```

验证：
natasha和harry可以使用密码登入ssh

## 配置 cron 作业

配置 cron 作业，该作业每隔 2 分钟运行并执行以下命令:
logger “EX200 in progress”，以用户 natasha 身份运行

```
crontab -u natasha -e
*/2 * * * * logger "EX200 in progress"
```

验证：

```
grep EX200 /var/log/messages
```

## 创建协作目录

创建具有以下特征的协作目录 /home/managers ：

- /home/managers 的组用权是 sysmgrs

```
chgrp sysmgrs /home/managers
```

- 目录应当可被 sysmgrs 的成员读取、写入和访问，但任何其他用户不具这些权限。（当然，root 用户有权访问系统上的所有文件和目录）

```
chmod g+rw,o-rw /home/managers
```

- /home/managers 中创建的文件自动将组所有权设置到 sysmgrs 组

```
chmod g+s /home/managers
```

验证:

```
ls -ld /home/managers
```

去`/home/managers`中创建文件，查看文件属性，确认后删除该文件。

## 配置 NTP

配置您的系统，使其成为 materials.example.com 的 NTP 客户端。（注：materials.example.com 是 classroom.example.com 的 DNS 别名）

```
vim /etc/chrony.conf
# 添加配置
server materials.example.com iburst
# 重启服务
systemctl restart chronyd
# 开机启动
systemctl enable chronyd
```

验证:

```
chronyc sources
```

## 配置 autofs

配置 autofs ，以按照如下所述自动挂载远程用户的主目录：

- materials.example.com ( 172.25.254.254 ) NFS 导出 /rhome 到您的系统。此文件系统包含为用户 remoteuser1 预配置的主目录
- remoteuser1 的主目录是 materials.example.com:/rhome/remoteuser1
- remoteuser1 的主目录应自动挂载到本地 /rhome 下的 /rhome/remoteuser1
- 主目录必须可供其用户写入
- remoteuser1 的密码是 flectrag

```
#新建编辑
vim /etc/auto.master.d/rhcsa.autofs
#添加以下内容
/rhome /etc/rhcsa.misc
#新建编辑/etc/rhcsa.misc
vim /etc/rhcsa.misc
remoteuser1     -fstype=nfs,rw    materials.example.com:/rhome/remoteuser1
```

> *注意/rhome/remoteuser1 如果有多级目录如/rhome/b/c/remoteuser1，则需要在rhcsa.autofs配置多级/rhome/a/b。*

重启配置：

```
systemctl restart autofs
```

开机启动：

```
systemctl enable autofs
```

验证：
`su - remoteuser1` 或者`cd /rhome/remoteuse1`

## 配置 /var/tmp/fstab 权限

将文件 /etc/fstab 复制到 /var/tmp/fstab 。配置 /var/tmp/fstab 的权限以满足如下条件：

```
cp -a /etc/fstab /var/tmp/fstab
```

- 文件 /var/tmp/fstab 自 root 用户所有
  无需修改
- 文件 /var/tmp/fstab 属于组 root
  无需修改
- 文件 /var/tmp/fstab 应不能被任何人执行
  无需修改
- 用户 natasha 能够读取和写入 /var/tmp/fstab
  `setfacl -m u:natasha:rw /var/tmp/fstab`
- 用户 harry 无法写入或读取 /var/tmp/fstab
  `setfacl -m u:harry:- /var/tmp/fstab`
- 所有其他用户（当前或未来）能够读取 /var/tmp/fstab
  无需修改

验证：

```
#查看文件信息
ls -ld /var/tmp/fstab
# 验证文件权限
getfacl /var/tmp/fstab
```

> *查看帮助里面的例子：*`*man setfacl |grep -A8 -i example*`

## 配置用户帐号

配置用户 manalo ，其用户 ID 为 3533。此用户的密码应当为 flectrag。

```
# 创建用户并设置uid
useradd -u 3533 manalo
# 设置密码
echo flectrag | passwd --stdin manalo
```

验证：

查看uid:`id manalo`
验证密码:`ssh manalo @localhost`

或者使用远程执行ssh命令验证:`ssh manalo@localhost id`

## 查找文件

查找当 jacques 所有的所有文件并将其副本放入 /root/findfiles 目录。

```
find / -user jacques -exec cp -a {} /root/findfiles/ \;
```

验证:

```
ls -l /root/findfiles
```

找出/etc目录下大小超过5MB的文件，并将其副本放入 /root/findfiles 目录。

```
find /etc/ -size +5M -exec cp -a {} /root/findfiles/ \;
```

验证:

```
ls -l /root/findfiles
```

## 查找字符串

查找文件 /usr/share/xml/iso-codes/iso_639_3.xml 中包含字符串 ng 的所有行。将所有这些行的副本按原始顺序放在文件 /root/list 中。 /root/list 不得包含空行，且所有行必须是 /usr/share/xml/iso-codes/iso_639_3.xml 中原始行的确切副本。

```
grep ng /usr/share/xml/iso-codes/iso_639_3.xml > /root/list
```

验证：

```
cat /root/list
```

## 创建存档

创建一个名为 /root/backup.tar.gz 的 tar 存档，其应包含 /usr/local 的 tar 存档，其应包含 /usr/local 的内容。该 tar 存档必须使用 gzip 进行压缩。

```
tar -zcvf /root/backup.tar.gz /usr/local
```

验证：

```
# 查看文件类型
file /root/backup.tar.gz
# 查看压缩包内容
tar -tf /root/backup.tar.gz
```

> *gzip压缩或bzip2压缩查阅参数*`*man tar |egrep 'gzip|bzip2'*`

## sudo提权

配置sudo提权，允许admins组的成员可以以root身份执行命令，且不需要输入密码。

```
# 复制模板
grep wheel /etc/sudoers > /etc/sudoers.d/admins
# 编辑/etc/sudoers.d/admins内容
%admins ALL=(ALL)       NOPASSWD: ALL
```

## 密码过期

设置新建用户的密码20天过期。

```
# 编辑配置文件
vim /etc/login.defs
# 修改密码20天过期
PASS_MAX_DAYS   20
```

## 配置容器使其自动启动

利用注册服务器上的 rsyslog 镜像，创建一个名为 logserver 的容器 这两道容器题目需要结合来做，根据步骤来做题。

- 面向 wallah 用户，配置一个 systemd 服务

```
# 步骤四
# 创建systemd 用户目录
mkdir -p ~/.config/systemd/user
# 进入systemd 用户目录
cd .config/systemd/user/
```

- 该服务命名为 container-logserver ，并在系统重启时自动启动，无需干预

```
# 步骤五
# 停止容器服务
podman stop logserver
# 检查 systemd 控制权的状态
loginctl enable-linger wallah
loginctl show-user wallah
# 生成systemd服务
podman generate systemd -n logserver  -f
# 开机启动
systemctl --user enable --now container-logserver.service
# 查看状态
systemctl --user status container-logserver
```

> *注意题目描述服务命名为 container-logserver，这里创建时不需要额外加container，直接使用logserver创建后会自动加上。*

验证：

```
# 查看Linger=no是否变为yes
loginctl show-user wallah
# 查看是否开机启动
systemctl --user is-enabled  container-logserver.service
```

> *如果重新生成了systemc服务，并且是同名的。则使用*`*systemctl --user daemon-reload*`*刷新生效*

## 为容器配置持久存储

通过以下方式扩展上一个任务的服务

- 配置主机系统的 journald 日志以在系统重启后保留数据，并重新启动日志记录服务

```
# 步骤一# 先查看所需要的所有者和所属组
ll -d /run/log/journal
# 检查目录/var/log/journal 是否存在，没有就创建
mkdir /var/log/journa
# 和/run/log/journal一样的用户和组权限
chown root:systemd-journal /var/log/journal
chmod 2755 /var/log/journa
# 重启日志记录服务systemctl 
restart systemd-journald
# 设置容器永久化
vim /etc/systemd/journald.conf
# 将Storage=auto内容修改为Storage=persistent
```

- 将主机 /var/log/journal目录下任何以 *.journal 的文件复制到 /home/wallah/container_logfile 中

```
# 步骤二
# 将日志拷贝到指定目录
cp -a /var/log/journal/*/*.journal /home/wallah/container_logfile/
# 修改文件权限
chown -R wallah /home/wallah/
# 修改wallah密码
echo flectrag | passwd --stdin wallah
```

- 将服务配置为在启动时自动将 /home/wallah/container_logfile 挂载到容器中的 /var/log/journal 下

```
# 步骤三
# ssh到wallah
ssh wallah @node1
# 登入镜像仓库podman login registry.domain250.example.com
# 拉取镜像podman pull rhel8/rsyslog
# 启动容器并挂载目录podman run -d --name logserver -v /home/wallah/container_logfile:/var/log/journal:Zregistry.domain250.example.com/rhel8/rsyslog
```

> *登入时提示错误*Error: error authenticating creds for “registry.domain250.example.com”: error pinging docker registry registry.domain250.example.com: Get https://registry.domain250.example.com/v2/: x509: certificate has expired or is not yet valid*，解决思路：* `*man podman login |grep -A8 certificates*`

验证：

```
# 在容器里执行命令查看日志文件podman exec logserver ls /var/log/journal
```

## 设置 root 密码

将 node2 的 root 密码设置为 flectrag 。您需要获得系统访问权限才能进行此操作。 这道题是在不知道node2密码的前提下设置root密码。

关机，开机（使用重启时来不及打开控制台就进入系统了）。在第一个选项中输入e，进入编辑模式，在**Linux**开头的那行ctrl+e，进入末尾，空格，输入`rd.break`,然后按`ctrl+x`，等待进入**switch_root**终端界面后,在终端输入命令:

```
mount -o remount,rw /sysroot/
chroot /sysroot/
# 修改root密码
echo flectrag | passwd --stdin root
touch /.autorelabel
exit
exit
```

## 预先创建2G的分区/dev/vdb1,并用于创建卷组testvg

提示: lvm结构 从下到上 pp pv vg lg

```
# 创建pp
# 查看块设备
lsblk
# 分区
fdisk /dev/vdb
# 进入fdisk交互界面
# 查看分区信息
p
# 增加新分区
n
# 添加主分区(p）或扩展分区(e)
p
# 分区信息，前2个默认即可,直接回车
Partition number (1,2, default 1):
First sector (2095106-8388607, default 2097152):
# 设置结束分区大小
Last sector, +sectors or +size{K,M,G,T,P} (2097152-8388607, default 8388607): +2G
# 保存
w
# 再次查看块设备
lsblk
# 创建pv
pvcreate /dev/vdb1
Physical volume "/dev/vdb1" successfully created.
#查看
pvs
# 创建vg
vgcreate testvg /dev/vdb1
Volume group "testvg" successfully created
# 查看
vgs
```

## 创建大小为200M的逻辑卷/dev/testvg/vo,格式化为xfs文件系统，并挂载在/mnt/vo上

```
# 创建lv
lvcreate -L 200M -n vo testvg
Logical volume "vo" created.
# 查看
lvs
# 格式化
mkfs.xfs /dev/testvg/vo
#查看块信息
blkid
/dev/mapper/testvg-vo: UUID="61414b60-5003-48ed-94ec-14a6a7d69b67" TYPE="xfs"
# 永久挂载
vim /etc/fstab
# 添加以下内容
dev/mapper/testvg-vo /mnt/vo xfs defaults        0 0
# 刷新挂载
mount -a
# 查看
df -hT
```

## 设置逻辑卷大小

将逻辑卷 vo 及其文件系统的大小调整到 230 MiB。确保文件系统内容保持不变。注：分区大小很少与请求的大小完全相同，因此可以接受范围为 217 MiB 到 243 MiB 的大小。

```
# ext4
# 查看volo逻辑卷
df -hT |grep vo
/dev/mapper/myvol-vo ext4      175M  1.6M  160M   1% /reports
# 扩展逻辑卷
lvextend -L 230M /dev/mapper/myvol-vo
# 应用扩展(ext4格式),后面跟随逻辑卷的路径
resize2fs /dev/mapper/myvol-vo
# 如果是xfs格式
# xfs_growfs /reports
# xfs
df -hT |grep vo
/dev/mapper/testvg-vo xfs       195M   12M  184M   6% /mnt/vo
lvextend -L 300M /dev/mapper/testvg-vo
# xfs后面跟随挂载点路径
xfs_growfs /mnt/vo
```

> *注意：*`*lvextend -L 230M /dev/mapper/myvol-vo*`*和*`*resize2fs /dev/mapper/myvol-vo*`*可以合并为一步*`*lvextend -L 230M -r /dev/mapper/myvol-vo*`

## 添加交换分区

向您的系统添加一个额外的交换分区 756MiB 。交换分区应在系统启动时自动挂载。不要删除或以任何方式改动系统上的任何现有交换分区。

```
# 做题要先lsblk查看块设备，能在同个块上做的就在同块上做，保留完整的块做后面的题
# 查看块设备，找到符合大小的块设备
lsblk
fdisk /dev/vdb
# 新建分区
n
# 划分分区大小
Partition number (3,4, default 3):
First sector (2095106-8388607, default 2097152):
Last sector, +sectors or +size{K,M,G,T,P} (2097152-8388607, default 8388607): +756M
# 转化分区
t
# 选择分区3
Partition number (1-3, default 3): 3
# 选择swap(82  Linux swap / So)
Hex code (type L to list all codes): 82
# 保存
w
partprobe /dev/vdb
# 格式化分区
mkswap /dev/vdb3
# 编辑永久挂载
vim /etc/fstab
/dev/vdb3 none swap defaults 0 0
# 刷新fstab挂载
swapon -a
swapon -s
```

## 创建逻辑卷

根据如下要求，创建新的逻辑卷：

- 逻辑卷取名为 qa ，属于 qagroup 卷组，大小为 60 个扩展块
- qagroup 卷组中逻辑卷的扩展块大小应当为 16 MiB
- 使用 ext3 文件系统格式化新逻辑卷。该逻辑卷应在系统启动时自动挂载到 /mnt/qa 下

```
fdisk /dev/vdb
# 新建分区
n
# 划分分区大小
Selected partition 4
First sector (2095106-8388607, default 3645440):
Last sector, +sectors or +size{K,M,G,T,P} (3645440-8388607, default 8388607): +1200M
# 转化分区
t
# 选择分区3
Partition number (1-3, default 3): 3
# 选择LVM(Linux LVM)
Hex code (type L to list all codes): 8e
# 保存
w
partprobe /dev/vdb
# 创建物理卷
pvcreate /dev/vdb4
# 创建卷组
vgcreate -s 16 qagroup /dev/vdb4
# 创建逻辑卷
lvcreate -l 60 -n qa qagroup
# 格式化exe3
mkfs.ext3 /dev/qagroup/qa
# 创建挂载目录
mkdir /mnt/qa
# 配置永久挂载点
vim /etc/fstab
/dev/qagroup/qa /mnt/qa  ext3 defaults 0 0
# 刷新fstab
mount -a
```

## 创建 VDO 卷

根据如下要求，创建新的 VDO 卷：

- 使用未分区的磁盘
- 该卷的名称为 vdough
- 该卷的逻辑大小为 50G
- 该卷使用 xfs 文件系统格式化
- 该卷（在系统启动时）挂载到 /vbread 下

```
# 搜索vod的包
yum list *vdo*
# 在找到的软件包中安装这2个包名
yum -y install kmod-kvdo.x86_64  vdo.x86_64
# 查看是否有服务了
systemctl list-unit-files |grep vod
# 查看服务状态
systemctl status vdo
# 启动服务
systemctl start vdo
# 利用帮助找选项
vdo create --help |grep Size
# 根据题目创建vdo
vdo create --name=vdough --device=/dev/vdc --vdoLogicalSize=50G
# 快速格式化xfs
mkfs.xfs -K /dev/mapper/vdough
# 创建挂载目录
mkdir /vbread
# 配置永久挂载文件
vim /etc/fstab
# 如果不配置_netdev或写错了，系统会启动不起来，并进入emergency模式
/dev/mapper/vdough /vbread  xfs  defaults,_netdev  0 0
# 刷新fstab
mount -a
# 查看
df -hT
```

> *利用帮助:* `*man vdo |grep create*` *和*`*man vdo |grep defaults*`

## 配置系统调优

为您的系统选择建议的 tuned 配置集并将它设为默认设置。

```
# 查看系统推荐的配置
tuned-adm recommend
virtual-guest
# 查看目前使用的配置
tuned-adm active
Current active profile: throughput-performance
# 设置推荐的配置
tuned-adm profile virtual-guest
# 验证修改后的配置
tuned-adm active
Current active profile: virtual-guest
```

## 删除逻辑卷

1. 卸载文件系统，并删除/etc/fstab中的记录

```
unmount /dev/vg0/1v01
```

1. 删除逻辑卷

```
lvremove /dev/vg0/1v01
```

1. 删除卷组

```
vgremove vg0
```

1. 删除物理卷

```
pvremove /dev/sdb1 /dev/db2
```

## 查找拷贝

在/usr/bin 目录下创建一个 repwis 脚本，查找/usr 目录大小范围在 10–50K 之间并且组 id 不为 root 的文件且拥有 sgid 的文件，把查到的文件结果拷贝到/root/myfiles文件夹内。

```
#/bin/bash
if [ ! -d /root/myfiles ]; then
mkdir /root/myfiles
fi
find /usr -type f -a \( -size +10k -size -50k \) -a \( ! -group root \) -a \( -perm /2000 \) -exec cp -a {} /root/myfiles \;
```

## 欢迎语

登录到普通用户后提示一句欢迎语’hello word !’或者用户在登陆前显示欢迎语提示。

```
# 配置登入后的欢迎语（登入系统输入账号后）
echo 'hello word !' >> /etc/motd
# 配置登入前的欢迎语（登入系统输入账号前）
echo 'hello word !' >> /etc/issue
```

## 查找文件

在/usr/bin 目录下创建一个 repwis 脚本，查找/usr/目录下小于 10M 并且组 id 不为 root 的文 件，把查找到的文件放在/root/myfiles 文件夹内

```
find /usr -type f -a  \( -size -10M \) -a \( ! -group root \)  -exec cp -a {} /root/myfiles \;
```

## 查找文件

在/usr/bin 目录下创建一个 repwis 脚本，查找/usr/ 具有粘贴位是 4（u 的位置上有 s 位）， 其他 ugo 是什么权限， 不管。且组 id 不为 root 的文件，把查找到的文件放在/root/myfiles文件夹内

```
find /usr -type f -a \( -perm /4000 \) -a \( ! -group root  \) -exec cp -a {} /root/myfiles \;
```

## 权限设置

对 diff 用户设置默认权限：

- 用户新创建的文件权限都为：444 r — r — r —
- 同一个用户下新创建的目录权限为：554 dr-xr-xr —

如果既要求了文件的默认权限 xxx，也要求了目录的默认权限 yyy。
则 umask 的值以目录来算，即 umask=777-yyy，不用管文件。
比如目录的默认权限为 554，文件的默认权限为 444。
则 umask 可以设置为 777–554=223，不用管文件的默认权限了。
如果题目只要求设置文件的默认权限 xxx，并没有说目录的默认权限是什么。
则 umask=666-xxx
算出来 umask 值为 zzz 之后。
用户 vim ~用户/.bash_profile，添加 umask zzz 这里 zzz 是你算出来的数字。

## 打印输出

在节点上使用 bob 用户写一个脚本 rhcsa，然后执行 rhcsa,执行后输出**it is ok**

```
# 查看用户的环境变量
echo $PATH
/home/bob/.local/bin:/home/bob/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin
mkdir /home/bob/bin
# 编辑脚本
vim /home/bob/bin/rhcsa
#/bin/bash
echo "it is ok"
# 设置可执行权限
chmod +x /home/bob/bin/rhcsa
```
