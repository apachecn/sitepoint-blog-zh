# 更快的工作流程:掌握 Emmet，第 2 部分

> 原文：<https://www.sitepoint.com/faster-workflow-mastering-emmet-part-2/>

在[第 1 部分](https://www.sitepoint.com/faster-workflow-mastering-emmet-part-1/)中，我们了解了 Emmet 的特性以及它如何让您的工作流程更快。在这一部分，我们将学习如何安装和使用 Emmet。

Emmet 可用于很多文本编辑器，但我将使用 [SublimeText](http://www.sublimetext.com/) ，这是一个非常流行且功能丰富的文本编辑器。如果你使用另一个文本编辑器，在 http://emmet.io/download/的[查看文档。](http://emmet.io/download/)

## SublimeText 中的 Emmet 装置

在 SublimeText 中安装 Emmet 需要两个步骤。首先我会安装 SublimeText 的[包控件，然后我会安装 Emmet 插件。](https://sublime.wbond.net/installation)

### 步骤 1:安装软件包控制

有了 SublimeText 包控制，我们可以很容易地安装新的插件。安装 SublimeText 后，访问 https://sublime.wbond.net/installation 的复制 SublimeText2 或 SublimeText3 的代码——无论您使用哪个版本——按`ctrl+``或转到`View > Show Console`。打开后，将适合您的 SublimeText 版本的代码粘贴到控制台中。安装完成后，重新启动 SublimeText。

### 步骤 2:安装 Emmet 插件

重启 SublimeText 后，转到`Preferences > package Control`并键入`install package`。在出现的窗口中，键入/搜索 **Emmet** 并选择安装。安装完成后，重新启动 SublimeText，以便更改生效..

## Emmet 入门

如果你从来没有用过 Emmet，你知道编写 HTML 代码需要时间。假设你创建了一个新的 HTML 文件，你需要一些必要的标签`html, head, title, body`和`HTML5 doctype`，如下所示:

```
<!doctype HTML>
<HTML Lang="en">
<head>
	<meta charset="UTF-8">
	<title>Document</title>
</head>
<body>

</body>
</HTML>
```

您可能有一个默认的模板来开始，或者您可能保存代码，每次都只是复制和粘贴代码。如果你这样做，你是在浪费你的宝贵时间。使用 Emmet，创建上述代码大约需要两三秒钟。创建一个新文件并另存为。html(例如 index.html)并键入`html:5`或`!`并在文本编辑器中按下`tab`或`ctrl+e`或任何其他触发键来扩展缩写，您将拥有 HTML5 doctype 和一些必要的标签。

在 SublimeText 中`Tab`是用于扩展缩写的键。让我们从一些基础开始，稍后我们将学习更高级的例子。用 SublimeText 创建一个新文件，另存为 emmet.html 或 index.html。现在输入`HTML:5`或`!`并按下`Tab`，你将拥有我们页面的基本 HTML 代码。

## 一些有用的 HTML 快捷方式

HTML 和 CSS 有很多快捷方式，这里是一些最广泛使用的。

**1:html 5 Doctype 的基本标记**

```
! or html:5
```

延伸到

```
<!doctype html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Document</title>
</head>
<body>

</body>
</html>
```

**2:带源的脚本**

```
script:src
```

延伸到

```
<script src=""></script>
```

**3:兼容 X-UA 的元标签**

```
meta:compat
```

延伸到

```
<meta http-equiv="X-UA-Compatible" content="IE=7">
```

**4 : Meta viwe 端口标签**

```
meta:vp
```

延伸到

```
<meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
```

**5 : CSS 样式**

```
style
```

延伸到

```
<style></style>
```

**6:链接图标**

```
link:favicon
```

延伸到

```
<link rel="shortcut icon" type="image/x-icon" href="favicon.ico">
```

**7:RSS 链接**

```
link:rss
```

延伸到

```
<link rel="alternate" type="application/rss+xml" title="RSS" href="rss.xml">
```

**8:原子的链接**

```
link:atom
```

延伸到

```
<link rel="alternate" type="application/atom+xml" title="Atom" href="atom.xml">
```

**9:锚标签**

```
a
```

延伸到

```
<a href=""></a>
```

**10:带默认值的链接**

```
a:link
```

延伸到

```
<a href="http://"></a>
```

**11:邮件链接**

```
a:mail
```

延伸到

```
<a href="mailto:"></a>
```

**12:图像**

```
img
```

延伸到

```
<img src="" alt="">
```

**13:带有 get 方法的表单**

```
form:get
```

延伸到

```
<form action="" method="get"></form>
```

**14:带方法 post 的表单**

```
form:post
```

延伸到

```
<form action="" method="post"></form>
```

**15:默认输入**

```
input
```

延伸到

```
<input type="text">
```

**16:输入隐藏**

```
input:hidden
```

延伸到

```
<input type="hidden" name="">
```

**17:按钮**

```
btn
```

延伸到

```
<button></button>
```

**18:按钮提交**

```
btn:s
```

延伸到

```
<button type="submit"></button>
```

**19:必要的表格标记**

```
table+
```

延伸到

```
<table>
	<tr>
		<td></td>
	</tr>
</table>
```

**20:选择元素**

```
select+
```

延伸到

```
<select name="" id="">
	<option value=""></option>
</select>
```

**21:IE6 的条件样式**

```
cc:ie6
```

延伸到

```
<!--[if lte IE 6]>

<![endif]-->
```

**22:IE 的条件样式**

```
cc:ie
```

延伸到

```
<!--[if IE]>

<![endif]-->
```

**23:非 IE 浏览器的条件样式**

```
cc:noie
```

延伸到

```
<!--[if !IE]><!-->

<!--<![endif]-->
```

#### 埃米特小抄

这些只是 web 开发人员经常使用的一些标签。您可以在[http://docs.emmet.io/cheat-sheet/](http://docs.emmet.io/cheat-sheet/)查看支持语法的完整列表。

### CSS 样式表

您可以轻松添加默认 CSS 或打印样式表。只需键入`link`作为基本链接，键入`link:CSS`作为默认的 style.css 文件，键入`link:print`作为打印样式表。您可以覆盖默认属性值并添加新值。也可以添加子元素。

**1。基本 CSS 链接**

```
Link 
```

将转换为下面的代码，在空的`href=""`属性中有制表位。

```
<link rel="stylesheet" href="">

```

**2。链接默认 style.css 链接**

```
Link:css 
```

将转换成以下代码

```
<link rel="stylesheet" href="style.css">	
```

**3。链接打印样式表**

```
Link:print 
```

会转变成。

```
<link rel="stylesheet" href="print.css" media="print">	
```

**4。覆盖默认属性**

```
 link[rel=prefetch title="Hello world"] 
```

延伸到

```
<link rel="prefetch" href="" title="Hello world">	
```

**5。添加子元素**

```
link>xsl:apply-templates 
```

延伸到

```
<link rel="stylesheet" href="">
	<xsl:apply-templates></xsl:apply-templates>
</link>
```

### 理解类似 CSS 的缩写

Emmet 中的缩写使得编写繁琐的标记代码变得轻而易举。缩写是为 HTML 和 XML 生成而优化的，但不限于此。缩写的语法看起来像 CSS 选择器，所以如果你熟悉 CSS，学习它并不困难。

让我们使用缩写为我们的网页创建一些基本的 HTML 标记，包括页眉、主体和页脚部分以及必要的类。

让我们从一个非常基本的例子开始。只是打字

```
header#site-header	
```

你会看到

```
<header id="site-header"></header>
```

如果你打字

```
header#site-header>h1.logo>a{site name}
```

并按 Tab 键，它将展开到

```
<header id="site-header">
	<h1 class="logo"><a href="">site name</a></h1>
</header>
```

### 页面的基本标记

若要使用基本的必要标记创建网页，请键入以下代码，并在编辑器中按 tab 或 Ctrl+E。

```
header#site-header^div.container#main>.primary+aside#sidebar^footer#site-footer
```

上述缩写将生成以下标记。如您所见，我们的页面有三个部分:首先，我们有一个 id 为`site-header`的`header`，然后我们有一个包含类`.container`和 id `main`的主 div，然后我们有一个 id 为`site-footer`的页脚部分。您可以将 id 和类应用于任何元素，就像我已经应用于我们的`<div class="container" id="main">`一样

```
<header id="site-header"></header>

<div class="container" id="main">
	<div class="primary"></div>
	<aside id="sidebar"></aside>
</div>

<footer id="site-footer"></footer>
```

**一个更复杂的例子**:在上面的例子中，我为我们的网页创建了部分(页眉、主页面、主要页面、侧页、页脚)，但是现在我将在我们的部分中添加更多的信息和标记。Emmet 非常强大，可以生成非常复杂的代码。在下面的例子中，我将为整个网页创建标记。看看它，然后我会定义它。

```
(header#site-header>h1.logo>a[href=#]{site name})^div.container#main>(.primary>h1.post-title{post title here}+img.featured-img+p{post text here})+(aside#sidebar>#widget>h2.widget-title+p{widget text})^footer.#site-footer>.col-4.widget*3>h2.widget-title+p.widget-text
```

上述代码将生成以下 HTML 标记:

```
<header id="site-header">
	<h1 class="logo"><a href="#">site name</a></h1>
</header>

<div class="container" id="main">
	<div class="primary">
		<h1 class="post-title">post title here</h1>
		<img src="" alt="" class="featured-img">
		<p>post text here</p>
	</div>
	<aside id="sidebar">
		<div id="widget">
			<h2 class="widget-title"></h2>
			<p>widget text</p>
		</div>
	</aside>
</div>

<footer class="" id="site-footer">
	<div class="col-4 widget">
		<h2 class="widget-title"></h2>
		<p class="widget-text"></p>
	</div>
	<div class="col-4 widget">
		<h2 class="widget-title"></h2>
		<p class="widget-text"></p>
	</div>
	<div class="col-4 widget">
		<h2 class="widget-title"></h2>
		<p class="widget-text"></p>
	</div>
</footer>
```

现在仔细看看上面的代码，注意我用来生成上面代码的函数和符号。我用括号`()`将标题部分分组，然后我用`^`爬上一个部分，将标题和#主容器分开。为了让上面的长缩写更简单，我们把它分成三部分。

**这个缩写将为标题部分生成标记:**

```
(header#site-header>h1.logo>a[href=#]{site name})^
```

**这个缩写将为主要部分生成标记:**

```
div.container#main>(.primary>h1.post-title{post title here}+img.featured-img+p{post text here})+(aside#sidebar>#widget>h2.widget-title+p{widget text})^
```

**这个缩写将为页脚部分生成标记:**

```
footer.#site-footer>.col-4.widget*3>h2.widget-title+p.widget-text
```

你可以看到我不止一次地使用了`()`和`^`来分隔各个部分，并生成更复杂的代码。

在第 3 部分中，我将介绍语法和函数。

## 分享这篇文章