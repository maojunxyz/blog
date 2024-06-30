---
title: Markdown导出带大纲目录的Html
date: 2024/04/04 22:40:25
updated: 2024/04/04 23:15:31
categories:
- [技术, 示例]
tags:
- Markdown
- Typora
- Html
---

## Typora导出HTML

### 原生侧边栏导出

Typora导出HTML是支持导出目录大纲的，导出的时候勾选**保留大纲侧边栏**。

![导出保留大纲侧边栏](./assets/image-20240404225835454.png)

导出后的样式有些美观，侧边栏左侧空隙过大。但是锚点和功能都可以正常使用，是一种快速方便的导出HTML且保留大纲的方式。

![默认导出大纲样式](./assets/image-20240404230059074.png)

### 自定义导出样式

根据通过引入外部样式来自定义修改导出的HTML样式。导出的自定义样式效果如下：

![自定义导出大纲样式](./assets/image-20240404230400534.png)

#### 配置步骤

1. 下载`jquery-3.3.1.min.js`文件到本地文件。

   [下载地址](https://code.jquery.com/jquery-3.3.1.min.js)

2. 保存下面的css内容为`md2html.js`文件。

```js
//是否显示导航栏
 var showNavBar = true;
 //是否展开导航栏
 var expandNavBar = true;

 $(document).ready(function(){

    var h1s = $("body").find("h1");
    var h2s = $("body").find("h2");
    var h3s = $("body").find("h3");
    var h4s = $("body").find("h4");
    var h5s = $("body").find("h5");
    var h6s = $("body").find("h6");

   var links = document.links;
   for (var i = 0; i < links.length; i++) {
    if (!links[i].target) {
        if (
            links[i].hostname !== window.location.hostname || 
            /\.(?!html?)([a-z]{0,3}|[a-zt]{0,4})$/.test(links[i].pathname)
        ) {
            links[i].target = '_blank';
            } 
        }
    }

    var headCounts = [h1s.length, h2s.length, h3s.length, h4s.length, h5s.length, h6s.length];
    // 获取所有样式
    var vH1Tag = null;
    var vH2Tag = null;
    var vH3Tag = null;
    var vH4Tag = null;
    var vH5Tag = null;
    var vH6Tag = null;
    for(var i = 0; i < headCounts.length; i++){
        if(headCounts[i] > 0){
            if(vH1Tag == null){
                vH1Tag = 'h' + (i + 1);
            }else if(vH2Tag == null){
                vH2Tag = 'h' + (i + 1);
            }else if(vH3Tag == null){
                vH3Tag = 'h' + (i + 1);
            }else if(vH4Tag == null){
                vH4Tag = 'h' + (i + 1);
            }else if(vH5Tag == null){
                vH5Tag = 'h' + (i + 1);
            }else if(vH6Tag == null){
                vH6Tag = 'h' + (i + 1);
            }
        }
    }
    if(vH1Tag == null){
        return;
    }

    // 导航侧边栏
    $("body").prepend('<div class="BlogAnchor">' + 
        
        '<p class="html_header">' + 
            '<span></span>' + 
        '</p>' + 
        '<div class="AnchorContent" id="AnchorContent"> </div>' +
    '</div>' );

    var menuIndex = 0;
    $("body").find("h1,h2,h3,h4,h5,h6").each(function(i,item){
        var id = '';
        var tag = $(item).get(0).tagName.toLowerCase();
        var className = '';
        if(tag == vH1Tag){
            className = 'item_h1';
        }else if(tag == vH2Tag){
            className = 'item_h2';
        }else if(tag == vH3Tag){
            className = 'item_h3';
        }else if(tag == vH4Tag){
            className = 'item_h4';
        }else if(tag == vH5Tag){
            className = 'item_h5';
        }else if(tag == vH6Tag){
            className = 'item_h6';
        }

        //修改 id 生成策略
        id = "_" + menuIndex;
        menuIndex++;

        $(item).attr("id","wow"+id);
        $(item).addClass("wow_head");
        $("#AnchorContent").css('max-height', ($(window).height() - 80) + 'px');
        $("#AnchorContent").append('<li><a class="nav_item '+className+' anchor-link" onclick="return false;" href="#" link="#wow'+id+'">'+""+""+$(this).text()+'</a></li>');
    });

    
    $(".anchor-link").click(function(){
        $("html,body").animate({scrollTop: $($(this).attr("link")).offset().top}, 500);
    });

    var headerNavs = $(".BlogAnchor li .nav_item");
    var headerTops = [];
    $(".wow_head").each(function(i, n){
        headerTops.push($(n).offset().top);
    });
    $(window).scroll(function(){
        var scrollTop = $(window).scrollTop();
        $.each(headerTops, function(i, n){
            var distance = n - scrollTop;
            if(distance >= 0){
                $(".BlogAnchor li .nav_item.current").removeClass('current');
                $(headerNavs[i]).addClass('current');
                return false;
            }
        });
    });

    if(!showNavBar){
        $('.BlogAnchor').hide();
    }
    if(!expandNavBar){
        $("#AnchorContent").hide();
    }
 });


 $(window).resize(function() {
  $("#AnchorContent").css('max-height', ($(window).height() - 80) + 'px');
});


//插入title的ico图标  
var ico_link = "<link rel=icon  sizes=32x32 href=favicon.ico>";
$("head").prepend(ico_link);
```



3. 保存下面的css内容为md2html.css文件。

```css
/*markdown_box*/
    html{background: #F4F6F9; font-family:"Microsoft YaHei", "微软雅黑", Arial; font-weight: 400}
    body{background:#F4F6F9;font-size: 16px; color: #515254; line-height: 188%; margin: 0 !important; padding: 0 !important;}
    .html_header{display:block; height: 60px; line-height: 46px; border-bottom: 1px solid #E6ECF1; text-align: left !important;}
    .html_header span::after{content: "导航目录"; font-size: 17px; font-weight: bold;}
    .html_header span::before{content: "❖"; font-size: 19px; font-weight: bold; margin:0 8px 0 5px; color: #1284d9}
    blockquote{border-radius:0 4px 4px 0;color:#708090;border-left:3px solid #1284d9; background:rgba(238,242,249,.7); padding:8px 16px; font-size: 15px;}
    code{padding: 2px 4px 1px; font-weight: bold; margin:0 3px; border:1px solid rgba(255,130,130,.3);background:rgba(255,130,120,.05); color:#F66; font-family:arial;}
    a{color: #1284d9}
    a:visited{color:#1284d9}
    a:hover{text-decoration: none; color: #087cd2}

    h2{margin: 40px 0 20px; font-size: 36px; font-weight: 400}
    h3{margin-top: 40px; font-size: 30px; font-weight: 400}
    img{border-radius:5px;margin:30px 0 46px !important;width:100%;max-width: 1100px; opacity: .6; transition: opacity .3s}
    img:hover{opacity: 1}


    #write{margin:50px 50px 50px 260px; padding: 20px 60px; max-width: unset; background: #fff;border: 1px solid #E6ECF1; box-shadow: 0 2px 8px rgba(116, 129, 141, .08); border-radius: 4px}
    .BlogAnchor {width: 330px !important}

    @media screen and (max-width: 1620px) {
        #write{margin:40px 40px 40px 250px; padding: 20px 40px;}
        .BlogAnchor {width: 330px !important}
    }

    @media screen and (max-width: 1370px) {
        #write{margin:30px 30px 30px 230px; padding: 20px 40px;}
        .BlogAnchor {width: 200px !important}
    }

    @media screen and (max-width: 1220px) {
        #write{margin:20px 20px 20px 220px; padding: 20px 40px;}
    }

    @media screen and (max-width: 820px) {
        #write{margin:20px 20px 20px 200px; padding: 20px 20px; font-size: 15px; line-height: 170%}
        .BlogAnchor {width: 180px !important}
        .html_header span::before{ margin-left: 0}
        .html_header span::after{ font-size: 15px;}
        h2{ font-size: 30px; }
        h3{ font-size: 24px; }
    }

    @media screen and (max-width: 640px) {
        #write{margin:10px; padding: 16px; font-size: 14px; line-height: 160%}
        .BlogAnchor {display: none;}
        h2{ font-size: 28px; }
        h3{ font-size: 22px; }
    }


/*scroll_style*/
.AnchorContent {
    overflow-y: auto !important;
    overflow-x: hidden !important;
    margin-right:0 !important
}

.AnchorContent::-webkit-scrollbar {
    width: 6px;
    height: 10px;
    background-color: rgba(0,0,0,0) !important;
    position: absolute;
    right: 10px
}
.AnchorContent::-webkit-scrollbar-track {
    -webkit-box-shadow: none;
    border-radius: 0;
    background-color: rgba(0,0,0,0) !important
}
.AnchorContent::-webkit-scrollbar-thumb {
    border-radius: 0px;
    -webkit-box-shadow: none;
    background-color: none;
    opacity: 0 !important;
    transition: all .3s
}

.AnchorContent:hover::-webkit-scrollbar-thumb {
    border-radius: 0px;
    -webkit-box-shadow: none;
    background-color: #d4d6d8;
    border-right:2px solid #fff;
    opacity:1;
}

.AnchorContent::-webkit-scrollbar-thumb:hover {
    background-color: #c0c2c4 !important
}


/*导航*/
    .BlogAnchor {
        background: #fff;
        padding: 10px 0 ;
        line-height: 180%;
        position: fixed;
        height: 100%;
        width: 200px;
        left: 0;
        top: 0;
        border-right: 1px solid #E6ECF1;

    }
    .BlogAnchor p {
        font-size: 20px;
        color: #444;
        margin: 0 16px;
        text-align: center;
    }
    .BlogAnchor .AnchorContent {
        padding: 5px 0px;
        overflow: auto;
    }
    .BlogAnchor li{
        text-indent: 1.5rem;
        font-size: 14px;
        list-style: none;
        padding: 0 0px;
    }
    .BlogAnchor li .nav_item{
        padding:3px 16px;

    }
    .BlogAnchor li .item_h1{
        margin-left: 0rem;
        padding-left:3px;
        font-size: 1rem;
        font-weight: bold;
    }
    .BlogAnchor li .item_h2{
        margin-left: 0;
        font-size: 0.9rem;
        padding-left: 18px;
        font-weight: bold;
    }
    .BlogAnchor li .item_h3{
        margin-left: 0;
        font-size: 0.8rem;
        padding-left: 33px;
    }
    .BlogAnchor li .item_h4{
        margin-left: 0;
        font-size: 0.7rem;
        padding-left: 48px;
    }
    .BlogAnchor li .item_h5{
        margin-left: 0;
        font-size: 0.7rem;
        padding-left: 63px;
    }
    .BlogAnchor li .item_h6{
        margin-left: 0;
        font-size: 0.7rem;
        padding-left: 78px;
    }
    .BlogAnchor li .nav_item.current{
        color: #087cd2;
        background-color: rgba(238,242,249,.8);
        border-left:4px solid #087cd2;
        text-indent: 20px;
        font-weight: bold;

    }
    #AnchorContentToggle {
        font-size: 13px;
        font-weight: normal;
        color: #FFF;
        display: inline-block;
        line-height: 20px;
        background: rgb(7,196,189);
        font-style: normal;
        padding: 1px 8px;
    }
    .BlogAnchor a:hover {
        color: #087cd2  ;
        background:rgba(238,242,249,.5);
    }
    .BlogAnchor a {
        text-decoration: none;
        color: #333;
        display: block;
        border-radius: 0px;
    }
```



4. 设置Typora导出的偏好，将下载和保存的文件配置到导出HTML的header位置。

```html
<script type="text/javascript" src="C:\Users\maojun\code\static\jquery-3.3.1.min.js"></script>
<script type="text/javascript" src="C:\Users\maojun\code\static\md2html.js"></script>
<link rel="stylesheet" type="text/css" href="C:\Users\maojun\code\static\md2html.css">
```

![导出HTML添加样式](./assets/image-20240404225227093.png)

> 注意，不要勾选保留大纲侧边栏，否则会生成2个侧边栏。



（完）



**参考**

- [Typora 2 Html带目录](https://github.com/icanleric/other/blob/master/Typora%202%20Html%E5%B8%A6%E7%9B%AE%E5%BD%95.md)