---
title: Arthas配合Recaf调试服务端代码
date: 2023/03/10 00:00
updated: 2023/09/24 21:56
categories:
- [技术, 示例]
tags:
- 软件测试
- 测试工具
- Arthas
- Recaf
---

## 安装

### Arthas

从官方网站下载Arthas压缩包，上传到要对应的服务部署服务器上并解压。

```sh
# 新建arthas目录
mkdir /opt/arthas
# 进入目录
cd /opt/arthas
# 上传arthas压缩包后，进行解压
unzip arthas*.zip
```

解压后的目录文件：

![image-20230924224839416](./assets/image-20230924224839416.png)

到此，  arthas就安装和部署完成了。

### Recaf

Recaf是一个Jar包字节码查看工具，下载地址 ：https://github.com/Col-E/Recaf



## 使用

Arthas的功能和使用场景很多，这里以实际应用场景作为演示。



### 修改SDK返回内容

借助 Arthas 可以让字节码修改和热更新变得简单。

- 双击打开Recaf，或在命令行执行：


```java
java jar -jar recaf
```

- 在启动的图形界面中，点击菜单栏的 `File - Load` ，加载需要反编译的 SDK Jar包。

![image-20230924224749975](./assets/image-20230924224749975.png)

- 找到 SDK Jar包文档的方法入口，假设这是我们要注入异常的切入点。

![image-20230924224917063](./assets/image-20230924224917063.png)

在字节码查看工具里面找到对应的接口实现和方法，如下图所示：

![image-20230924225116497](./assets/image-20230924225116497.png)

后面我们通过在服务器上直接反编译并修改Jar包逻辑，查看热更新字节码。

>  注意，在本地先修改后编译，然后上传class文件也是可以的，只是通过服务器操作更直接。



###　修改Jar包出分

首先反编译查看Jar包代码，结合文档的方法入口和出入参找到对应的代码块。

![image-20230924225242783](./assets/image-20230924225242783.png)

文档中出参是**bj_score**,对应代码的字段是**jsonResult**。

jsonResult的数据类型是个JSONObject，所以bj_score是JSONObject中的key，只要覆盖bj_score的value就可以修改bj_score的值了。

以下步骤同模拟**Jar**包异常部分，重复内容已省略。首先修改并覆盖bj_score的值，然后编译和刷新字节码。

![image-20230924225341513](./assets/image-20230924225341513.png)

再次请求，就能看到**bj_score**的值已经被修改了。

![image-20230924225350121](./assets/image-20230924225350121.png)

根据字节码反编译查看的源码，我们可以就可以直面加工方的Jar包逻辑，从而进行比如Jar包返回的Code码，返回值内容等等，等任何可以通过源码修改的地方，我们得可以轻松修改和模拟场景。

### 热更新字节码

Arthas 可以热更新字节码并生效，无需重启服务。

- 启动 Arthas 服务，并进入 **Arthas** 控制台。

```sh
# 进入arthas解压后的目录
cd /opt/arthas
# 启动arthas服务
java -jar arthas-boot.jar
```

- Arthas会列出当前服务下的java进程，比如我们要测试是的**pudao_biz**服务，就输入1。

![image-20230924225642474](./assets/image-20230924225642474.png)

- 然后会打印以下的内容，比如 Java 的进程和 Arthas 的版本号。

![image-20230924225721797](./assets/image-20230924225721797.png)

- 通过命令反查类的加载器，首先获取Jar包方法入口所在的包名称和类名。复制 package 路径 + class名称，**com.icekredit.rccsdf.service.impl.RccsDeviceFraudServiceImpl**。比如下图：

![image-20230924225815199](./assets/image-20230924225815199.png)

 ```sh
 # 通过类的路径反查类的加载器
 sc -d com.icekredit.rccsdf.service.impl.RccsDeviceFraudServiceImpl |grep classLoaderHash
 ```

- 拿到类加载器的Hash值，比如下图的就是：**51abf713**

![image-20230924225908583](./assets/image-20230924225908583.png) 

- 将jar包的的字节码反编译成源码，下面的代码是将class字节码反编译成源码到**/tmp/RccsDeviceFraudServiceImpl.java**下，注意类名应该保持一致，源

  码以**.java**结尾。

```sh
jad --source-only com.icekredit.rccsdf.service.impl.RccsDeviceFraudServiceImpl > /tmp/RccsDeviceFraudServiceImpl.java
```

- 上面的命令执行完毕后，新建一个服务器窗口，可以在上面查看到刚才反编译出的源码问题。

 ![image-20230924230006957](./assets/image-20230924230006957.png)

- 通过vim命令修改源码内容：

```sh
vim /tmp/RccsDeviceFraudServiceImpl.java
```

- 这里以模拟Jar包方法异常为例，在里面写入一个除零异常的代码。

![image-20230924230048944](./assets/image-20230924230048944.png)

这样凡是调用Jar包的下面方法时，就会抛出异常。

![image-20230924230108625](./assets/image-20230924230108625.png)

- 切换到**arthas**控制台的窗口，执行以下的命令将源码编译成字节码。（注意替换**51abf713**为实际类加载器hash值。）

```sh
mc -c 51abf713 /tmp/RccsDeviceFraudServiceImpl.java -d /tmp/
```

![image-20230924230143233](./assets/image-20230924230143233.png)

- 再次切换服务器的窗口，获取变异后的class文件路径(/tmp是之前编译的路径，RccsDeviceFraudServiceImpl是类名，class是字节码的后缀） 

```sh
find /tmp -name RccsDeviceFraudServiceImpl.class
```

![image-20230924230206615](./assets/image-20230924230206615.png)



- 重新切换回**arthas**控制台窗口，根据类加载器的Hash值（注意替换**51abf713**为实际类加载器hash值）热更新字节码（立即生效，无需重启）

```sh
redefine -c 51abf713 /tmp/com/icekredit/rccsdf/service/impl/RccsDeviceFraudServiceImpl.class
```

![image-20230924230252745](./assets/image-20230924230252745.png)

- 调用接口，Jar包异常场景就覆盖到了。

![image-20230924230327452](./assets/image-20230924230327452.png)

- Jar包场景验证完毕后，将源码文件里面的除零异常代码行删除，再根据第**8**步骤的顺序往下执行，使用**mc**命令将源码编译成字节码，接着使用**redefine**命令热更新字节码就可以了，更新后服务又恢复正常了。

![image-20230924230355671](./assets/image-20230924230355671.png)

(本文完)