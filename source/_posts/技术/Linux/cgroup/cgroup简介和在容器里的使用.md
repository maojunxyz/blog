---
title: cgroup简介和在容器里的使用
date: 2021/03/14 14:02:19
updated: 2021/03/14 14:02:19
categories:
- [技术, Linux]
tags:
- cgroup
- 容器
---



`free -hm`看到的内存并不一定是真实可用内存。查看机器内存有16G,但实际可用只有1G，超出分配内存就被cgroup杀死的进程，原因是被cgroup给做了内存限制。

## cgroup
在Linux系统中，cgroups（控制组）可以用来限制、记录、隔离进程组所使用的物理资源（如CPU、内存、磁盘I/O等）。
可以通过读取cgroup文件系统中的相关文件来查看内存限制和当前使用情况。

一般来说，cgroup的内存相关设置和状态信息存储在`/sys/fs/cgroup/memory/`目录下。

以下是一些基本的步骤来查看cgroup的内存信息：

### 查看内存cgroup的总览

   ```bash
   ls /sys/fs/cgroup/memory/
   ```

   - `memory.usage_in_bytes`
   - `memory.limit_in_bytes`  
      这些文件包含了cgroup的内存使用情况和限制信息。

### 查看特定cgroup的内存使用情况

   ```bash
   cat /sys/fs/cgroup/memory/your_cgroup_name/memory.usage_in_bytes
   ```

   > 注意：`your_cgroup_name`需要替换为你想要查看的cgroup的实际名称或路径。

### 查看内存限制

   ```bash
   cat /sys/fs/cgroup/memory/your_cgroup_name/memory.limit_in_bytes
   ```

### 查看所有cgroup的内存信息
   要查看所有cgroup的内存使用情况，需要遍历`/sys/fs/cgroup/memory/`目录下的所有子目录，并读取每个子目录中的`memory.usage_in_bytes`文件。


## 容器和cgroup


在容器内运行Java应用时，可以通过JVM参数来让Java运行时识别并适应容器设置的资源限制。这主要通过使用JVM的某些特性来实现，这些特性允许Java应用更好地与容器的资源限制集成。

一种常用的方法是使用JVM的`-XX:+UnlockExperimentalVMOptions -XX:+UseCGroupMemoryLimitForHeap`参数。它们允许JVM自动检测并适应由cgroup设置的内存限制。当启用这些选项时，JVM会尝试根据容器的内存限制来自动调整Java堆的大小。

例如:

```bash
java -XX:+UnlockExperimentalVMOptions -XX:+UseCGroupMemoryLimitForHeap -jar your-app.jar
```

这样，当Java应用运行在Docker容器或其他使用cgroup限制资源的环境中时，JVM会自动检测这些限制并相应地调整堆大小。这有助于避免手动设置堆大小不当而导致的问题，如内存溢出或容器被杀死。


（完）