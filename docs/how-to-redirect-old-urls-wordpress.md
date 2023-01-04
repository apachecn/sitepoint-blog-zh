# 在 WordPress 中重定向旧的 URL

> 原文：<https://www.sitepoint.com/how-to-redirect-old-urls-wordpress/>

我们最近设计了一个系统来[重定向 PHP](https://www.sitepoint.com/how-to-redirect-old-urls-php/) 中的旧 URL，这样你就可以避免“找不到页面”的错误(我建议你在冒险之前先阅读一下)。在本文中，我们将为流行的 PHP<acronym title="Content Management System">CMS</acronym>WordPress 创建一个类似的系统。

## WordPress 有什么不同？

WordPress 通过一个单独的`index.php`文件发送所有的请求。在正常情况下，所有没有映射到你的服务器上的物理文件或文件夹的 URL 都将被 WordPress 处理。这包括所有无效地址，所以不需要在 Apache 或 IIS 中配置 404 错误文档。

如果你从第一天开始就使用 WordPress，可能就没有必要担心重定向旧的 URL。即使你改变了永久链接的结构(设置>永久链接)，系统也能很好地找到正确的页面。然而，如果您从静态站点或另一个 CMS 迁移内容，您几乎肯定会遇到 URL 不匹配的情况。

## 更新主题

我们将在你的 WordPress 主题中处理重定向。你可以创建一个插件，但是:

*   重定向是特定于站点的，通用插件会增加复杂性
*   代码不太可能需要进一步的更新，最好避免让用户配置或禁用它，并且
*   将它嵌入到主题中使它更容易移植——你只需将主题文件复制到服务器上

首先，在`wp-content/themes/*my-theme-name*`中找到你的主题文件。许多主题都提供了一个`404.php`文件来处理“找不到页面”的错误，但是如果需要的话，你也可以创建一个；例如:

```
 <?php get_header(); ?>

<h1>Page not found</h1>
<p>Sorry, we cannot find that page.</p>
<p>Please browse the sitemap&hellip;</p>

<ul id="sitemap">
<?php wp_list_pages('title_li='); ?>
</ul>

<?php get_footer(); ?> 
```

您现在应该在`404.php`文件的顶部包含`redirect.php`，例如:

```
 <?php include('redirect.php'); ?>
<?php get_header(); // etc... 
```

(请注意，WordPress 的早期版本可能需要:`include(TEMPLATEPATH.'/redirect.php');`–这在最新版本中也适用。)

现在在主题文件夹中创建`redirect.php`文件，并添加以下代码:

```
 <?php
// current address
$oldurl = strtolower($_SERVER['REQUEST_URI']);

// new redirect address
$newurl = '';

// old to new URL map (for you to configure)
$redir = array(

	'index.html' => '/',
	'article1.html' => '/blogs/my-first-article',
	'article2.html' => '/blogs/my-second-article'

);

while ((list($old, $new) = each($redir)) && !$newurl) {
	if (strpos($oldurl, $old) !== false) $newurl = $new;
}

// redirect
if ($newurl != '') {

	header('HTTP/1.1 301 Moved Permanently');
	header("Location: $newurl");
	exit();

}
?> 
```

可以根据需要配置`$redir` URL 映射数组。或者，您可以使用一系列正则表达式替换或数据库表查找来找到有效的`$newurl`。

我希望这对你的 WordPress URL 困境有所帮助。如果您有其他管理重定向的技巧，请在下面留下您的评论。

## 分享这篇文章