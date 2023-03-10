# WordPress 中提要的开发者指南

> 原文：<https://www.sitepoint.com/developers-guide-to-feeds-in-wordpress/>

提要是一种数据格式，用于向用户提供频繁更新的内容。[提要阅读器](https://www.sitepoint.com/12-free-google-reader-alternatives/)用于阅读提要，从而允许用户订阅它。让一个提要集合可以在一个地方访问被称为聚合，这是由提要聚合器执行的。

![WordPress Feeds](img/a05ad169ab5a143ad8b2dd1141a5ef57.png)

在本教程中，我将向你展示 WordPress 提供的不同种类的 feed 格式以及如何访问它们，重点是通过编程定制它们。

## WordPress 支持的 Feed 格式

WordPress 支持四种流行的提要格式:RDF、RSS 0.92、RSS 2.0 和 Atom。RSS 2.0 是最流行的提要格式，几乎每个提要阅读器都支持它。

这些源的 URL 路径是:

```
http://example.com/?feed=rss
http://example.com/?feed=rss2
http://example.com/?feed=rdf
http://example.com/?feed=atom

```

您不必将它们硬编码到您的主题或插件中，您只需使用以下函数来检索各种提要格式的 URL:

```
<?php 
    bloginfo('rdf_url'); 
    bloginfo('rss_url'); 
    bloginfo('rss2_url'); 
    bloginfo('atom_url');
?>
```

这些 URL 检索网站的最新“帖子”。

## WordPress 最近评论提要

以上路径提供了该网站的最新帖子。要查找最近的评论，我们需要使用特定于评论的反馈路径:

```
http://example.com/?feed=comments-rss2
```

如果您不想硬编码它，您可以使用这个函数来检索评论提要:

```
<?php
    bloginfo('comments_rss2_url');
```

WordPress 也为单个帖子的评论提供了 feed。要查找特定帖子的最近评论，订阅路径是:

```
http://example.com/?p=id&feed=rss2
```

同样，如果您不想硬编码它，您可以使用这个函数来检索它:

```
<?php
    post_comments_feed_link('link_text', 'post_id', 'rss2' );
```

## WordPress 分类摘要

WordPress 为每个类别提供了一个 feed。单个类别或多个类别的馈送路径是:

```
http://www.example.com/?cat=id1,id2&feed=rss2
```

您可以使用此函数检索单个类别或多个类别的提要 URL，方法是:

```
<?php
    get_category_feed_link('id1', 'rss2');
```

同样，您也可以获得一个或多个标签的提要 URL。

## WordPress 作者订阅源

WordPress 也提供了一个作者最近文章的提要。如果用户想通过提要关注某个特定的作者，这是很有用的。

作者提要的 URL 路径可以使用下面的代码构建:

```
<?php 
    echo '<a href="' . get_author_link(0, $authordata->ID, $authordata->user_nicename) . 'feed/">' . the_author($idmode, false) . '</a>';
```

注意:WordPress 没有提供任何内置的函数来获取作者提要的 URL。

## WordPress 搜索源

WordPress 也可以检索搜索词的提要。以下是搜索词的源 URL 路径:

```
http://example.com/?s=sitepoint&feed=rss2
```

## 检查 Feed 请求

在你的主题或插件中，你可以使用下面的代码检查一个请求是否是一个 feed 请求:

```
<?php
    if(is_feed())
    {
        echo "Feed Request";
    }
```

这里我们使用`is_feed()`函数来检查当前请求是否是一个提要请求。如果是，则返回 true。

WordPress 内部使用一个内置的 feed 模板来显示 feed。使用`do_feed`动作，我们可以定义自己的提要模板。

## 删除订阅源

如果您不想在您的站点上提供提要，那么您可以使用下面的代码来禁用所有提要:

```
<?php

    function disable_feed() 
    {
        wp_die(__("Feed Disabled"));
    }
    add_action('do_feed',      'disable_feed', 1);
    add_action('do_feed_rdf',  'disable_feed', 1);
    add_action('do_feed_rss',  'disable_feed', 1);
    add_action('do_feed_rss2', 'disable_feed', 1);
    add_action('do_feed_atom', 'disable_feed', 1);
```

## 过滤馈送内容

要过滤一篇文章的描述，我们可以使用`the_content_feed`过滤器。下面是一个如何使用它的示例:

```
function feed_word_count($content)
{
   $content .= 'Total '.str_word_count($content).' words';
   return $content;
}

add_filter("the_content_feed", "feed_word_count");
```

## 自动馈送链接

从 WordPress 3.0 开始，有一个主题支持特性，可以将提要 URL 添加到`head`标签中。如果你是 WordPress 主题开发者，那么将这行代码添加到你的主题的`function.php`文件中来启用这个特性。

```
add_theme_support( 'automatic-feed-links' );
```

## 将 WordPress 订阅源重定向到 FeedBurner 订阅源

许多网站管理员更喜欢使用 [FeedBurner](http://www.feedburner.com/) (或其他第三方服务)来提供他们的 feeds。这让他们可以追踪他们的订阅源和订阅者数量。

肮脏的方法是将下面的代码添加到`.htaccess`文件中

```
<IfModule mod_rewrite.c>
 RewriteEngine on
 RewriteCond %{HTTP_USER_AGENT} !FeedBurner    [NC]
 RewriteCond %{HTTP_USER_AGENT} !FeedValidator [NC]
 RewriteRule ^feed/?([_0-9a-z-]+)?/?$ http://feeds.feedburner.com/name [R=302,NC,L]
</IfModule>
```

这段代码将请求重定向到 FeedBurner 的最新帖子。

如果你不愿意碰你的。htaccess 文件，然后你可以使用 [FeedBurner 插件](https://wordpress.org/plugins/feedburner-plugin/)来做同样的事情。

## 更多操作和过滤器

WordPress 提供了更多的动作和过滤器来定制我们的 feeds。我们只讨论了一些最重要和最有用的。

你可以在 [WordPress 动作参考](https://codex.wordpress.org/Plugin_API/Action_Reference#Feed_Actions)找到更多与 feed 相关的动作，在 [WordPress 过滤器参考](https://codex.wordpress.org/Plugin_API/Filter_Reference)找到过滤器。

## 结论

许多 WordPress 用户更喜欢订阅 feed，而不是电子邮件或社交订阅。由于现在所有平台都可以使用提要聚合器，所以为用户提供订阅提要的选项是一个好主意，RSS 2.0 是首选格式。如果启用了评论功能，您还应该显示作者提要和评论提要，而不仅仅是最近的帖子提要。

让我知道你使用 WordPress feeds 的体验。

## 分享这篇文章