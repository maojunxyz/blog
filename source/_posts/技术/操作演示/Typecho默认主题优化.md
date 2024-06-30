---
title: Typecho 默认主题优化
date: 2020/02/24 00:00
updated: 2023/08/27 09:47
categories:
- [技术, 示例]
tags:
- 博客平台
- Typecho
---
##    优化需求

在博客迁移到Typecho[^1]文章中，介绍了迁移到`Typecho`后做的一些配置。为了让默认主题发挥出更好的效果，在原基础上对其进一步优化。主要优化的内容有：

- 前置上下篇文章
- 文章页编辑功能
- 独立页编辑功能
- 文章页阅读次数
- 文章页加载时间



## 前置上下篇文章

`Typecho`默认主题的上下篇文章是在评论输入框后面的。如果前置上下篇文章会和文章之间的关联性更强，方便用户点击查看。 编辑`post.php`文件，默认的代码顺序如下：

```php
<?php $this->need('comments.php'); ?>

   <ul class="post-near">
        <li>上一篇: <?php $this->thePrev('%s','没有了'); ?></li>
        <li>下一篇: <?php $this->theNext('%s','没有了'); ?></li>
    </ul>
```

将行尾部分的上下篇代码前置到评论前：

```php
<ul class="post-near">
        <li>上一篇: <?php $this->thePrev('%s','没有了'); ?></li>
        <li>下一篇: <?php $this->theNext('%s','没有了'); ?></li>
    </ul>

    <?php $this->need('comments.php'); ?>
```

保存退出刷新页面，即可查看到效果。



## 文章页编辑功能

`Typecho`的文章页面默认没有提供**编辑**的功能，需要登入到后台管理文章时才可以编辑。像 `WordPress` 之类的博客平台都有提供文章页面**编辑**功能。这个功能可以让有权限的用户快速的编辑文章。 在默认主题文件夹下编辑 `post.php` 文件，在带 `post-meta` 属性的 `ul` 标签下增加以下代码：

```php
<?php if($this->user->hasLogin()):?>
<a href="<?php $this->options->adminUrl(); ?>write-post.php?cid=<?php echo $this->cid;?>">编辑</a>
<?php endif;?>
```



## 独立页编辑功能

同文章页面设置，在默认主题文件夹下编辑 `page.php` 文件，在带 `post-content` 属性的 `div` 标签之前增加以下代码：

```php
<?php if($this->user->hasLogin()):?>
<a href="<?php $this->options->adminUrl(); ?>write-page.php?cid=<?php echo $this->cid;?>" >编辑</a>
<?php endif;?>
```



## 文章页阅读次数

在默认主题文件夹下编辑 `functions.php`，在行尾增加以下代码：

```php
/**
* 阅读次数统计
*
*/
function get_post_view($archive)
{
    $cid    = $archive->cid;
    $db     = Typecho_Db::get();
    $prefix = $db->getPrefix();
    if (!array_key_exists('views', $db->fetchRow($db->select()->from('table.contents')))) {
        $db->query('ALTER TABLE `' . $prefix . 'contents` ADD `views` INT(10) DEFAULT 0;');
        echo 0;
        return;
    }
    $row = $db->fetchRow($db->select('views')->from('table.contents')->where('cid = ?', $cid));
    if ($archive->is('single')) {
 $views = Typecho_Cookie::get('extend_contents_views');
        if(empty($views)){
            $views = array();
        }else{
            $views = explode(',', $views);
        }
if(!in_array($cid,$views)){
       $db->query($db->update('table.contents')->rows(array('views' => (int) $row['views'] + 1))->where('cid = ?', $cid));
array_push($views, $cid);
            $views = implode(',', $views);
            Typecho_Cookie::set('extend_contents_views', $views); //记录查看cookie
        }
    }
    echo $row['views'];
}
```

在需要统计阅读量的页面，如**文章查看页**增加阅读量。可以在默认主题文件夹下编辑 `post.php` 文件，在带 `post-meta` 属性的 `ul` 标签下增加以下代码：

```php
<li><?php _e('阅读量: '); ?><?php get_post_view($this); ?></li>
```



## 文章页加载时间

同阅读次数统计，先在默认主题文件夹下编辑 `functions.php` ,在行尾添加以下内容：

```php
/**
     * 加载时间
     * @return bool
     */
    function timer_start() {
        global $timestart;
        $mtime     = explode( ' ', microtime() );
        $timestart = $mtime[1] + $mtime[0];
        return true;
    }
    timer_start();
    function timer_stop( $display = 0, $precision = 3 ) {
        global $timestart, $timeend;
        $mtime     = explode( ' ', microtime() );
        $timeend   = $mtime[1] + $mtime[0];
        $timetotal = number_format( $timeend - $timestart, $precision );
        $r         = $timetotal < 1 ? $timetotal * 1000 . " ms" : $timetotal . " s";
        if ( $display ) {
            echo $r;
        }
        return $r;
    }
```

然后在需要统计阅读次数的页面，比如**文章查看页**增加加载时间统计。可以在默认主题文件夹下编辑 `post.php` 文件，在带 `post-meta` 属性的`ul`标签下增加以下代码：

```php
<li><?php _e('加载时间: '); ?><?php echo timer_stop();?> </li>
```

在做好以上的配置后，我们就可以刷新页面查看效果了。在配置后备份自己的主题，方便以后重装使用。

(完）

[^1]: [博客迁移到 Typecho](https://maojun.xyz/blog/2020/02/博客迁移到Typecho.html)