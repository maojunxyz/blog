---
title: 使用 backblaze-B2 搭建自己的图床
date: 2020/02/21 00:00
updated: 2020/02/21 00:00
categories:
- [技术, 示例]
tags:
- 对象存储
- backblaze
---


## 图床需求

在搭建完`Typecho` 博客，并给上传的图片添加图片阴影效果后，发现在使用`Typecho`自身的图片上传功能时，每次截图后不支持直接粘贴。而是需要先将图片保存到本地后，才能上传到服务器。

这在使用的体验上就不太顺畅，并且上传的图片是不经过任何处理的。每次截图的大小都在`50kb`上下了，保存的位置也是同博客的目录。长久下来会占用不少的空间，毕竟个人的服务器资源有限。最要紧的还是图片加载问题了，所有请求都是向同一服务器发起请求。页面的响应速度也是会受到一定的影响。

带着以上的问题，决定将图片迁移到图片服务器。比如**阿里云 OSS **, **Amazon S3** 等，本着全面了解的心情，在了解其他产品同类产品后。发现 **backblaze B2** 提供了免费`10G`的存储服务，这让我想到了之前的 **七牛云** 也是有`10G`免费图片托管服务。但是国内的网站备案才能使用让我望而却步，最终决定尝试 **backblaze B2**,同时本着鸡蛋不放在同一个篮子的思想，后续将继续在其他平台开启图片托管服务，实现多机备份。

**Backblaze B2** 提供了`10G`免费文件存储服务。超出部分将按量付费，但是对与博客平台而言，存图片能把**10G**空间用完还真不容易，以一张图片`100K`计算，差不多可以存储`10w`张图片。对于文件我更倾向与上传到网盘后分享，而不是通过文件存储服务器。所以只用来当图床已经足够了。**Backblaze** 官网也对比了其他储存服务的价格，总之是很便宜了。



## 使用 B2 服务

点击进入[backblaze注册](https://web.archive.org/web/20201024065121/https://secure.backblaze.com/user_signin.htm)页面，输入邮箱密码后进入填写相关信息进入到控制台界面。 

点击**Create a Bucket**，输入服务要创建的`bucket`名称，文件权限选择**public**,然后点击**Create a Bucket**。

创建完`bucket`后，就可以通过`backblaze`提供的网页来上传图片了。在控制台界面点击**Browse Files**,进入我们刚才创建的`bucket*中，点击**upload**按钮就可以拖拽文件或者访问本地图片路径上上传图片到**backblzze b2**了。

上传文件后，就可以点击文件名称后面的**i**图标按钮查看图片的信息，点击**Friendly URL**链接就可以访问到图片了。如果我们每次都要通过网页上传并且点击图片信息获取链接的就会显得非常的繁琐。**backblaze b2**也提供了客户端工具给我们使用。

如果需要利用客户端上传，则需要在`bucket`创建后，在控制台界面点击**App Keys**创建应用密钥，点击**Add a New Application Key**,在弹出的窗口中填写密钥的名称，以及访问`bucket`的范围，默认是可以访问所有的`bucket`,权限选择**Read and Write**，允许有**读写**权限。

保存后会显示三个值的内容。

- keyID
- keyName
- applicationKey

> 注意保存**applicationKey**的值，**applicationKey**只会显示一遍。如果没保存就需要重复上面的步骤再创建一个，把之前的删除即可。



生成应用密钥的目的是为了让第三方应用可以用上传图片到`backblaze`的权限。方便我们将截图保存并访问截图链接。由于我使用的是`Archlinux`系统，目前没有支持`backblaze b2`的`linux`平台工具，所以我只能通过官方提供的命令行工具来实现客户端上传。如果你是`Windows`或者`Mac`用户则可以在这里查看支持的[backblaze客户端](https://web.archive.org/web/20201024065121/https://help.backblaze.com/hc/en-us/articles/226688888-How-can-I-upload-files-to-B2-)。

这里以**backblaze b2**命令行客户端为例，首先去`GitHub`下载官方开源的命令行工具:[b2-command](https://web.archive.org/web/20201024065121/https://github.com/Backblaze/B2_Command_Line_Tool)。根据官方文档安装工具，安装后使用命令登入`backblaze b2`,

```bash
1$ b2 authorize-account
```

根据提示输入:

- keyID
- applicationKey

> 在控制台**App Keys**查看`keyID`，`applicationKey`即之前保存的密钥，如果忘记需重新生成创建。

查看`buckets`

```bash
1$ b2 list_buckets
```

> 注意：如果输出为空则说明没有创建`buckets`

上传图片到`bucket`:

```bash
1$ b2 upload-file  <bucketName> <localFilePath> <b2FileName>
```

- **bucketName**参数是`bucket`名称
- **localFilePath**参数为本地文件路径
- **b2FileName**参数为上传后的文件名称

例如：

```bash
1$ b2 upload-file  test foo.png bar.png
```

上面的格式为上传本地图片`foo.png`到名为`test`的`bucket`并命令为`bar.png`文件。

上传成功会在控制台返回以下内容：

```bash
1foo.png: 100%|███████████████████████████████████████████████████████████████████| 73.2k/73.2k [00:30<00:00, 2.39kB/s]
2URL by file name: https://f000.backblazeb2.com/file/bucketName/foo.png
3URL by fileId: https://f000.backblazeb2.com/b2api/v2/b2_download_file_by_id?fileId=4_zb21c374aec406a66740d0716_f109b36d0492bfbf2_d20200222_m133812_c000_v0001063_t0000
4{
5  "action": "upload",
6  "fileId": "4_zb21c374aec406a66740d0716_f109b36d0492bfbf2_d20200222_m133812_c000_v0001063_t0000",
7  "fileName": "foo.png",
8  "size": 73243,
9  "uploadTimestamp": 1582378692000
10}
```

可以看到控制台返回了图片的`URL`，所以我们只要处理下返回结果。过滤出链接就可以。

- URL by file name 上面返回的参数可以通过管道命令先过滤出`URL`所在的行内容，即

```
1URL by file name: https://f000.backblazeb2.com/file/bucketName/foo.png
```

然后再用`akw`工具通过`name:`列分隔取得后面的链接。

所以图片的`URL`链接就可以通过下面的命令过滤出来了。

```bash
1$ b2 upload-file  test foo.png bar.png | grep "URL by file name" |awk -F 'name:' '{print $2}'
```

但是现在的问题是我们要获取上传的图片名称和保存到图床的名称，上传到`backblaze b2`的文件名可以和本地图片的文件名一样。这样我们可以可以写一个脚本实现截图后自动上传及获取图片的链接地址。



## 优化和改良

我们通过`b2`客户端上传图片并过滤获取到了图片的地址。但是其中存在着大量的手工步骤：

- 截图
- 截图后命名文件
- 获取图片上传路径
- 修改脚本的图片地址
- 复制上传后返回的链接
- 粘贴使用

如果我们需要在短时间内上传大量的图片那重复的步骤就会很多。我们可以将重复的步骤自动化，优化后如下：

- 截图
- 自动化（命令文件;获取获取图片上传路径;修改脚本中的图片地址;返回链接到剪切板中）
- 粘贴使用

这里将用到命令行截图工具`maim`做为演示，如果是`Archlinux`用户或者使用`Pacman`作为包管理的系统，可以使用以下命令安装`maim`:

```bash
1$ sudo pacman -S maim
```

安装完后可以通过命令`maim -h`或者帮助信息。其中我们要用到的参数有`-s`，即选择区域截图。比如将截图保存到`/tmp`目录下命令为`screenshot.png`文件命令为：

```bash
1$ maim -s /tmp/screenshot.png
```

这样我们就有了本地图片名称了，但是当我们的图床名称和本地图片一样时，多次上传同样的名称图片会覆盖图床上的同名文件。所以最好使用随机产生不重复名称作为文件名。

这里我们可以使用格式化的日期函数作为文件名称:

```bash
1$ echo $(date +%y%m%d%k%M%s)
```

参数说明：

- y:年份后2位数字
- m:2位数月份数字
- d:2位数单天数字
- K:24制小时数字
- M:60制分钟数字
- s:UTC到当前时间的秒数 上面的输出结果格式为：

```bash
120022323001582470005
```

得到这个随机数字作为图片名称后，再加上图片的后缀`.jpg`或者`.png`就完成上传的图片名称定义了。

接着我们将前面的`b2`上传和图片过滤地址命令结合起来作为一个组合命令：

```bash
1#!/bin/sh
2picName=$(date +%y%m%d%k%M%s)
3picSuffix=png
4# protocol:http or https
5protocol=https
6domainName=img.maojun.xyz
7
8sleep 2;
9maim -s /tmp/screenshot.png; b2 upload-file  imgxyz  /tmp/screenshot.png "$picName"."$picSuffix" | grep name |awk -F '/' '{print $(NF)}' |sed "s/^/$protocol:\/\/$domainName\//" | xclip -selection clipboard
10
11echo url:  "$protocol"://"$domainName"/"$picName"."$picSuffix"
```

上面的脚本地址定义了:

- picName：上传到图床的随机名称
- picSuffix:上传的图片后缀名
- protocol:自定义域名的前缀协议
- domainName:自定义域名

> 注意:使用自定义域名需要先完成`DNS`的`CNAME`解析。

组合脚本将`b2`上传后返回的图片`URL`地址过滤只保留下**文件名称和后缀名**,通过拼接我们的自定义域名然后发送给剪切板。这样只要将脚本绑定到快捷键就可以快速调用脚本并生成需要的链接，然后粘贴到需要的地方使用就可以了。

上面的脚本中，我将本地的图片地址写死为`screenshot.png`，而不是随机名称。因为上传到图床的图片我不想保存在本地，每次都替换上一次的文件。如果你希望保留在本地，可以将这里替换成随机生成的图片名称。

现在就可以愉快的截图并上传到图床使用了。同时为了区分本地截图和图床截图，我绑定了`2`个快捷键给不同的功能。这样就能区分场景完成截图的需求了。

后续的图片地址优化过程将在[使用Cloudflare服务优化图床](https://web.archive.org/web/20201024065121/https://maojun.xyz/archives/using-cloudflare-to-optimize-image-services.html)中介绍。

（本文完）

**参考内容**

- [free-personal-image-hosting-with-backblaze-b2-and-cloudflare-workers](https://web.archive.org/web/20201024065121/https://jross.me/free-personal-image-hosting-with-backblaze-b2-and-cloudflare-workers/)



