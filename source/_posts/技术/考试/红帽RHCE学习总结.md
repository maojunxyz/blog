---
title: 红帽RHCE学习总结
date: 2022/2/19 6:52
updated: 2022/2/19 6:52
categories:
- [技术, 考试]
tags:
- Linux
- Ansible
---

## 考试信息
考试时间：4小时  
总分：300分  
及格分：210  


### 安装和配置 Ansible
按照下方所述，在控制节点 172.25.250.200 上安装和配置 Ansible：  
安装所需的软件包  
创建名为 /home/greg/ansible/inventory 的静态清单文件，以满足以下要求:
- 172.25.250.9 是 dev 主机组的成员
- 172.25.250.10 是 test 主机组的成员
- 172.25.250.11 和 172.25.250.12 是 prod 主机组的成员
- 172.25.250.13 是 balancers 主机组的成员
- prod 组是 webservers 主机组的成员  
创建名为 /home/greg/ansible/ansible.cfg 的配置文件，以满足以下要求：  
- 主机清单文件为 /home/greg/ansible/inventory
- playbook 中使用的角色的位置包括 /home/greg/ansible/roles

```sh
# 安装ansible
yum -y install ansible
# 创建目录
mkdir /home/greg/ansible/
# 配置清单
vim /home/greg/ansible/inventory
[dev]
172.25.250.9

[test]
172.25.250.10

[prod]
172.25.250.11
172.25.250.12

[balancers]
172.25.250.13

[webservers:children]
prod
# 创建配置文件
vim /home/greg/ansible/ansible.cfg
[defaults]
inventory      =/home/greg/ansible/inventory 
roles_path    = /home/greg/ansible/roles

[privilege_escalation]
become=True
become_method=sudo
become_user=root
become_ask_pass=False
```

验证:

```sh
[root@bastion ansible]# ansible-inventory --graph
@all:
  |--@balancers:
  |  |--172.25.250.13
  |--@dev:
  |  |--172.25.250.9
  |--@test:
  |  |--1782.25.250.10
  |--@ungrouped:
  |--@webservers:
  |  |--@prod:
  |  |  |--172.25.250.11
  |  |  |--172.25.250.12

```

### 创建和运行 Ansible 临时命令
作为系统管理员，您需要在受管节点上安装软件。  
请按照正文所述，创建一个名为 /home/greg/ansible/adhoc.sh 的 shell 脚本，该脚本将使用Ansible 临时命令在各个受管节点上安装 yum 存储库：

存储库 1：  
- 存储库的名称为 EX294_BASE
- 描述为 EX294 base software
- 基础 URL 为 ftp://host.domain8.example.com/dvd/BaseOS
- GPG 签名检查为启用状态
- GPG 密钥 URL 为 ftp://host.domain8.example.com/dvd/RPM-GPG-KEY-redhat-release
- 存储库为启用状态

存储库 2：  
- 存储库的名称为 EX294_STREAM
- 描述为 EX294 stream software
- 基础 URL 为 ftp://host.domain8.example.com/dvd/AppStream
- GPG 签名检查为启用状态
- GPG 密钥 URL 为 ftp://host.domain8.example.com/dvd/RPM-GPG-KEY-redhat-release
- 存储库为启用状态

```sh
# 配置脚本
vim /home/greg/ansible/adhoc.sh
#!/bin/bash

ansible all -m yum_repository -a "name=EX294_BASE description='EX294 base software' baseurl=ftp://host.domain8.example.com/dvd/BaseOS enabled=yes gpgcheck=yes gpgkey=ftp://host.domain8.example.com/dvd/RPM-GPG-KEY-redhat-release"

ansible all -m yum_repository -a "name=EX294_STREAM description='EX294 stream software' baseurl=ftp://host.domain8.example.com/dvd/AppStream enabled=yes gpgcheck=yes gpgkey=ftp://host.domain8.example.com/dvd/RPM-GPG-KEY-redhat-release"
# 添加可执行权限
chmod +x /home/greg/ansible/adhoc.sh
# 执行(需要进入执行目录，在外面的目录依旧是ansible默认配置）
 /home/greg/ansible/
 ./adhoc.sh
```

> 提示：查询模块：`ansible-doc -l |grep yum`  查询模块帮助：`ansible-doc yum_repository`

验证：  


```sh
ansible all -m shell -a "yum repolist all"
```

### 安装软件包

创建一个名为 /home/greg/ansible/packages.yml 的 playbook :  
- 将 php 和 mariadb 软件包安装到 dev、test 和 prod 主机组中的主机上
- 将 RPM Development Tools 软件包组安装到 dev 主机组中的主机上
- 将 dev 主机组中主机上的所有软件包更新为最新版本

```sh
# 编辑playbook
vim /home/greg/ansible/packages.yml

---
- hosts: dev,test,prod
  tasks:
  - name: install php mariadb
    yum: name=php,mariadb state=present

- hosts: dev
  tasks:
  - name: rmp grpup
    yum: name="@RPM Development Tools" state=present

  - name: upgrade software
    yum: name='*' state=latest

# 检查playbook
ansible-playbook /home/greg/ansible/packages.yml --syntax-check
# 执行playbook
ansible-playbook /home/greg/ansible/packages.yml

```
> 查看软件包组：`yum grouplist`

### 使用 RHEL 系统角色

安装 RHEL 系统角色软件包，并创建符合以下条件的 playbook /home/greg/ansible/timesync.yml ：  
- 在所有受管节点上运行
- 使用 timesync 角色
- 配置该角色，以使用当前有效的 NTP 提供商
- 配置该角色，以使用时间服务器 172.25.250.250
- 配置该角色，以启用 iburst

```sh
#　搜索和安装rhel角色软件包
yum search *rhel*roles*
yum -y install rhel-system-roles.noarch
# 复制角色
cp -r /usr/share/ansible/roles/rhel-system-roles.timesync/ ~/ansible/roles/timesync
# 创建playbook
vim  /home/greg/ansible/timesync.yml

--- 
- hosts: all 
  vars: 
   timesync_ntp_servers: 
   - hostname: 172.25.250.250 
     iburst: yes 
     pool: yes 
  roles: 
  - role: timesync 
```
> 备注：可以使用`rpm -ql rhel-system-roles`查看安装包的文件。playbook可以从路径复制：
> /usr/share/doc/rhel-system-roles/timesync/example-timesync-playbook.yml

验证：  

在任意一台受控节点上查看:  

```sh
cat /etc/chrony.conf
```

### 使用 Ansible Galaxy 安装角色

使用名字为文件 /home/greg/ansible/roles/requirements.yml 下载并安装 Ansible Galaxy 角
色。从下列 URL 下载 ：  
- http://host.domain8.example.com/ex300/haproxy.tar.gz 此角色的名称应当为 balancer
- http://host.domain8.example.com/ex300/phpinfo.tar.gz 此角色的名称应当为 phpinf

```sh
# 配置yml文件
vim /home/greg/ansible/roles/requirements.yml

- src: http://host.domain8.example.com/ex300/haproxy.tar.gz
  name: balancer
- src: http://host.domain8.example.com/ex300/phpinfo.tar.gz
  name: phpinfo
  
# 安装角色
ansible-galaxy install -r roles/requirements.yml -p roles
```

> r:指定role文件，p:指定roles路径

### 创建和使用角色

根据下列要求，在 /home/greg/ansible/roles 中创建名为 apache 的角色：
- httpd 软件包已安装，设为在系统启动时启用并启动
- 防火墙已启用并正在运行，并使用允许访问 Web 服务器的规则
- 模板文件为 index.html.j2 ，用于创建具有以下输出的文件 /var/www/html/index.html ：
Welcome to HOSTNAME on IPADDRESS
- 其中，HOSTNAME 是受管节点的完全限定域名，IPADDRESS 则是受管节点的 IP 地址。 
- 创建名为 newroles.yml 的 playbook，使用这个角色，对 webservers 组进行操作。
- HOSTNAME 是 FQDN 的完整主机名

```sh
# 创建角色
ansible-galaxy init roles/apache
# 编辑角色task
vim roles/apache/tasks/main.yml

---
# tasks file for apache
- name: install httpd
  yum: name=httpd state=installed

- name: start httpd
  service: name=httpd state=started enabled=yes

- name: start firewalld
  service: name=firewalld state=started enabled=yes

- name: open http firewalld
  firewalld: service=http immediate=yes permanent=yes state=enabled

- name: copy jinja2
  template: src=index.html.j2 dest=/var/www/html/index.html

# 创建模板
vim roles/apache/templates/index.html.j2

Welcome to {{ansible_fqdn}} on {{ansible_default_ipv4.address}}

# 创建playbook
vim newroles.yml 

---
- hosts: webservers
  roles:
  - role: apache
  
# 使用playbook
ansible-playbook newroles.yml 
```

> 变量帮助： `ansible dev -m setup | grep fqdn`和`ansible dev -m setup | grep defaul`

验证：  
	登入webserver下的机器，查看var/www/html/index.html。  
   在控制端curl被控端80端口，有响应和输出内容。

### 从 Ansible Galaxy 使用角色

根据下列要求，创建一个名为 /home/greg/ansible/roles.yml 的 playbook ：
- playbook 中包含一个 play，该 play 在 balancers 主机组中的主机上运行并将使用 balancer
角色。
- 此角色配置一项服务，以在 webservers 主机组中的主机之间平衡 Web 服务器请求的负载。
- 浏览到 balancers 主机组中的主机（例如 http://172.25.250.13 ）将生成以下输出：
Welcome to serverc.domain8.example.com on 172.25.250.11
- 重新加载浏览器将从另一 Web 服务器生成输出：
Welcome to serverd.domain8.example.com on 172.25.250.12

playbook 中包含一个 play，该 play 在 webservers 主机组中的主机上运行并将使用 phpinfo
角色。
- 请通过 URL /hello.php 浏览到 webservers 主机组中的主机将生成以下输出：
Hello PHP World from FQDN
- 其中，FQDN 是主机的完全限定名称。
Hello PHP World from serverb.lab.example.com
- 另外还有 PHP 配置的各种详细信息，如安装的 PHP 版本等。
- 同样，浏览到 http://172.25.250.12/hello.php 会生成以下输出：
Hello PHP World from serverc.lab.example.com
- 另外还有 PHP 配置的各种详细信息，如安装的 PHP 版本等。


```sh
# 创建playbook
vim  /home/greg/ansible/roles.yml

---
- hosts: all

- hosts: balancers
  roles:
  - role: balancer

- hosts: webservers
  roles:
  - role: phpinfo

# 使用playbook
ansible-playbook roles.yml
```

验证： 

```sh
# 负载均衡
curl 172.25.250.1
# 内容验证
firefox打开
http://172.25.250.11/hello.php
http://172.25.250.12/hello.php
```


### 创建和使用逻辑

创建一个名为 /home/greg/ansible/lv.yml 的 playbook ，它将在所有受管节点上运行以执行下列任务：  
创建符合以下要求的逻辑卷：  
- 逻辑卷创建在 research 卷组中
- 逻辑卷名称为 data
- 逻辑卷大小为 1500 MiB
- 使用 ext4 文件系统格式化逻辑卷
- 如果无法创建请求的逻辑卷大小，应显示错误信息 Could not create logical volume of that
size，并且应改为使用大小 800 MiB。
- 如果卷组 research 不存在，应显示错误信息 Volume group done not exist。
- 不要以任何方式挂载逻辑卷

```sh
# 创建playbook
vim /home/greg/ansible/lv.yml

---
- hosts: all
  tasks:
  - name: judge research exit
    shell: "vgdisplay research"
    ignore_errors: yes
    register: aa
  - name: if not exit print err
    fail: msg="Volume group done not exis"
    when: aa.rc != 0

  - name: create lv
    block:
    - name: create 1500M lv
      lvol: vg=research lv=data size=1500M
    rescue:
    - name: debug 1500 Not create
      debug: msg="Could not create logical volume of that size"
    - name: create 800M lv
      lvol: vg=research lv=data size=800M

  - name: mk filesystem ext4
    filesystem: dev=/dev/research/data fstype=ext4 force=yes

# 执行playbook
ansible-playbook lv.yml
```

验证：  

到受控机器上通过`vgs`和`lvscan`查看。





### 创建并使用磁盘分区

创建名为 partition.yml 的 playbook，对所有的节点进行操作：  
- 在 vdb 上创建一个主分区， 1500MiB
- 使用 ext4 文件系统进行格式化
- 将文件系统挂载到/newpart
- 如果分区大小不满足，产生报错信息： could not create partation of that size
则创建分区大小变成 800MiB
- 如果磁盘不存在，产生报错信息： disk does not exit

```sh
# 创建playbook
vim /home/greg/ansible/partition.yml

---
- hosts: diskgroup
  tasks:
  - name: judge vdb exist
    shell: "ls /dev/vdb"
    ignore_errors: yes
    register: aa

  - name: if vbd Not exist print a err info
    fail: msg="disk does not exist"
    when: aa.rc != 0

  - name: create a partitio
    block:
    - name: create 1500M
      parted: device=/dev/vdb number=1 part_end=1500MiM state=present
    rescue:
    - name: debug 1500M not create
      debug: msg="could not create partation of that size"
    - name: create 800M
      parted: device=/dev/vdb number=1 part_end=800MiB state=present
      
    - name: mk a file system
      filesystem: dev=/dev/vdb1 fstype=ext4 force=yes

    - name: mount path
      mount: src=/dev/vdb1 fstype=ext4 path=/newpart state=mounted

# 执行playbook
ansible-playbook  partition.yml
```

验证:  

到受控机器上通过`lsblk`查看。



### 生成主机文件

将一个初始模板文件从 http://host.domain8.example.com/ex300/hosts.j2 下载到/home/greg/ansible
完成该模板，以便用它生成以下文件：针对每个清单主机包含一行内容，其格式与/etc/hosts 相同。

创建名为 /home/greg/ansible/hosts.yml 的 playbook，它将使用此模板在 dev 主机组中
的主机上生成文件 /etc/myhosts 。

该 playbook 运行后， dev 主机组中主机上的文件 /etc/myhosts 应针对每个受管主机包含
一行内容：
```
127.0.0.1 localhost localhost.localdomain localhost4 localhost4.localdomain4 ::1 localhost
localhost.localdomain localhost6 localhost6.localdomain6
172.25.250.9 workstation.lab.example.com workstation
172.25.250.10 servera.lab.example.com servera
172.25.250.11 serverb.lab.example.com serverb
172.25.250.12 serverc.lab.example.com serverc
172.25.250.13 serverd.lab.example.com serverd
```
注：清单主机名称的显示顺序不重要


```sh
# 完善j2文件

{% for host in groups['all'] %}
{{ hostvars[host]['ansible_default_ipv4']['address']}} {{ hostvars[host]['ansible_fqdn'] }} {{ hostvars[host]['ansible_hostname'] }}
{% endfor %}

# 创建playbook
- hosts: all

- hosts: dev
  tasks:
  - name: copy a file
    template: src=hosts.j2 dest=/etc/myhosts
    
# 执行playbook
ansible-playbook hosts.yml
```

验证:  
去受控节点dev上查看`/etc/myhosts`文件


### 修改文件内容
按照下方所述，创建一个名为 /home/greg/ansible/issue.yml 的 playbook ：
- 该 playbook 将在所有清单主机上运行
- 该 playbook 会将 /etc/issue 的内容替换为下方所示的一行文本：
- 在 dev 主机组中的主机上，这行文本显示 为：Development
- 在 test 主机组中的主机上，这行文本显示 为：Test
- 在 prod 主机组中的主机上，这行文本显示 为：Productio

```sh
# 创建playbook
vim /home/greg/ansible/issue.yml

---
- hosts: all
  tasks:
  - name: dev content
    copy: dest=/etc/issue content="Development\n"
    when: inventory_hostname in groups.dev

  - name: test content
    copy: dest=/etc/issue content="Test\n"
    when: inventory_hostname in groups.test

  - name: prod content
    copy: dest=/etc/issue content="Production\n"
    when: inventory_hostname in groups.prod
    
# 执行playbook
ansible-playbook issue.yml
```
验证:  
去受控节点dev上查看`/etc/issue`文件

### 创建 Web 内容目录

按照下方所述，创建一个名为 /home/greg/ansible/webcontent.yml 的 playbook ：
- 该 playbook 在 dev 主机组中的受管节点上运行
- 创建符合下列要求的目录 /webdev ：
- 所属组为 webdev 组
- 具有常规权限：owner=read+write+execute ，group=read+write+execute，
other=read+execute
- 具有特殊权限：设置组 ID
- 用符号链接将 /var/www/html/webdev 链接到 /webdev
- 创建文件 /webdev/index.html ，其中包含如下所示的单行文件： Development
- 在 dev 主机组中主机上浏览此目录（例如 http://172.25.250.9/webdev/ ）将生成以下
输出：
Development

```sh
# 创建playbook
vim /home/greg/ansible/webcontent.yml

---
- hosts: dev
  roles:
  - role: apache

  tasks:
  - name: create a group
    group: name=webdev state=present
  - name: create a direct
    file: path=/webdev/ state=directory mode=2755 group=webdev setype=httpd_sys_content_t
  - name: create a link
    file: src=/webdev dest=/var/www/html/webdev state=link

  - name: create a file
    copy: content="Development\n" dest=/webdev/index.html setype=httpd_sys_content_t

# 执行playbook
ansible-playbook /home/greg/ansible/webcontent.yml
```
> setype可通过`ls -Zd /var/www/html`查看

验证:  

执行`curl 172.25.250.9/webdev/index.html`

### 生成硬件报告

创建一个名为 /home/greg/ansible/hwreport.yml 的 playbook ，它将在所有受管节点上生成
含有以下信息的输出文件 /root/hwreport.txt ：
- 清单主机名称
- 以 MB 表示的总内存大小
- BIOS 版本
- 磁盘设备 vda 的大小
- 磁盘设备 vdb 的大小
- 输出文件中的每一行含有一个 key=value 对。
您的 playbook 应当：
- 从 http://host/ex300/hwreport.empty 下载文件，并将它保存为 /root/hwreport.txt
- 使用正确的值改为 /root/hwreport.txt
- 如果硬件项不存在，相关的值应设为 NONE
when: ansible_devices.vdb is not define

```sh
# 创建playbook
vim /home/greg/ansible/hwreport.yml

---
- hosts: all
  tasks:
  - name: rm file
    shell: "rm -rf /root/hwreport.txt"

  - name: download file
    get_url: url="http://host/ex300/hwreport.empty" dest="/root/hwreport.txt"

  - name: inventory name
    lineinfile: path=/root/hwreport.txt regexp="^HOST" line=HOST={{inventory_hostname}}

  - name: memory size
    lineinfile: path=/root/hwreport.txt regexp="^MEMORY" line=MEMORY={{ansible_memtotal_mb}}MB

  - name: bios version
    lineinfile: path=/root/hwreport.txt regexp="^BIOS" line=BIOS={{ansible_bios_version}}

  - name: vda size
    lineinfile: path=/root/hwreport.txt regexp="^DISK_SIZE_VDA" line=DISK_SIZE_VDA={{ansible_devices.vda.size}}

  - name: vdb size
    block:
    - lineinfile: path=/root/hwreport.txt regexp="^DISK_SIZE_VDB" line=DISK_SIZE_VDB={{ansible_devices.vdb.size}}
    rescue:
    - lineinfile: path=/root/hwreport.txt regexp="^DISK_SIZE_VDB" line=DISK_SIZE_VDB=NONE

# 执行playbook
ansible-playbook hwreport.yml
```

方法二： 

```sh
# 创建playbook
vim /home/greg/ansible/hwreport.yml

---
- hosts: all
  tasks:
  - name: rm file
    shell: "rm -rf /root/hwreport.txt"

  - name: download file
    get_url: url="http://host/ex300/hwreport.empty" dest="/root/hwreport.txt"

  - name: inventory name
    replace: path=/root/hwreport.txt regexp="^HOST" replace="{{inventory_hostname}}"

  - name: memory size
    replace: path=/root/hwreport.txt regexp="^MEMORY" replace="{{ansible_memtotal_mb | string}}"

  - name: bios version
    replace: path=/root/hwreport.txt regexp="^BIOS" replace="{{ansible_bios_version}}"

  - name: vda size
    replace: path=/root/hwreport.txt regexp="^DISK_SIZE_VDA" replace="{{ansible_devices.vda.size}}"

  - name: vdb size
    replace: path=/root/hwreport.txt regexp="^DISK_SIZE_VDB" replace="{{ansible_devices.vdb.size}} | default('NONE')"


# 执行playbook
ansible-playbook hwreport.yml
```

验证:  

在被控节点上查看`/root/hwreport.txt`


### 创建密码库

按照下方所述，创建一个 Ansible 库来存储用户密码：
- 库名称为 /home/greg/ansible/locker.yml
- 库中含有两个变量，名称如下：
- pw_developer，值为 redhat
- pw_manager，值为 redhat
- 用于加密和解密该库的密码为 redhat
- 密码存储在文件 /home/greg/ansible/secret.txt 中


```sh
# 创建locker.yml
vim  /home/greg/ansible/locker.yml

pw_developer: redhat
pw_manager: redhat

# 创建secret.txt
vim /home/greg/ansible/secret.txt
redhat

# 加密
ansible-vault encrypt --vault-id=./secret.txt locker.yml
```

验证：  

查看密码：`ansible-vault view --vault-id=./secret.txt locker.yml`

### 创建用户帐户
从 http://host/ex300/user_list.yml 下载要创建的用户的列表，并将它保存到/home/greg/ansible。

在本次考试中使用在其他位置创建的密码库 /home/greg/ansible/locker.yml 。创建名为
/home/greg/ansible/users.yml 的 playbook ，从而按以下所述创建用户帐户：  
职位描述为 developer 的用户应当：
- 在 dev 和 test 主机组中的受管节点上创建
- 从 pw_developer 变量分配密码
- 是附属组 devops 的成员
职位描述为 manager 的用户应当：  
- 在 prod 主机组中的受管节点上创建
- 从 pw_manager 变量分配密码
- 是附属组 opsmgr 的成员
- 密码采用 SHA512 哈希格式。
- 额外，增加密码过期
您的 playbook 应能够在本次考试中使用在其他位置创建的库密码文件
/home/greg/ansible/secret.txt 正常运行。


```sh
# 下载用户列表
wget  http://host/ex300/user_list.yml
# 创建playbook

---
- hosts: dev,test
  vars_files:
  - user_list.yml
  - locker.yml
  tasks:
  - name: create a group
    group: name=devops state=present
  - name: create a user
    user: name={{item.name}} state=present comment=developer groups=devops password={{pw_developer | password_hash('sha512')}} 
    when: item.job == "developer"
    loop: "{{users}}"

- hosts: prod
  vars_files:
  - user_list.yml
  - locker.yml
  tasks:
  - name: create a group
    group: name=opsmgr state=present
  - name: create a user
    user: name={{item.name}}  state=present comment=manager groups=opsmgr password={{pw_manager | password_hash('sha512')}}
    when: item.job == "manager"
    loop: "{{users}}"
  - name: enable password expires
    shell: chage -M "{{item.passwd_max_expires}}" "{{item.name}}"
    when: item.job == "developer"
    loop: "{{users}}"

# 执行playbook
ansible-playbook users.yml --vault-id=./secret.txt
```
> enable password expires这步是新加的，{{item.passwd_max_expires}}可替换成实际过期天数，如30。

验证:  

在dev,test节点上查看`id bob`和`id fred`。  
验证密码过期:`chage -l bob`。
在prod节点上查看`id sally`。


### 更新 Ansible 库的密钥

按照下方所述，更新现有 Ansible 库的密钥：
从 http://host/ex300/salaries.yml 下载 Ansible 库到 /home/greg/ansible
- 当前的库密码为 111111
- 新的库密码为 redhat
- 库使用新密码保持加密状态

```sh
# 下载
wget http://host/ex300/salaries.yml
# 修改密码
ansible-vault rekey salaries.yml
```

验证：  
使用命令`ansible-vault view salaries.yml`，然后输出密码。

###  selinux

安装 RHEL 系统角色软件包，并创建符合以下条件的 playbook/home/greg/ansible/selinux.yml ：
- 在所有受管节点上运行
- 使用 selinux 角色
- 要求所有的节点上 selinux

```sh
# 在 ansible 主机上安装 rhel-system-roles.noarch
yum install rhel-system-roles.noarch
# 拷贝角色
cp -r /usr/share/ansible/roles/rhel-system-roles.selinux/ ./roles/selinux
# 拷贝playbook
cp /usr/share/doc/rhel-system-roles/selinux/example-selinux-playbook.yml selinux.yml
# 修改playbook
把 rhel-system-roles.selinux 换成 selinux
# 执行playbook
ansible-playbook selinux.yml
```

验证：  
去受控节点上查询`getenforce`

### crontab
创建一个名为 `/home/greg/ansible/cron.yml` 的 playbook : 
- 配置 `cron` 作业，该作业`每隔 2 分钟`运行并执行以下命令：  
`logger "EX200 in progress"`，以用户 `natasha`


```sh
# 创建playbook
vim /home/greg/ansible/cron.yml

---
- name: create logger
  hosts: all
  tasks:
  - name: create user natasha
    user:
      name: natasha
      state: present
  - name: "logger info for natasha"
    cron:
      name: "logger info"
      minute: "*/2"
      user: natasha
      job: logger "EX294 in progress"

# 执行playbook
ansible-playbook cron.yml
```

验证：  
去受控节点上查询`crontab -u natasha -l`。