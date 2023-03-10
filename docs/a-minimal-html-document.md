# 一个最小的 HTML 文档

> 原文：<https://www.sitepoint.com/a-minimal-html-document/>

我经常惊讶于有多少专业设计的网站是以不完整的 HTML 文档的形式发布的。然而，公平地说，这些年来，甚至一个空的 HTML 文档所需的代码量也显著增加了。

从前，一个 HTML 文档只需要包含一个`<!DOCTYPE>`声明和一个`<title>`标签。来自[的 HTML 3.2 推荐](https://www.w3.org/TR/REC-html32#html):

> 实际上，HTML、HEAD 和 BODY 的开始和结束标记可以从标记中省略[…]
> 
> 每个 HTML 3.2 文档还必须包含描述性的 title 元素。一个最小的 HTML 3.2 文档看起来像这样:
> 
> ```
>  <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 3.2 Final//EN">
>   <TITLE>A study of population dynamics</TITLE> 
> ```

在 HTML 3.2 作为推荐规范的时候，很少有网页设计师会为一个`<!DOCTYPE>`或者有效的代码而烦恼，所以在实践中，HTML 文档可以是包含文本和 HTML 标签任意组合的任何文本文件。

如今，可访问性、搜索引擎优化、JavaScript 操作的文档一致性以及对国际字符的支持都要求我们使用更多的 HTML。最小的 HTML 文档变得更大了。

假设 HTML 4 文档链接了 CSS 和 JavaScript，那么它至少应该包含以下内容:

```
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01//EN"
    "https://www.w3.org/TR/html4/strict.dtd">
<html lang="en">
  <head>
    <meta http-equiv="content-type" content="text/html; charset=utf-8">
    <title>title</title>
    <link rel="stylesheet" type="text/css" href="style.css">
    <script type="text/javascript" src="script.js"></script>
  </head>
  <body>

  </body>
</html>
```

如果您希望能够将文档处理为 XML，那么这个最小的 XHTML 1 文档应该是您的起点:

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"
    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
<html  lang="en" xml:lang="en">
  <head>
    <meta http-equiv="content-type" content="text/html; charset=utf-8"/>
    <title>title</title>
    <link rel="stylesheet" type="text/css" href="style.css"/>
    <script type="text/javascript" src="script.js"></script>
  </head>
  <body>

  </body>
</html>
```

请继续阅读以下内容，了解这些最小文档的每一行的描述。

## 崩溃

每个(X)HTML 文档都应该以一个 [`<!DOCTYPE>`声明](https://reference.sitepoint.com/html/doctypes)开始，这个声明告诉浏览器这个文档是用哪个版本的(X)HTML 编写的。实际上，这告诉像 Internet Explorer 和 Firefox 这样的浏览器使用它们最符合标准的(因此也是跨浏览器兼容的)呈现模式。`<!DOCTYPE>`声明的确切形式取决于您的文档是 HTML 4(适合大多数目的)还是 XHTML 1(使页面能够作为 XML 处理):

```
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01//EN"
    "https://www.w3.org/TR/html4/strict.dtd">
```

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"
    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
```

接下来，我们用开始的 [`<html>`标签](https://reference.sitepoint.com/html/html)来标记文档的开始。这个标签应该用 [`lang`属性](https://reference.sitepoint.com/html/core-attributes/lang)指定文档内容的主要语言:

```
<html lang="en">
```

在 XHTML 文档中，还应该指定文档的默认 XML 名称空间(使用`xmlns`属性),并使用 XML 的标准`xml:lang`属性重新指定语言:

```
<html  lang="en" xml:lang="en">
```

接下来是 [`<head>`标签](https://reference.sitepoint.com/html/head)，它开始文档头:

```
 <head>
```

头中的第一件事应该是一个 [`<meta>`](https://reference.sitepoint.com/html/meta) 标签，指定页面的[字符编码](https://www.sitepoint.com/guide-web-character-encoding/)。通常，字符编码是由将页面发送到浏览器的 web 服务器声明的，但是许多服务器没有配置为发送此信息，在此处指定字符编码可以确保文档正确显示，即使文档是直接从磁盘加载的，也不需要咨询服务器:

```
 <meta http-equiv="content-type" content="text/html; charset=utf-8">
```

在 XHTML 文档中，`<meta>`标签应该以斜杠结束，表示它们是空的:

```
 <meta http-equiv="content-type" content="text/html; charset=utf-8"/>
```

随着编码的建立，我们可以安全地在页面中写入第一段实际内容——页面 [`title`](https://reference.sitepoint.com/html/title) :

```
 <title>title</title>
```

如果你想把一个 CSS 文件链接到页面上来控制它的外观(你通常会这样做)，此时一个 [`<link>`](https://reference.sitepoint.com/html/title) 标签就可以做到:

```
 <link rel="stylesheet" type="text/css" href="style.css">
```

同样，这个标记的 XHTML 版本需要一个尾部斜杠来表示它是空的:

```
 <link rel="stylesheet" type="text/css" href="style.css"/>
```

如果您想将一个 JavaScript 脚本链接到页面，并且该脚本被设计为从页眉调用，那么在这里插入一个 [`<script>`](https://reference.sitepoint.com/html/script) 标签。无论文档是 HTML 还是 XHTML，为了向后兼容，都应该包含一个完整的`</script>`结束标记:

```
 <script type="text/javascript" src="script.js"></script>
```

差不多就这样了。你可以结束页眉，然后用一个 [`<body>`](https://reference.sitepoint.com/html/body) 标签开始页面主体。页面的内容由您决定，但是因为我们讨论的是一个最小的文档，所以不需要任何正文内容:

```
</head>
<body>

</body>
</html>
```

那么，你最近的作品怎么样？你包括了上面讨论的所有元素吗？像`lang`属性和`content-type` `<meta>`标签这样的常见省略看起来可能是不必要的，但它们确实为确保你的网站在网络上保持最佳状态添加了最后一层润色。

我很想听听你对上面讨论的基本 HTML 元素的想法，或者你绝对*总是*包含在你的页面中的任何其他元素。留下评论让我知道！

## 分享这篇文章