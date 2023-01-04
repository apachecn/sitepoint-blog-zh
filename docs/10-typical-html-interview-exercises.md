# 10 个典型的 HTML 面试练习

> 原文：<https://www.sitepoint.com/10-typical-html-interview-exercises/>

几周前，SitePoint 发表了我的两篇文章，讨论一些常见的 JavaScript 工作面试问题(如果你错过了，这两篇文章是 [5 个典型的 JavaScript 面试练习](https://www.sitepoint.com/5-typical-javascript-interview-exercises/)和 [5 个更多的 JavaScript 面试练习](https://www.sitepoint.com/5-javascript-interview-exercises/))。

这两篇文章在页面浏览量和分享量方面都取得了巨大的成功，这是相当出乎意料的。所以，我认为是时候在 HTML 上写一篇基于**工作面试问题的类似文章了，这是我努力的结果。玩得开心！**

## 1.标记验证

考虑以下标记:

```
<figure>
   <picture>
      <source media="(min-width: 40em)"
      srcset="large.jpg 1024w, medium.jpg 640w, small.jpg 320y">
      <img src="medium.jpg" alt="London by night">
   </picture>
   <figcaption>A landscape of London by night</figcaption>
</figure>
```

是否有效？如果没有，能解释一下原因吗？

### 回答

标记使用了图片元素[，这是规范中一个非常新的元素。除了在`srcset`属性中指定的最后一幅图像外，所有代码都有效；`320y`不是有效值。如果`y`被替换为`w`，它仍然有效。](https://www.w3.org/TR/html-picture-element/)

## 2.`main`元素

你能解释一下`main`元素的定义吗？它的目标是什么？两个规范(WHATWG 和 W3C)对它的定义一致吗？

### 回答

根据所使用的规范，`main`元素有两种不同的定义。

W3C 规范[将其](https://www.w3.org/TR/html51/grouping-content.html#the-main-element)描述为页面的主要内容，也就是说，描述页面主题的内容或者是应用程序的核心功能。该规范还规定一个文档不能包含一个以上的`main`元素。

WHATWG 规范没有给元素`main`赋予任何语义值，而[将它描述为另一个元素的主要内容的容器](https://html.spec.whatwg.org/multipage/semantics.html#the-main-element)。另外，根据 WHATWG 的说法，在一个文档中使用`main`元素的次数没有限制。如果你在一个页面上有多个`article`元素，你可能想用一个单独的`main`元素来标记每个`article`的主要内容。

## 3.WAI-ARIA

考虑下面的片段:

```
<header>
   <h1>Main title</h1>
   <form action="/" method="get">
      <input type="search">
      <input type="submit">
   </form>
</header>
<ul>
   <li><a href="/">Home</a></li>
   <li><a href="/products">Products</a></li>
   <li><a href="/about">About</a></li>
</ul>
<main>
   <article>
      <h1>Main title</h1>
      <p>This is the content of this section</p>
   </article>
</main>
<footer>
   <small>Copyright &amp;copy; Aurelio De Rosa 2014</small>
</footer>
```

考虑到旧的技术，你能在适当的时候使用 [WAI-ARIA 角色](https://www.w3.org/WAI/intro/aria.php)来提高它的可访问性吗？

### 回答

代码可以重写如下:

```
<header role="header">
  <h1>Main title</h1>
  <form action="/" method="get" role="search">
     <label for="search">Search:</label>
     <input id="search" type="search">
     <input type="submit">
  </form>
</header>
<nav role="navigation">
  <ul>
     <li><a href="/">Home</a></li>
     <li><a href="/products">Products</a></li>
     <li><a href="/about">About</a></li>
  </ul>
</nav>
<main role="main">
  <article role="article">
     <h1>Main title</h1>
     <p>This is the content of this section</p>
  </article>
</main>
<footer role="contentinfo">
  <small>Copyright &amp;copy; Aurelio De Rosa 2014</small>
</footer>
```

为了提高可访问性，主导航列表被包装了一个`nav`元素。为了提高不支持新语义元素的旧技术的可访问性，在`header`、`nav`、`main`、`article`和`footer`元素中分别添加了`header`、`navigation`、`main`、`article`和`contentinfo`的角色。

对搜索表单进行了其他改进。首先，表单已经使用`search`角色进行了标记。然后，添加了一个显式的`label`元素来为`input`字段提供上下文，并通过使用`for`属性将它与`input`关联起来。

## 4.`small`元素

描述何时适合使用`small`元素，并提供一个例子。

### 回答

在 HTML 4.01 中，`small`元素是用来标记较小文本的表示性元素。在 HTML5 中，它应该在语义上用来表示法律免责声明、警告等等。文本可能很“小”，但这不是必需的。

其使用示例如下所示:

```
<img src="image.jpg" alt="London by night">
<small>The copyright of this image is owned by Aurelio De Rosa</small>
```

## 5.副标题

副标题是任何网站中最常见的元素之一。几年前,`hgroup`标签被引入来满足这种需求，但它已经从规范中删除了。你能描述一下为什么`hgroup`被放弃了，以及现在如何处理这个标记吗？

### 回答

引入`hgroup`元素是为了将多个标题元素(`h1`–`h6`)分组，以避免在层次结构中创建非预期的子级别。为了理解它试图解决什么问题，让我们考虑下面的标记:

```
<article>
   <h1>Main title</h1>
   <h2>This is a subtitle</h2>
   <p>This is the content of this section</p>
</article>
```

概述上一个代码片段的文档层次结构为我们提供了以下表示形式:

```
h1
|
---h2
   |
   p
```

这个简单的模式显示，片段的段落内容被视为`h2`而不是`h1`的内容，不管这是否是预期的行为。因此，如果目的仅仅是创建一个副标题，并将`p`与`h1`相关联，那么最初的标记是不正确的。

引入了`hgroup`元素来轻松解决这个问题。因此在 2013 年 4 月从 HTML5 规范中移除，原因是缺乏实现和用例，使其使用无效。

创建副标题以使段落与`h1`相关联的可能解决方案如下所示:

```
<article>
   <h1>
       Main title
       <span>This is a subtitle</span>
   </h1>
   <p>This is the content of this section</p>
</article>
```

## 6.图像和可访问性

`alt`属性在`img`元素上是强制的吗？如果不是，你能描述一个可以设置为空值的场景吗？空值对可访问性有任何影响吗？

### 回答

[alt 属性](https://www.w3.org/TR/html5/embedded-content-0.html#alt)对于`img`元素是强制的，但其值可以为空(即`alt=""`)。当显示的图像仅用于装饰目的，因此不是页面内容的一部分时，建议使用空值。关于可访问性，如果`alt`属性为空，屏幕阅读器将忽略图像。强烈建议这样做，因为使用类似“内容分隔符”的值只会在朗读文本时打扰用户。

## 7.`time`元素

可以用一个单独的`time`元素来表示日期范围吗？

### 回答

不，这是不可能的。尽管可以使用两个[时间元素](https://www.w3.org/TR/html5/text-level-semantics.html#the-time-element)来表达该信息。例如，要描述从 2014 年 11 月 6 日到 2014 年 11 月 9 日的时间间隔，开发人员可以编写:

```
<time datetime="2014-11-06">6</time>-
<time datetime="2014-11-09">9 November 2014</time>
```

## 8.`meter`和`progress`

`meter`元素和`progress`元素有什么区别？

### 回答

[仪表元素](https://www.w3.org/TR/html5/forms.html#the-meter-element)代表一个在*已知*范围内的标量测量，或者一个分数值。这种元素不太适合测量像外部温度这样的东西，因为它没有固定的范围。但是，`meter`可以用来描述硬盘占用的内存。

[进度元素](https://www.w3.org/TR/html5/forms.html#the-progress-element)用于显示任务的完成进度。与`meter`元素不同，`progress`描述的进度是不确定的。例如，你可以描述给定的任务正在进行中，但是不知道任务何时完成。

## 9.`longdesc`属性

什么是`longdesc`属性？你能解释它的目的吗？

### 回答

[`img`元素](https://www.w3.org/TR/html-longdesc/)的`longdesc`属性在 HTML 4 中就已经存在，在 HTML5 中也有效。与`alt`属性提供的信息相比，该属性旨在提供图像的更详细描述。有趣的是，`longdesc`指向一个包含描述的超链接，而不是自己提供描述(像`alt`属性那样)。

下面是使用`longdesc`的一个例子:

```
<img src="italy.jpg"
     alt="This image represents the map of Italy" longdesc="italy.html#description">

<!-- other content here ... -->

<section id="description">
  <h2>Italy</h2>
  <p>The shown map of Italy illustrates its division 
  in regions...</p>
</section>
```

## 10.`mark`元素

什么是`mark`元素？你能描述一个使用这个元素的例子吗？

### 回答

[标记元素](https://www.w3.org/TR/html5/text-level-semantics.html#the-mark-element)代表高亮显示的文本。典型用途是突出显示用户搜索的一个或多个关键字的每个实例。

## 结论

在这篇文章中，我讨论了十个潜在的面试问题，你可以用它们来测试你的 HTML 知识。下一次面试中你可能会被问到这些问题中的一个或多个。

为了帮助您研究这些和其他与语义相关的主题，请务必查看文章中提供的规范链接，这里还有一些其他的 SitePoint 资源:

*   [html 5 中的 5 个过时特性](https://www.sitepoint.com/5-obsolete-features-html5/)
*   [html 5“时间”元素指南](https://www.sitepoint.com/html5-time-element-guide/)
*   [20 个 HTML 元素实现更好的文本语义](https://www.sitepoint.com/20-html-elements-better-text-semantics/)
*   [更换单选按钮而不更换单选按钮](https://www.sitepoint.com/replacing-radio-buttons-without-replacing-radio-buttons/)
*   [关于 HTML 的“pre”元素你需要知道的一切](https://www.sitepoint.com/everything-need-know-html-pre-element/)

你在面试中被问到过其他一些有趣的 HTML 问题吗？分享给我们吧，以后做职位面试的时候可能会对其他开发者有所帮助。

## 分享这篇文章