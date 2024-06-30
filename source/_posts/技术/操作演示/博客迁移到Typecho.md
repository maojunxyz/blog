---
title: 博客迁移到 Typecho
date: 2020/02/20 00:00
updated: 2023/08/22 12:48
categories:
- [技术, 示例]
tags:
- 博客平台
- Typecho
---
##   尝试

博客平台历经 `HEXO`, `HUGO`, `Wordpress`, `GitHub Issues`，使用这些作为博客平台有着不同的体验。

但最终我都没有坚持使用下去。目前除了`HEXO`博客托管在`GitLab`网站，以及`GitHub Issues`做为省事的博客平台外。

其他博客平台都已经下线，结束了他们早期作为博客平台的使命。

以上的博客平台都属于开源项目，还有一些商业的博客平台目前也已注销和放弃了。

直到今天迁移到 `Typecho`，迁移的时候有过犹豫。会不会是下一个被我放弃的平台（后续被证实），以后的事我不好打保票。当我在这写下这篇迁移博文时，我是打算长期使用下去的。之前那些博客平台放弃的原因无乎不是折腾完了，没有了之前安装时的兴致，伴随着时间也就慢慢忘却了写作的本质。没想到当初抱着以写博客为开始，到最终以折腾捣鼓为结束。好似忘记了搭建博客的初心了。



## 迁移

迁移到 `Typecho` 后，打开默认的首页后，惊乎有个似曾相识的感觉。没错，这和我使用较长时间 `WordPress` 有那么的相似。相似的是界面和风格，但是不相似的是 `Typecho` 的思想。为什么这么说，体验下来 `Typecho` 是轻量及支持 `MarkDown` 语法，可以开箱即用。`WordPress` 虽也可以开箱即用，但是作为一个拥有多功能的后台而言，免不了要进行一版配置和优化。而 `Typecho` 就不用了，毕竟本身就没过多的配置。

当然，为了达到我想要的效果。我还是对 **Typecho** 进行了一般研究和配置。修改了默认主题以下内容：

- 页面宽度
- 代码高亮
- 导航链接
- 图片阴影
- 设置`favicon`
- 链接重定向去掉`index.php`

其他一些配置是在网站后台管理界面修改。

由于安装是基于经典的`LAMP`平台，所以没有过多要说明的。后期考虑迁移到`Docker`,有机会再做介绍。



## 配置

安装后默认网站是没有配置`favicon.ico`的，所以这个需要自己配置。进入对应的项目目录:

```bash
$ cd <WebHome>/usr/themes/default
```

然后编辑`header.php`,指定`favicon.ico`文件位置:

```html
<!-- favicon ico -->
<link rel="shortcut icon" href="/favicon.ico" type="image/x-icon">
```

> 注意:`/favicon.ico`对应的是网站根目录

修改后即可刷新页面查看到网站已经可以显示自定义的`favicon.ico`图标了。

### 链接重定向去掉`index.php`

进入网站后台配置管理页面，选择**设置->永久链接->启用地址重写功能**。 如果有警告信息，点击保存即可。然后需要配置重定向：这里以`Apache`服务器为示例。 进入网站的服务器根目录，编写`.htaccess`配置文件，写入以下内容：

```.htaccess
Options +FollowSymLinks
RewriteEngine on
RewriteBase /
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ /index.php/$1 [L]
```

保存然后去网站首页刷新页面，点击文章链接查看效果。

### 修改页面宽度

主题默认提供的是二栏分布，我偏爱更宽的内容区域，这样阅读起来比较舒服。所以去网站后台管理界面，**控制台->设置外观->关闭侧边栏显示**。

接着配置`php`页面,进入默认主题**Default**目录，编辑以下文件：

- archive.php
- index.php
- page.php
- post.php

找到`<div class="col-mb-12 col-8" id="main" role="main">`所在的行，将`col-8`修改为`col-12`。即占满**12**个单元。 去页面刷新查看效果，发现宽度边宽了。但是两边有较大留白空隙，去页面查看元素，发现是有`container`属性控制的。在`Default**目录下编写`diy.css`文件，加入以下内容：

```css
@media (min-width: 1200px) {
    .container{
        max-width: 1400px;
    }
}
```

即将`container`的最大宽度设置为`1400px`.保存后编辑`header.php`，添加自定义的`CSS`文件：

```php
<!-- diy css -->
<link rel="stylesheet" href="<?php $this->options->themeUrl('diy.css'); ?>">
```

保存后刷新页面查看效果。

### 增加图片阴影

`Typecho`上传图片后默认是没有效果的，显得非常的平。于是想到给增加些阴影更有立体感，图片的阴影样式是引用自**王垠**博客的图片样式。编辑原先定义的`diy.css`样式，追加以下内容：

```css
img {
    display: block;
    box-shadow: 0 0 10px #555;
    border-radius: 6px;
    margin-left: auto;
    margin-right: auto;
    margin-top: 10px;
    margin-bottom: 10px;
    -webkit-box-shadow: 0 0 10px #555;
}
```

在配置页面宽度时，已经引用过该`css`文件，所以直接去页面刷新查看效果即可。

### 代码高亮

`Typecho`默认是不支持代码高亮的，为了显示和阅读上更加美观。我们可以通过插件或脚本让`Typecho`支持代码高亮。这里采用导入`JS`和`CSS`的方案让代码高亮，使用的是`prismjs`。去[官网下载](https://web.archive.org/web/20201019231149/https://prismjs.com/)选择配置代码高亮主题及支持的代码类型，同时可以勾选插件让支持更多的功能。

配置好用，下载对应的`JS`和`CSS`文件上传到主题的目录，配置`header.php`文件，引入`prismjs`的依赖：

```php
<link rel="stylesheet" href="<?php $this->options->themeUrl('prism.css'); ?>">
<script src="<?php $this->options->themeUrl('prism.js');?>"></script>
```

保存后，刷新网站的页面查看效果。

### 增加导航链接

`Typeche`新增的自定义页面只支持站内链接。如果需要点击导航跳转到外部链接就不能通过**新建独立页面**的方式。同样的，我们可以在`header.php`中配置想要的外部链接。找到`<nav id="nav-menu" class="clearfix" role="navigation">`这行，在对应的`</nav>`标签前增加自定义链接：

```php
<a href="//blog.maojun.xyz">博客</a>
<a href="//maojun.xyz/feed/">RSS</a>
```

保存后，去页面刷新查看效果。

到此为止，配置后的 `Typecho` 已经能满足我的要求了。

（本文完）