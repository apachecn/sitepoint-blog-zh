# 如何让 Ajax 应用程序可以被 Google 抓取

> 原文：<https://www.sitepoint.com/google-crawl-index-ajax-applications/>

Ajax 改变了网络。微软早在 1999 年就开创了这项技术，但是在杰西·詹姆斯·加勒特于 2005 年发明了这个缩写词之后，Web 2.0 应用出现了爆炸式增长。

尽管 Ajax 使 web 应用程序更加流畅，响应速度更快，但是它不能被搜索引擎索引。爬网程序无法运行 JavaScript 内容更新功能。在极端情况下，所有谷歌可能的索引是:

```
 <html>
	<head>
		<title>My Ajax-powered page</title>
		<script type="text/JavaScript" src="application.js"></script>
	</head>
	<body>
	</body>
</html> 
```

足以让一个 SEO 高手晕倒。

## 谷歌来拯救？

谷歌发明了一种新技术来使 Ajax 应用程序可以被抓取。这有点复杂，但这里有一个实现的总结。

**1。告诉爬虫你的 Ajax 链接**

假设您的应用程序通过对以下地址的 Ajax 调用来检索数据:

```
 www.mysite.com/ajax.php#state 
```

您需要修改该 URL，并在散列后添加一个感叹号:

```
 www.mysite.com/ajax.php#!state 
```

HTML 中的链接将被遵循，但是如果这些新链接通常是在 JavaScript 代码中生成的，那么你应该将它们添加到站点地图中。

**2。提供 Ajax 内容**

Google crawler 将翻译您的 Ajax URL 并请求以下页面:

```
 www.mysite.com/ajax.php?_escaped_fragment_=state 
```

请注意，参数是转义的；例如，' & '将作为' %26 '传递。因此，您需要取消转义并解析该字符串。PHP 的`urldecode()`函数会做得很好。

您的服务器现在必须返回整个页面的快照，也就是 Ajax 调用执行后 HTML 代码的样子。换句话说，这是动态更新后 Firebug 的 HTML 窗口的翻版。

如果 Ajax 请求通常通过 PHP 或 ASP.NET 进程返回 HTML，这应该相对简单。然而，如果您通常返回由 JavaScript 解析并插入到 DOM 中的 XML 和 JSON，情况会变得稍微复杂一些。Google 建议创建页面的静态版本，或者使用诸如 [HtmlUnit](http://htmlunit.sourceforge.net/) 之类的系统以编程方式获取 JavaScript 执行后的 HTML。

这是该技术的本质，但我建议[阅读谷歌的文档](http://code.google.com/intl/sv-SE/web/ajaxcrawling/)——还有几个其他问题和边缘案例。

## 这是进步(ive)吗？

与流行的观点相反，使用 Ajax 的网站可以被搜索引擎索引…如果你采用[渐进式增强技术](https://www.sitepoint.com/progressive-enhancement-1-html/)。不使用 JavaScript 的页面将被谷歌和其他搜索引擎编入索引。然后，您可以应用少量的 Ajax 技巧来逐步增强那些启用了 JavaScript 的用户的体验。

例如，假设您想一次翻阅 [SitePoint 图书馆](https://www.sitepoint.com/books/)的十本书。将创建一个带有导航链接的标准的纯 HTML 请求/响应页面；例如:

library.php

```
<html>
<head>
<title>SitePoint library</title>
</head>
<body>
<table id="library">
<?php
// show books at appropriate page
?>
</table>
<ul>
	<li><a href="library.php?page=-1">BACK</a></li>
	<li><a href="library.php?page=1">NEXT</a></li>
</ul>
</body>
</html>

```

该页面可以被谷歌抓取，链接将被跟踪。没有 JavaScript 的用户也可以使用该系统。JavaScript 渐进式增强现在可以添加到:

1.  检查 ID 为“library”的表是否存在
2.  向导航链接添加事件处理程序，单击后
3.  取消标准导航事件并启动一个 Ajax 调用。这将检索新数据并更新表，而无需刷新整个页面。

虽然我欢迎谷歌的解决方案，但它看起来像是一把复杂的大锤，用来敲开一个小小的坚果。如果你对渐进式增强和 Hijax 等技术感到困惑，你肯定会在实现谷歌的爬行系统时遇到麻烦。然而，它可能会帮助那些使用单一 Ajax 应用程序的人，他们在考虑 SEO 时已经太晚了。

## 分享这篇文章