---
title: 配置 docker 镜像加速
date: 2020/02/26 00:00
updated: 2023/09/02 19:29
categories:
- [技术, 示例]
tags:
- 容器
- docker
---


## 配置镜像加速

根据官方文档给出的配置步骤进行操作：  

针对 Docker 客户端版本大于 1.10.0 版本的，通过修改daemon配置文件 `/etc/docker/daemon.json` 来加速：

```sh
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://8jngaj3j.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```
根据以上命令配置并重启 docke r服务后，重新进行镜像拉取。

（本文完）