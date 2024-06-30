---
title: Typecho 默认主题功能增强
date: 2020/03/01 00:00
updated: 2023/09/05 7:36
categories:
- [技术, 示例]
tags:
- 博客平台
- Typecho
---

## 基础

在Typecho默认主题优化[^1]中对 `Typecho` 的主题进行了一些页面显示优化。在此基础上，增加一些配置让 `Typecho` 更加实用。



## 一言

 `Typecho` 默认主题的右侧搜索框下有空白区，显示有点空白。增加**一言**功能，让页面每次刷新或打开新页面时都可以显示**一言**，增加页面的趣味性。**一言**可以采用网络接口的方式或者读取本地的方式。这里采用本地读取**一言**,方便后期维护。

在默认的主题下，编辑 `function.php` 文件，追加以下内容:

```php
/**
*  每日一句
*
*/


function one_sentence(){

$filename = 'data.dat';
header('Content-type: text/html; charset=utf-8');

if(!file_exists($filename)) {
    die($filename . ' 数据文件不存在');
}

$data = file_get_contents($filename);
$data = explode(PHP_EOL, $data);
$result = $data[array_rand($data)];
$result = str_replace(array("\r","\n","\r\n"), '', $result);

echo $result;
}
```

编辑 `header.php` 文件，找到以下内容所在的行：

```php
<nav id="nav-menu" class="clearfix" role="navigation">
</nav>
```

在 `</nav>` 之前增加函数调用语句：

```php
<span class="kit-hidden-tb" style="float:right"> <?php echo one_sentence();?> </span>
```

> `class`的样式是让**一言**在小屏幕不显示，宽屏时右对齐。



在**根目录**添加 `data.dat` 文件,添加需要显示的**一言**内容:

```dat
往者不可谏，来着犹可追。——《论语·微子》
多行不义必自毙。——《左传》
敏而好学，不耻下问。——《论语·公冶长》
避其锐气，击其惰归。——《孙子兵法·军争》
十年树木，百年树人。——《管子·权修》
居安思危，思则有备，有备无患。——《左传》
天时不如地利，地利不如人和。——《孟子·公孙丑》
人谁无过？过而能改，善莫大焉。——《论语》
信言不美，美言不信。——老子
满招损，谦受益。——《尚书·大禹谟》
高岸为谷，深谷为陵。——《诗经·小雅》
天作孽，犹可违，自作孽，不可活。——《尚书》
言之无文，行而不远。——《左传》
三军可夺帅也，匹夫不可夺志也。——《论语·子罕》
天行健，君子以自强不息。——《周易·乾·象》
皮之不存，毛将焉附。——《左传》
路漫漫其修远兮，吾将上下而求索。——屈原《离骚》
长太息以掩涕兮，哀民生之多艰。——屈原《离骚》
人而无仪，不死何为。——《诗经·鄘风》
捐躯赴国难，视死忽如归。——曹植《白马篇》
天下之事常成于困约，而败于奢靡。——陆游
知之者不如好之者，好之者不如乐之者。——《论语·雍也》
志当存高远。——诸葛亮《诫外生书》
不去庆父，鲁难未已。——《左传》
老吾老，以及人之老；幼吾幼，以及人之幼。——《孟子·梁惠王下》
博学之，审问之，慎思之，明辨之，笃行之。——《中庸》
人非圣贤，孰能无过。——《训俗遗规》
亦余心之所善兮，虽九死其犹未悔。——《屈原·离骚》
若要功夫深，铁杵磨成针。——曹学《蜀中广记·上川南道彭山县》
少壮不努力，老大徒悲伤。——汉乐府古辞《长歌行》
穷则独善其身，达则兼济天下。——《孟子·尽心上》
仁者见仁，智者见智。——《易经·系辞上》
青，取之于蓝而青于蓝；冰，水为之而寒于水。——《荀子·劝学》
千羊之皮，不如一狐之腋。——《史记》
余将董道而不豫兮，固将重昏而终身。——《屈原·涉江》
高山仰止，景行行止。——《诗经·小雅·车辖》
锲而舍之，朽木不折；锲而不舍，金石可镂。——《荀子·劝学》
不傲才以骄人，不以宠而作威。——诸葛亮
尺有所短；寸有所长。物有所不足；智有所不明。——屈原《卜居》
言必信，行必果。——《论语·子路》
有志者事竟成。——《后汉书·耿列传》
其身正，不令而行；其身不正，虽令不从。——论语·子路
三人行，必有我师焉：择其善而从之，其不善者而改之。——《论语·述而》
非学无以广才，非志无以成学。——《三国·诸葛亮·诫子书》
绳锯木断，水滴石穿。——罗大经《鹤林玉露》
君子坦荡荡，小人长戚戚。——孔子
老当益壮，宁知白首之心；穷且益坚，不坠青云之志。——王勃
尺有所短，寸有所长。——《史记》
他山之石，可以攻玉。——《诗经·小雅·鹤鸣》
苟余心之端直兮，虽僻远其何伤？——《屈原·涉江》
人有不为也，而后可以有为。——《孟子·离娄下》
路漫漫其修远今，吾将上下而求索。——屈原
孔子登东山而小鲁，登泰山而小天下。——《孟子·尽心上》
积土而为山，积水而为海。——《荀子·儒效》
生于忧患，死于安乐。——《孟子·告子下》
知足不辱，知止不殆。——老子
桃李不言，下自成蹊。——《史记》
傲不可长，欲不可纵，乐不可极，志不可满。——魏徵
既来之，则安之。——《论语·季氏》
知己知彼，百战不殆。——《孙子兵法·谋攻》
真者，精诚之至也，不精不诚，不能动人。——《庄子·渔夫》
独学而无友，则孤陋而寡闻。——《礼记·杂记》
勿以恶小而为之，勿以善小而不为。惟贤惟德，能服于人。——刘备
```

> 注意：一言的内容不要过长，不然页面显示效果不好。



保存后刷新页面即可查看效果。



## 博客总访问

为了统计博客一共被访问了多少次，可以在页面底部增加统计功能。在默认主题目录下编辑 `function.php` 内容，追加以下内容：

```php
function total_view()
        {
            $db = Typecho_Db::get();
            $row = $db->fetchAll('SELECT SUM(VIEWS) FROM `typecho_contents`');
                echo number_format($row[0]['SUM(VIEWS)']);
        }
```

然后编辑 `footer.php` 文件，在 `</footer>` 标签前添加：

```php
<?php _e('Total UV): '); ?><?php get_total_view($this); ?>.
```

保存后刷新页面即可查看效果。



## 文章更新时间：

`Typecho `默认的文章页面只显示创建时间，而没有更新时间。不方便浏览者知晓页面内容是否有更新变化。同样在默认主题的目录下编辑 `post.php` 文件,在 `<ul class="post-meta"></ul>` 下找到之前的**时间**文字并将其修改为**发布时间**，然后在下面增加一行代码显示**更新时间**

```php
<li><?php _e('更新时间: '); ?><?php echo date('Y-m-d H:i:s', $this->modified);?></li>
```

保存刷新页面查看。



## 归档页自定义模板

`Typecho `默认的归档页面是有主页显示效果类似的。里面包含了文章内容，为了更直接的查看文章归档，可以只保留标题和评论数显示。在默认主题目录下新建 `timeline.php`（保留原先的 `archive.php` )文件：

```php
<?php if (!defined('__TYPECHO_ROOT_DIR__')) exit; ?>
<?php
/**
 * archive
 *
 * @package custom
 */
 $this->need('header.php'); ?>

<div class="col-mb-12 col-12" id="main" role="main">
    <article class="post" itemscope itemtype="http://schema.org/BlogPosting">
        <h1 class="post-title" itemprop="name headline"><a itemprop="url" href="<?php $this->permalink() ?
>"><?php $this->title() ?></a></h1>

<?php if($this->user->hasLogin()):?>
<a href="<?php $this->options->adminUrl(); ?>write-page.php?cid=<?php echo $this->cid;?>" >编辑</a>
<?php endif;?>


        <div class="post-content" itemprop="articleBody">
<?php $this->widget('Widget_Contents_Post_Recent', 'pageSize=10000')->to($archives);
    $year=0; $mon=0; $i=0; $j=0;
    $output = '<div id="archives">';
    while($archives->next()):
        $year_tmp = date('Y',$archives->created);
<?php $this->widget('Widget_Contents_Post_Recent', 'pageSize=10000')->to($archives);
<?php if (!defined('__TYPECHO_ROOT_DIR__')) exit; ?>
<?php
/**
 * archive
 *
 * @package custom
 */
 $this->need('header.php'); ?>

<div class="col-mb-12 col-12" id="main" role="main">
    <article class="post" itemscope itemtype="http://schema.org/BlogPosting">
        <h1 class="post-title" itemprop="name headline"><a itemprop="url" href="<?php $this->permalink() ?
>"><?php $this->title() ?></a></h1>

<?php if($this->user->hasLogin()):?>
<a href="<?php $this->options->adminUrl(); ?>write-page.php?cid=<?php echo $this->cid;?>" >编辑</a>
<?php endif;?>


        <div class="post-content" itemprop="articleBody">
<?php $this->widget('Widget_Contents_Post_Recent', 'pageSize=10000')->to($archives);
    $year=0; $mon=0; $i=0; $j=0;
    $output = '<div id="archives">';
    while($archives->next()):
        $year_tmp = date('Y',$archives->created);
        $mon_tmp = date('m',$archives->created);
        $y=$year; $m=$mon;
        if ($mon != $mon_tmp && $mon > 0) $output .= '</ul></li>';
        if ($year != $year_tmp && $year > 0) $output .= '</ul>';
        if ($year != $year_tmp) {
            $year = $year_tmp;
            $output .= '<h3>'. $year .' 年</h3><ul>';
        }
        if ($mon != $mon_tmp) {
            $mon = $mon_tmp;
            $output .= '<li><span>'. $mon .' 月</span><ul>';
        }
        $output .= '<li>'.date('d日: ',$archives->created).'<a href="'.$archives->permalink .'">'. $archiv
es->title .'</a> <em>('. $archives->commentsNum.')</em></li>';
    endwhile;
    $output .= '</ul></li></ul></div>';
    echo $output;
?>
        </div>
    </article>
</div><!-- end #main-->

<?php $this->need('sidebar.php'); ?>
<?php $this->need('footer.php'); ?>
```

然后登入到 `Typecho` 的管理后台，新建**独立页面**,然后选择默认为 **timeline** ，保存后点击新建的页面名称查看效果。



## 分页标题

`Typecho `的文章分页标题默认都是一样的，为了更好的区分页面。可以在不同页面中显示不同的页面标题。编辑 `header.php` 文件，在 <`title></title>` 尾部添加以下内容：

```php
<?php if($this->_currentPage>1) echo ' _ 第 '.$t
his->_currentPage.' 页 '; ?>
```

保存后刷新页面即可查看到效果。



## 浏览器标识

可以给访问的用户添加浏览器标识，在默认主题目录下编辑 `function.php` 文件，追加以下内容：

```php
/** 获取浏览器信息 */
function getBrowser($agent)
{ $outputer = false;
    if (preg_match('/MSIE\s([^\s|;]+)/i', $agent)) {
        $outputer = '<i></i> IE浏览器';
    } else if (preg_match('/FireFox\/([^\s]+)/i', $agent)) {
        $outputer = '<i></i> 火狐浏览器';
    } else if (preg_match('/Maxthon([\d]*)\/([^\s]+)/i', $agent)) {
        $outputer = '<i></i> 傲游浏览器';
    } else if (preg_match('#SE 2([a-zA-Z0-9.]+)#i', $agent)) {
        $outputer = '<i></i> 搜狗浏览器';
    } else if (preg_match('#360([a-zA-Z0-9.]+)#i', $agent)) {
        $outputer = '<i></i> 360浏览器';
    } else if (preg_match('/Edge([\d]*)\/([^\s]+)/i', $agent)) {
        $outputer = '<i></i> Edge';
    } else if (preg_match('/EdgiOS([\d]*)\/([^\s]+)/i', $agent)) {
        $outputer = '<i></i> Edge';
    } else if (preg_match('/UC/i', $agent)) {
        $outputer = '<i></i> UC浏览器 ';
    }else if (preg_match('/OPR/i', $agent)) {
        $outputer = '<i></i> 欧朋浏览器';
    } else if (preg_match('/MicroMesseng/i', $agent)) {
        $outputer = '<i></i> 微信内嵌浏览器';
    }  else if (preg_match('/WeiBo/i', $agent)) {
        $outputer = '<i></i> 微博内嵌浏览器';
    }  else if (preg_match('/QQ/i', $agent)||preg_match('/QQBrowser\/([^\s]+)/i', $agent)) {
        $outputer = '<i></i> QQ浏览器';
    } else if (preg_match('/MQBHD/i', $agent)) {
        $outputer = '<i></i> QQ浏览器 ';
    } else if (preg_match('/BIDU/i', $agent)) {
        $outputer = '<i></i> 百度浏览器';
    } else if (preg_match('/LBBROWSER/i', $agent)) {
        $outputer = '<i></i> 猎豹浏览器';
    } else if (preg_match('/TheWorld/i', $agent)) {
        $outputer = '<i></i> 世界之窗浏览器';
    } else if (preg_match('/XiaoMi/i', $agent)) {
        $outputer = '<i></i> 小米浏览器';
    } else if (preg_match('/UBrowser/i', $agent)) {
        $outputer = '<i></i> UC浏览器 ';
    } else if (preg_match('/mailapp/i', $agent)) {
        $outputer = '<i></i> email内嵌浏览器';
    } else if (preg_match('/2345Explorer/i', $agent)) {
        $outputer = '<i></i> 2345浏览器';
    } else if (preg_match('/Sleipnir/i', $agent)) {
        $outputer = '<i></i> 神马浏览器';
    } else if (preg_match('/YaBrowser/i', $agent)) {
        $outputer = '<i></i> Yandex浏览器';
    }  else if (preg_match('/Opera[\s|\/]([^\s]+)/i', $agent)) {
        $outputer = '<i></i> Opera浏览器';
    } else if (preg_match('/MZBrowser/i', $agent)) {
        $outputer = '<i></i> 魅族浏览器';
    } else if (preg_match('/VivoBrowser/i', $agent)) {
        $outputer = '<i></i> vivo浏览器';
    } else if (preg_match('/Quark/i', $agent)) {
        $outputer = '<i></i> 夸克浏览器';
    } else if (preg_match('/mixia/i', $agent)) {
        $outputer = '<i></i> 米侠浏览器';
    }else if (preg_match('/fusion/i', $agent)) {
        $outputer = '<i></i> 客户端';
    } else if (preg_match('/CoolMarket/i', $agent)) {
        $outputer = '<i></i> 基安内置浏览器';
    } else if (preg_match('/Thunder/i', $agent)) {
        $outputer = '<i></i> 迅雷内置浏览器';
    } else if (preg_match('/Chrome([\d]*)\/([^\s]+)/i', $agent)) {
        $outputer = '<i></i> Chrome ';
    } else if (preg_match('/safari\/([^\s]+)/i', $agent)) {
        $outputer = '<i></i> Safari';
    } else{
        return false;
    }
   return $outputer;
}
/** 获取操作系统信息 */
function getOs($agent)
{
    $os = false;

    if (preg_match('/win/i', $agent)) {
        if (preg_match('/nt 6.0/i', $agent)) {
            $os = '<i></i> Windows Vista';
        } else if (preg_match('/nt 6.1/i', $agent)) {
            $os = '<i></i> Windows 7';
        } else if (preg_match('/nt 6.2/i', $agent)) {
            $os = '<i></i> Windows 8';
        } else if(preg_match('/nt 6.3/i', $agent)) {
            $os = '<i></i> Windows 8.1';
        } else if(preg_match('/nt 5.1/i', $agent)) {
            $os = '<i></i> Windows XP';
        } else if (preg_match('/nt 10.0/i', $agent)) {
            $os = '<i></i> Windows 10';
        } else{
            $os = '<i></i> Windows';
        }
    } else if (preg_match('/android/i', $agent)) {
if (preg_match('/android 9/i', $agent)) {
        $os = '<i></i> Android P';
    }
else if (preg_match('/android 8/i', $agent)) {
        $os = '<i></i> Android O';
    }
else if (preg_match('/android 7/i', $agent)) {
        $os = '<i></i> Android N';
    }
else if (preg_match('/android 6/i', $agent)) {
        $os = '<i></i> Android M';
    }
else if (preg_match('/android 5/i', $agent)) {
        $os = '<i></i> Android L';
    }
else{
        $os = '<i></i> Android';
}
    }
 else if (preg_match('/ubuntu/i', $agent)) {
        $os = '<i></i> Linux';
    } else if (preg_match('/linux/i', $agent)) {
        $os = '<i></i> Linux';
    } else if (preg_match('/iPhone/i', $agent)) {
        $os = '<i></i> iPhone';
    } else if (preg_match('/iPad/i', $agent)) {
        $os = '<i></i> iPad';
    } else if (preg_match('/mac/i', $agent)) {
        $os = '<i></i> OSX';
    }else if (preg_match('/cros/i', $agent)) {
        $os = 'chrome os';
    }else {
 return false;
    }
   return $os;
}
```

然后在对应需要显示浏览器标识的地方输出以上函数即可。



## 默认后台地址

默认的后台登入地址是域名后跟`/admin`，如果熟悉`Typecho`的用户就有可能尝试登入。为了安全可以将其修改为其他登入地址，进入网站的**根目录**，编辑 **config.inc.php** 文件，找到下面这行，将`/admin`修改成自己想要的后台登入地址。

```php
/** 后台路径(相对路径) */
define('__TYPECHO_ADMIN_DIR__', '/admin/');
```

保存后将**根目录**的 **admin** 文件夹重名名修改的地址。

保存后刷新页面查看效果。



## 分页页数

`Typecho `的默认分页数量是 `5`，可以将其增加到 `10 `提高阅读体验。编辑默认主题目录下的 `functioin.php` 文件，追加以下内容：

```php
/**
* 自定义分页数量
*/
function themeInit($archive) {
    if ($archive->is('index')) {
                $archive->parameter->pageSize = 10;
        }elseif($archive->is('archive')){
                $archive->parameter->pageSize = 10;
        }
}
```

保存后刷新即可。



## 文章自动摘要

`Typecho` 的文章在主页默认是全文显示了，可以在后台编写文章时通过**摘要分隔线**功能划分显示的摘要部分，每次手动设置容易忘记。为了简单起见，可以在代码中设置自动摘要内容。在默认主题目录下编辑 `index.php` ，找到以下内容：

```php
<?php $this->content('阅读剩余部分'); ?>
```

将其内容替换为

```php
<?php $this->excerpt(200, '...'); ?>
```

上面的`200``数值可以自行修改，表示截取多少个字节作为摘要显示。



## GitHub star

分享 GitHub 仓库时可以让其显示 `star` 的数量，只需要增加以下代码即可：

```html
<iframe src="https://ghbtns.com/github-btn.html?user=amplest&repo=molerose-typecho-themes-dev&type=star&count=true" frameborder="0" scrolling="0" width="170px" height="20px"></iframe>
```



## 文章回复可见

文章正文设置回复可见。在默认主题目录下编辑 `post.php` 文件， 找到 `<?php $this->content(''); ?>` ,将其替换为：

```php
<?php
$db = Typecho_Db::get();
$sql = $db->select()->from('table.comments')
    ->where('cid = ?',$this->cid)
    ->where('mail = ?', $this->remember('mail',true))
    ->limit(1);
$result = $db->fetchAll($sql);
if($this->user->hasLogin() || $result) {
    $content = preg_replace("/\[hide\](.*?)\[\/hide\]/sm",'<div class="reply2view"><i class="icon-lock-open"></i>$1</div>',$this->content);
}
else{
    $content = preg_replace("/\[hide\](.*?)\[\/hide\]/sm",'<div class="reply2view"><i class="icon-lock"></i>此处内容需要评论回复后方可阅读。</div>',$this->content);
}
echo $content
?>
```



（本文完）

[^1]: [Typecho 默认主题优化 ](https://maojun.xyz/blog/2020/02/Typecho默认主题优化.html)



**参考链接：**

- [Typecho非插件实现内容回复可见功能](https://web.archive.org/web/20201019231412/https://www.lanka.cn/Typecho-1_3284.html#comment-763)

