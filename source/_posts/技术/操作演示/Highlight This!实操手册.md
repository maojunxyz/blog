---
title: Highlight This!实操手册
date: 2023/08/13 15:09
updated: 2023/08/13 15:09
categories:
- [技术, 示例]
tags:
- Chrome
- Extension
---
##  介绍

Highlight This！, 在其官网[^1]的描述是：

Highlight This 是 Chrome 浏览器和 Firefox 浏览器的一个浏览器扩展，可以在访问页面时高亮显示你选择的单词。你可以设置不同的单词列表，为它们指定颜色，并指定要在哪些网页上突出显示这些单词。



## 下载安装

可以分为谷歌插件市场在线安装和离线安装两种方式。

### 谷歌插件市场

打开Highlight This！插件市场地址[^2]，直接点击安装即可。

### 离线安装

1. 借助 Crx 在线下载工具[^3]，先插件下载到本地,接着解压到本地目录。

![离线拓展包](./assets/wps1.png) 

2. 打开谷歌浏览器的扩展管理，或者直接在浏览器地址栏输入下面的内容并回车：

 ```url
 chrome://extensions/
 ```

 

点击右上角启用开发者模式，然后点击加载已解压的，选择之前下载并解压的 Highlight This! 文件夹。看到如下插件加载完成即成功。



![加载已解压的插件](./assets/wps2.png) 

 

 

## 配置

要让 Highlight This! 发挥出最大的效果，首先要进行配置，推荐根据分组结合颜色的方式来进行划分。

点击扩展图标，并点击右下角的***\*+\****号来新建列表分组。

![创建分组](./assets/wps3.png) 



### 创建列表

选择非同步和标准列表即可。

![新建列表](./assets/wps4.png) 

###　高亮关键字

配置关键字高亮内容和要校验的规则。



![高亮关键字配置](./assets/wps5.jpg)

 

增加多组关键字高亮配置，当配置完后可以看到如下的效果。

![配置效果](./assets/wps7.png) 

当页面显示的内容有匹配到关键字时，页面中的关键字会使用匹配的规则列表来显示。

![页面使用效果](./assets/wps8.png) 



（本文完）



[^1]:[Highlight This!](https://highlightthis.net/)
[^2]:[Highlight This: finds and marks words](https://chrome.google.com/webstore/detail/highlight-this-finds-and/fgmbnmjmbjenlhbefngfibmjkpbcljaj)
[^3]:[CRX Downloader](https://standaloneinstaller.com/online-tools/crx-downloader)