# 一个最小的 HTML 文档(HTML5 版)

> 原文：<https://www.sitepoint.com/a-minimal-html-document-html5-edition/>

早在 2008 年，我发布了一个标签集的[明细，你应该在每个 HTML 文档中包含最少的标签。如你所见，当时有很多东西需要理解:](https://www.sitepoint.com/a-minimal-html-document/)

> 如今，可访问性、搜索引擎优化、JavaScript 操作的文档一致性以及对国际字符的支持都要求我们使用更多的 HTML。最小的 HTML 文档变得更大了。

从那以后，HTML5 工作组花了很多心思来精简这个最小的标签集。原来所有主流浏览器都同意几个可以减少代码的快捷键，而 [HTML5 规范](https://www.w3.org/TR/html5/)现在允许在有效代码中使用这些快捷键。

因为所有的浏览器(甚至像 IE6 这样的老浏览器)都完全支持 HTML5 中正在被标准化的快捷方式，所以我们今天可以使用它们；尽管在浏览器赶上之前，HTML5 的大多数新功能仍然是禁区。

有了这些快捷方式，下面是一个 HTML 文档现在应该包含的最基本的内容，假设它链接了 CSS 和 JavaScript:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>title</title>
    <link rel="stylesheet" href="style.css">
    <script src="script.js"></script>
  </head>
  <body>
    <!-- page content -->
  </body>
</html>
```

请继续阅读下面的内容，了解这个最小文档中每一行的完整描述。

## 崩溃

每个 HTML 文档都应该以一个 [`<!DOCTYPE>`声明](https://reference.sitepoint.com/html/doctypes)开始，这个声明告诉浏览器这个文档是用哪个版本的 HTML 编写的。HTML5 文档要求的`<!DOCTYPE>`比之前的要短很多:

```
<!DOCTYPE html>
```

像所有这些快捷方式一样，这段代码是专门设计来“愚弄”当前浏览器(还不支持 HTML5)将文档视为纯 HTML4 文档的。早在 Internet Explorer 6 之前的浏览器版本将使用最符合标准的呈现模式来呈现页面。

接下来，我们用开始的 [`<html>`标签](https://reference.sitepoint.com/html/html)来标记文档的开始。这个标签应该用 [`lang`属性](https://reference.sitepoint.com/html/core-attributes/lang)指定文档内容的主要语言:

```
<html lang="en">
```

接下来是 [`<head>`标签](https://reference.sitepoint.com/html/head)，它开始文档头:

```
 <head>
```

头中的第一位应该是一个 [`<meta>`](https://reference.sitepoint.com/html/meta) 标签，指定页面的[字符编码](https://www.sitepoint.com/guide-web-character-encoding/)。通常，字符编码是由将页面发送到浏览器的 web 服务器声明的，但是许多服务器没有配置为发送此信息。在这里指定它可以确保文档正确显示，即使它是直接从磁盘加载的，也不需要咨询服务器。

同样，与 HTML4 相比，HTML5 大大缩短了这个标记，但是，和以前一样，这个快捷方式利用了所有当前浏览器的现有错误处理行为，所以现在使用是安全的:

```
 <meta charset="utf-8">
```

随着编码的建立，我们可以安全地在页面中写入第一段实际内容——页面 [`title`](https://reference.sitepoint.com/html/title) :

```
 <title>title</title>
```

如果你想把一个 CSS 文件链接到页面上来控制它的外观(你通常会这样做)，此时一个 [`<link>`](https://reference.sitepoint.com/html/title) 标签就可以做到:

```
 <link rel="stylesheet" href="style.css">
```

HTML4 中需要的`type="text/css"`属性现在在 HTML5 中是可选的，并且所有当前的浏览器都知道如果您省略该属性该怎么做。

如果您想将一个 JavaScript 脚本链接到页面，并且该脚本被设计为从页眉调用，那么在这里插入一个 [`<script>`](https://reference.sitepoint.com/html/script) 标签。与`<link>`标签不同，`<script>`必须与完整的`</script>`结束标签配对:

```
 <script src="script.js"></script>
```

再次强调，在 HTML5 中,`type="text/javascript"`属性是可选的，当你忽略它时，当前所有的浏览器都可以正常工作。

差不多就这样了。你可以结束页眉，然后用一个 [`<body>`](https://reference.sitepoint.com/html/body) 标签开始页面主体。页面的内容由您决定，但是因为我们讨论的是一个最小的文档，所以不需要任何正文内容:

```
 </head>
  <body>
    <!-- page content -->
  </body>
</html>
```

你觉得怎么样？有惊喜吗？

如果你和我一样，这里呈现的一些快捷方式乍一看会让你感到有些不安。当当前的浏览器还不支持大部分 HTML5 时，使用 HTML5 `<!DOCTYPE>`声明真的安全吗？

虽然从一个尚未被支持的规范中采用代码看起来很奇怪，但 HTML5 正是以这种方式被采用的。上面提到的快捷方式并不是新标准的特性，而是对浏览器内置的 HTML 解析特性的一种更有效的利用。

既然 [W3C HTML 验证器](http://validator.w3.org/)支持 HTML5，它将验证包含这些快捷方式的文档；真的没有理由再走长路了。

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如真实世界的 [HTML5 & CSS3。](https://learnable.com/courses/html5-css3-for-the-real-world-1484?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)

对本文的评论已经关闭。有关于 HTML5 的疑问？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?52-(X)HTML?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章