---
title: 修复 Ubuntu 下 “门” 或 “复” 字显示错误
date: 2023/09/11 12:19
updated: 2023/09/11 12:19
categories:
- [技术, Linux]
tags:
- 操作系统
- Ubuntu
- 字体
---

##  问题

在使用 Ubuntu 系统时，当输入  “门”或“复”字时，显示的文字有些别扭。认为是 Linux 字体原因显示的不正常，后来才发现这是日文汉字，可以通过修改配置文件显示为中文汉字。



## 原因

Ubuntu 在当前所使用的字体中不包含某个字符时，会按照顺序从一系列候选字体中选择包含字符的字体。候选列表配置文件 ：

```sh
/etc/fonts/conf.d/64-language-selector-prefer.conf
```

 分为：

- 衬线字体
- 非衬线字体
- 等宽字体

Ubuntu 在安装了中文语言的情况下将 [Noto](https://www.google.com/get/noto) 系列字体添加到字体候选

例如衬线字体优先度如下：

1. Noto Sans CJK JP
2. Noto Sans CJK KR
3. Noto Sans CJK SC
4. Noto Sans CJK TC
5. Noto Sans CJK HK

这样的配置导致日文字体包含的字符以日文字体显示。例如 “门”或“复”字，日文中存在这个汉字所以就会用日文字体显示。

其他在日文中不存在的汉字，或在 `Noto Sanas CJK JP` 和 `Noto Sans CJK SC` 字型相同的汉字则显示正常。



## 修复

修改配置文件 :

```sh
vim /etc/fonts/conf.d/64-language-selector-prefer.conf
```

将中文字体的优先度提高( 带有`SC`的提到第一位 )：

```xml
<?xml version="1.0"?>
<!DOCTYPE fontconfig SYSTEM "fonts.dtd">
<fontconfig>
	<alias>
		<family>sans-serif</family>
		<prefer>
			<family>Noto Sans CJK SC</family>
			<family>Noto Sans CJK TC</family>
			<family>Noto Sans CJK HK</family>
			<family>Noto Sans CJK JP</family>
			<family>Noto Sans CJK KR</family>
			<family>Lohit Devanagari</family>
		</prefer>
	</alias>
	<alias>
		<family>serif</family>
		<prefer>
			<family>Noto Serif CJK SC</family>
			<family>Noto Serif CJK TC</family>
			<family>Noto Serif CJK JP</family>
			<family>Noto Serif CJK KR</family>
			<family>Lohit Devanagari</family>
		</prefer>
	</alias>
	<alias>
		<family>monospace</family>
		<prefer>
			<family>Noto Sans Mono CJK SC</family>
			<family>Noto Sans Mono CJK TC</family>
			<family>Noto Sans Mono CJK HK</family>
			<family>Noto Sans Mono CJK JP</family>
			<family>Noto Sans Mono CJK KR</family>
		</prefer>
	</alias>
</fontconfig>
```



更新字体缓存：

```sh
fc-cache -fv
```

执行以下命令检查，如果出现 `NotoSansCJK-Regular.ttc: "Noto Sans CJK SC" "Regular"` 则表示设置成功：

```sh
fc-match -s | grep 'Noto Sans CJK'
```

(本文完)



**参考链接：**

- [Ubuntu 下 “门” 和 “复” 显示错误](https://bediverezero.github.io/ubuntu/2021/06/04/ubunt-fonts.html)
- [Ubuntu Linux '门' '复' 显示不标准](https://blog.csdn.net/yi0322/article/details/113699524)

