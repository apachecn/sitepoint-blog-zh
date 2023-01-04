# PHP 中如何避免 404 和重定向旧网址

> 原文：<https://www.sitepoint.com/how-to-redirect-old-urls-php/>

没有什么可以说是确定的，除了死亡和税收。和 URL 更改。经常需要重新组织你的站点和改变 URL 结构，但是，假设你有相似的内容，用户应该很少会遇到“没有找到页面”的错误。产生不必要的 404 页是我的十大开发错误之一。在本文中，我们将创建一个自动的 PHP 重定向系统，将旧的 URL 转换成新的地址。这不是生产代码，但它将说明基础知识，以便您可以将它用于您自己的网站。

## 1.创建 404 错误处理文件

如果你还没有一个“未找到”的页面，在你网站的根目录下创建一个名为**404.php**的基本页面:

```
<?php// basic 404 error pageheader('HTTP/1.1 404 Not Found');header('Status: 404 Not Found');?><!DOCTYPE html><html lang="en"><head><meta charset="UTF-8"><title>Page not found</title></head><body><h1>Page not found</h1><p>Sorry, we cannot find that page.</p><p><a href="/">Please return to the home page&hellip;</a></p></body></html>
```

**Note:** What’s a 404?

404 是在服务器上找不到资源时返回的 HTTP 错误号。上述文件顶部的 PHP 代码返回此代码，以确保搜索引擎等系统不会将页面误认为是真实内容。

## 2.配置您的服务器

您现在需要告诉您的服务器，所有 404 错误都应该由 404.php 文件来处理。如果您使用的是 Apache，将下面一行添加到。网站根目录中的 htaccess 文件:

```
errordocument 404 /404.php
```

对于 IIS，打开 Internet 信息服务管理器。在 IIS7 中，双击“错误页面”图标。(以前版本的用户必须选择网站属性的“自定义错误”选项卡。)编辑 404 错误代码，选择“URL”类型，输入“/404.php”作为地址。如果你现在访问一个不存在的页面，比如 http://yoursite.com/non-existent.url,，你应该会看到我们上面创建的错误页面。

## 3.创建重定向系统

我们将把重定向代码放在另一个名为**redirect.php**的文件中，以保持功能独立于 404 内容。将下面的代码添加到 404.php 文件的顶部，紧接着<？php 声明:

```
include('redirect.php');
```

现在在网站根目录下创建**redirect.php**，并添加以下代码:

```
<?php// current address$oldurl = strtolower($_SERVER['REQUEST_URI']);// new redirect address$newurl = '';
```

当前页面地址存储在$oldurl 中，例如/non-existent.url，我们现在需要检查该地址，如果可能的话，将其转换为新的 url(存储在$newurl 变量中)。如何实现这一点将取决于你的新旧网址的结构。例如，如果唯一的更改是您的“blog”文件夹已被重命名为“blogs”，则以下代码就足够了:

```
$newurl = str_replace('blog', 'blogs', $oldurl);
```

你可以使用一系列的替换或正则表达式。或者，可以在数组或数据库表中定义每个旧 URL 到其新地址的映射。如果您有相当简单的重定向需求，可以使用下面的代码:

```
$redir = array(	'blog' => '/blogs/',	'video' => '/videos/',	'demo' => '/demonstrations/main/');while ((list($old, $new) = each($redir)) && !$newurl) {	if (strpos($oldurl, $old) !== false) $newurl = $new;}
```

$redir 数组定义了许多可以相应配置的值对。如果在旧 URL 中的任何地方都能找到第一个字符串，则重定向地址被设置为第二个字符串。在上面的例子中，如果在丢失的页面 URL 中找到单词“blog ”,用户将被重定向到“/blogs/”。如果旧的 URL 包含两个或更多这样的词，第一个优先；例如，/video/examples 将首先找到“video”并重定向到“/videos/”。一旦有了$newurl 值，就可以在重定向之前仔细检查它是否存在。不幸的是，这带来了一些风险:

*   新 URL 可能缺少物理文件；例如，你正在使用 WordPress 永久链接。PHP 的 file_exists()函数将找不到任何东西。
*   您可以使用 file()或 http_get()之类的函数来检查 URL 是否存在；但是，如果没有，您将返回到您的 redirect.php 文件——这可能会导致递归重定向尝试。

就我个人而言，我会避免重复检查网址，除非这样做很容易。如果您的重定向 URL 不正确，在测试过程中或通过检查您的服务器日志文件，很快就会发现这一点。最后，如果我们有一个$newurl，我们将重定向到该页面。否则，我们将显示 404.php 错误:

```
// redirectif ($newurl != '') {	header('HTTP/1.1 301 Moved Permanently');	header("Location: $newurl");	exit();}?>
```

这段代码是一个简单的例子，但是您应该能够将它应用于任何情况。您还可以考虑其他选项，例如:

*   将所有未映射的 URL 记录到一个文件中，供以后检查
*   通过存储 cookie、使用会话值或向新 URL 传递参数来防止多次重定向错误

希望你觉得有用。在即将发布的一篇新的 SitePoint 文章中，我将向你展示如何修改 WordPress 安装的代码…

## 分享这篇文章