---
title: 获取网站的 favicon 的方式
date: 2020/03/28 00:00
updated: 2023/09/10 12:10
categories:
- [技术, 示例]
tags:
- 软件
- 网页工具
---

## 需求

搭建网址导航时，需要获取网站的favicon.ico来增加辨识度和美观度。可以通过以下几种方式获取到网站的favicon.ico.



## 域名拼接

找到网站的的默认路径，然后在地址后面拼接favicon.ico，就是favicon图标的地址了。 

例如:[https://maojun.xyz/favicon.ico](https://maojun.xyz/favicon.ico)

  > 注意：如果网站没有配置favicon.ico则会返回404错误



## head元素定位

当域名后面拼接favicon.ico无法获取图标的，可以尝试通过通过首页的`head`标签中的`rel="shortcut icon"`来找到对应的favicon.ico位置。



## 在线获取ico工具

通过Google的链接工具来找到favicon.ico

[https://www.google.com/s2/favicons?domain=maojun.xyz](https://www.google.com/s2/favicons?domain=maojun.xyz)

> 把domain后的域名修改成需要查找favicon.ico的网站

（本文完）