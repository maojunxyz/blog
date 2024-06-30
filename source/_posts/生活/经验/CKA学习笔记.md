---
title: CKA学习笔记
date: 2023/05/01 17:41:11
updated: 2023/05/01 17:41:11
categories:
- [生活, 经验]
tags:
- 技巧
---
# CKA学习笔记

CKA学习记录

## 容器

容器和虚拟机的区别，容器本身是一个APP，虚拟机是一个完整的系统。



### 容器管理

runtime，运行时。

| **高级别Runtime** | **低级别Runtime** |
| ----------------- | ----------------- |
| **docker**        | **runc**          |
| **containerd**    | lxc               |
| cri-o             | gvisor            |
| rkt               | kata              |
| podman            | ...               |
| ...               |                   |

> 一般而言，高级别的Runtime会去调用低级别的Runtime，docker和containerd底层调用的是runc。

低级别运行时只能管理容器，高级别运行时不仅可以管理容器，还可以管理镜像。无论是高级别还是低级别运行时，所使用的镜像是一样的。





### 容器架构

docker是客户端工具，dockerd是服务端工具。docker默认连接的是本机dockerd，可以通过-H指定远程的dockerd服务。

containerd是服务端，上层的客户端有ctr,crictl,**nerdctl**，不同的客户端之间是通过命名空间相互隔离的，所以不同客户端创建的容器是隔离看不见的。



## 搭建环境

创建2个虚拟机，在里面分别安装docker和containerd。

> 使用containerd运行时是因为K8S的新版本中不再作为运行时了，但是docekr依旧可以用来打包和构建。

```sh
# 关闭屏幕保护
setterm -black 0
# 配置yum源
rm -rf /etc/yum.repos.d/* ; wget ftp://ftp.rhce.cc/k8s/* -P /etc/yum.repos.d/
# 安装docker
yum install -y docker-ce
# 启动docker并设置开机启动
systemctl enable docker --now
# 安装containerd和客户端工具
yum install -y containerd.io cri-tools 
# 启动containerd并设置开机启动
systemctl enable containerd --now
```



### 查看docker运行时

```sh
# 查看运行时
docker info |grep -i runtime
 Runtimes: io.containerd.runc.v2 runc
 Default Runtime: runc
```



###　docker配置

```sh
cat > /etc/docker/daemon.json <<EOF
{ "registry-mirrors": ["https://frz7i079.mirror.aliyuncs.com"]
}
EOF
systemctl restart docker
```



###　查看containerd运行时

```sh
# crictl连接运行时
crictl config runtime-endpoint unix:///var/run/containerd/containerd.sock
# 查看运行时
crictl info |grep -i runtime
        "type": "RuntimeReady",
      "defaultRuntimeName": "runc",
```



###　containerd配置

需要先配置客户端连接的运行时，

```sh
# 查看当前的配置
containerd config dump
# 导出containerd默认配置并覆盖当前的配置文件
containerd config default > /etc/containerd/config.toml
# 设置加速服务,搜索mirrors并在底下加入2行，完整配置如下
[plugins."io.containerd.grpc.v1.cri".registry.mirrors]
  [plugins."io.containerd.grpc.v1.cri".registry.mirrors."docker.io"]  
    endpoint = ["https://frz7i079.mirror.aliyuncs.com"]
# 搜索sandbox，修改这行内容为
sandbox_image = "registry.aliyuncs.com/google_containers/pause:3.7"
# 搜索SystemdCgroup并修改为true,同时删除上面几行，最终效果如下
 [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
            SystemdCgroup = true
# 修改完重启containerd
systemctl restart containerd
```



#### 安装nerdctl客户端

crictl用起来和docker命令兼容不是很好，比较好用的是nerdctl且命令也兼容docker，所以可以单独安装nerdctl搭配containerd。

```sh
# 下载后并解压
tar -zxvf nerdctl-0.23.0-linux-amd64.tar.gz -C /usr/bin/
#　安装网络插件
mkdir -p /opt/cni/bin/
tar -zxvf cni-plugins-linux-amd64-v1.1.1.tgz -C /opt/cni/bin/
```

由于nerdctl和crictl使用的是不同的命名空间，所以是相互隔离，为了兼容crictl创建的容器，可以将nerdctl的命名空间修改为和crictl一样。

```sh
# 创建配置目录
mkdir /etc/nerdctl/
# 添加配置信息
cat > /etc/nerdctl/nerdctl.toml <<EOF
debug = false
debug_full = false
address = "unix:///var/run/containerd/containerd.sock"
# 修改namespace为k8s.io,默认是default
namespace = "k8s.io"
#snapshotter = "stargz"
cgroup_manager = "systemd"
#hosts_dir = ["/etc/containerd/certs.d","/etc/nerdctl/certs.d"]
insecure_registry = false
EOF

# 启用linger
loginctl show-user root | grep Linger
loginctl enable-linger root
```



#### 配置nerdctl加速

nerdctl并不会使用container的配置文件，而是有其自己的配置文件。

```sh
mkdir -p /etc/containerd/certs.d/docker.io
cat > /etc/containerd/certs.d/docker.io/hosts.toml <<EOF
# server = "https://docker.io"
[host."https://frz7i079.mirror.aliyuncs.com"]
	capabilities = ["pull","resolve"]
override_path = true
EOF
```

#### nerdctl命令自动补齐

```sh
# 编辑~/.bashrc文件并添加以下内容
source <(nerdctl completion bash)
```



## 容器里运行的是什么

默认运行的是CMD指定的内容，可以通用`nerdctl history`查看镜像中指定的CMD命令。

```sh
nerdctl history hello-world
SNAPSHOT                                                                   CREATED          CREATED BY                                       SIZE        COMMENT
<missing>                                                                  17 months ago    /bin/sh -c #(nop)  CMD ["/hello"]                0.0 B       
sha256:e07ee1baac5fae6a26f30cabfe54a36d3402f96afda318fe0a96cec4ca393359    17 months ago    /bin/sh -c #(nop) COPY file:50563a97010fd7ce…    16.0 KiB    
```

hello-world运行默认运行的就是`/hello`命令。







## 容器的生命周期

容器里面的运行进程结束了，容器生命周期也结束了。默认进程运行的是CMD中制定的命令。


-t 模拟终端 -i 交互界面，如果默认CMD是bash,这样bash就有个依附窗口，不会自动结束容器了。

以下是合并的内容，还需要手动处理
容器
容器和虚拟机的区别：image-20230107193134952

要想运行容器，我们必须先安装软件runtime，也就是运行时。
image-20230107192916772

无论使用上图的哪一个runtime，他们最终使用的镜像是一致的。

OCI是一个镜像结构，和容器状态标准。

在1.24的时候就不使用docker使用runtime了，可以选择containerd.

但是并不是说docker就被淘汰了，无法继续使用了，而是可以在构建镜像，搭建仓库时用。

image-20230107194018096

docker是客户端，服务端模式，客户端可以连接到远程的机器上。

containerd是一个执行引擎，可以连接不通的客户端服务，最好用的是nerdctl，nerdctl，crictl,ctr之间创建的容器是通过namespace(命名空间）隔离的互相看不到。

虚拟机创建
从temp虚机克隆出两台虚机：

vm71-docker
vm72-container
账号密码都是root redhat
然后使用命令分别设置：

./set.sh 71

./set.sh 72

关闭屏保：setterm -blank 0

docekr安装
略

continerd安装
默认是crictl

默认已经有配置文件了，但是不全。 可以生成比较全的默认配置：

containerd config dufault > /etc/containerd/config.toml

查看当前的配置命令：

containerd config dufault

crictl命令
crictl images 查看镜像

crictl info 查看信息

默认使用的namespace:k8s.io

nerdctl
命令基本都是兼容docker的，非常好用。

但是netl和crictl默认使用的命名空间 是不同的。

默认使用的default

如果是K8s创建的容器，使用nerdctl排错时需要先切换成k8s的命令空间，否则看不到。

不同的命名空间 中镜像和容器都是隔离的，namespace的作用就是资源隔离。

镜像
一个镜像打了多个tag,都是用的一个image id，只有最后一个同imageid的镜像后，镜像文件才会从服务器中删除。

容器
容器里是一定要运行一个进程的。

如果创建容器的时候指定容器里运行的是什么的话，则容器里运行的是镜像里CMD所指定的。

使用docker history查看镜像所打的CMD命令

cka22-12-containerd-2镜像管理.vep 待学


































































