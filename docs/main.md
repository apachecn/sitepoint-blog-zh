# 引入新的 HTML5

<main>元素</main>

> 原文：<https://www.sitepoint.com/html5-main-element/>

我们上一次收到新元素是什么时候？HTML5 只引入了 9 个新的结构标签，而且它们已经稳定了好几年:`section`、`article`、`aside`、`hgroup`、`header`、`footer`、`nav`、`figure`和`figcaption`。另外还有 20 个左右的内容元素，包括`video`、`audio`、`canvas`、`progress`等。

今天，我们要学习一个新的结构元素:

<main>根据[最新的 W3C 规范](https://www.w3.org/html/wg/drafts/html/master/grouping-content.html#the-main-element):</main>

> main 元素表示文档或应用程序主体的主要内容。主要内容区域由与文档的中心主题或应用程序的中心功能直接相关或扩展的内容组成。
> 
> 作者不能在一个文档中包含一个以上的主要元素。
> 
> 作者不得将主要元素作为文章、旁注、页脚、页眉或导航元素的子元素。

`main`标记你页面中的具体内容，即*“跳转到”*链接的目标。您可能会在以前有内容包装器的地方使用`main`;它将取代`<div id="main">`、`<div id="page">`或`<div id="wrapper">`等标签。如果你使用 ARIA，你会把它用于定义了`role="main"`的元素…

```
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<title>Using main</title>
</head>
<body>

	<header>My page</header>

	<nav>
		<a href="#">Home</a>
	</nav>

	<main>
		<article>
			<h1>My article</h1>
			<p>Content</p>
		</article>

		<aside>
			<p>More information</p>
		</aside>
	</main>

	<footer>Copyright 2013</footer>

</body>
</html> 
```

## 浏览器支持

虽然`main`是新的，但大多数浏览器都支持无法识别的标签。但是，您需要在 CSS 中应用块样式，即

```
main
{
	display: block
} 
```

随着浏览器的发展，这可能变得没有必要了(Chrome 和 Firefox 的夜间版本支持`main`)，但是保留它没有什么坏处。

这个元素也被添加到了 html5shiv 中，所以它可以在 IE6，7 和 8 中工作。如果您使用的是本地托管的文件，您可能需要下载新版本。

## 今天可以用

<main>吗？</main>

令人惊讶的是，这一元素引起了争议。有人认为这是不必要的，页面应该允许不止一个`main`标签。但它会一直存在，我认为它会有用。只要确保你已经彻底测试了你的新网站，然后才投入使用！

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如真实世界的 [HTML5 & CSS3。](https://learnable.com/courses/html5-css3-for-the-real-world-1484?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)

对本文的评论已经关闭。有关于 HTML5 的疑问？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?52-(X)HTML?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章