# HTML5 中的 5 个过时功能

> 原文：<https://www.sitepoint.com/5-obsolete-features-html5/>

2014 年 10 月 28 日，HTML5 成为 W3C 的推荐标准。HTML5 当然不是一个新话题，许多开发人员都知道它的特性:语义、可访问性、API 等等。

然而，在标准的发展过程中，一些元素、属性和 API 被添加，然后被更改或删除。因为很难跟上我们领域的所有新闻和更新，你们中的一些人可能已经错过了已经被否决或从规范中删除的特性。在这篇文章中，我们将涵盖其中的五个。

## 过时与弃用

在我们深入研究本文将要介绍的特性之前，我想确保您知道*已废弃的*(一个你们大多数人可能都很熟悉的术语)和*过时的*之间的区别。

HTML 4.01 规范将不推荐使用的特性定义为不应该使用，但浏览器应该继续支持的特性。相反，过时的功能只是出于历史目的列出的，不需要浏览器支持。

[HTML5 规范](https://www.w3.org/TR/html5/)更新了术语。不再使用术语*弃用的*，术语*废弃的*有不同的含义。特别是，过时指的是将在一致性检查器中触发警告的元素或属性。虽然你不应该使用它们，但是浏览器可能支持它们，并且会长期支持它们，这与向后兼容的[原则相一致。旧的过时特性的例子有`img`元素上的`border`属性和`a`元素上的`name`属性。](https://www.w3.org/TR/html-design-principles/#support-existing-content)

为了让您了解这些术语在 HTML 4.01 和 HTML5 中的区别，`font`元素在 HTML 4.01 中被弃用，在 HTML5 中被废弃。

考虑到这一点，让我们来讨论其中五个过时的特性。

## 1.`hgroup`元素

该规范将`hgroup`元素定义为<q>，通常用于对一个或多个`h1`–`h6`元素进行分组，例如，分组一个章节标题和一个伴随的副标题。</q>

引入该元素是为了轻松创建字幕，并解决文档轮廓算法中的一个重要问题。事实上，当在一个`hgroup`中对多个标题元素进行分组时，outline 算法应该从结果文档大纲中屏蔽掉组中除最高级别标题之外的所有标题。

自从我知道这个元素以来，我一直是它的粉丝，但不幸的是，在 2013 年 4 月，它被从 W3C 的规范中删除了。在 SitePoint 上，Craig Buckler 在他的文章[RIP html 5<h group>Element](https://www.sitepoint.com/html5-hgroup-element-dropped/)中报道了这一事件。

其使用示例如下所示:

```
<article>
   <hgroup>
      <h1>5 deprecated features of HTML5</h1>
      <h2>Sometimes specifications are changed
      and you need to refactor your code</h2>
   </hgroup>
   <p>In this article we'll discuss...</p>
</article>
```

今天，如果你想在你的 HTML 页面中添加一个副标题，你可以使用这样的代码片段，正如规范中所推荐的:

```
<article>
   <h1>
       5 deprecated features of HTML5
       <span>Sometimes specifications are changed
       and you need to refactor your code</span>
   </h1>
   <p>In this article we'll discuss...</p>
</article>
```

在 HTML5.1 规范中可以找到其他可能的替代方案[。](https://www.w3.org/html/wg/drafts/html/master/common-idioms.html#sub-head)

现在，话虽如此，奇怪的是[的 WHATWG 版本的规范](https://html.spec.whatwg.org/multipage/semantics.html#the-hgroup-element)，仍然包括`hgroup`。我更喜欢 W3C 版本的规范，而且共识似乎是不再需要`hgroup`。

## 2.`pubdate`属性

[的规范初稿`time`元素](https://www.sitepoint.com/html5-time-element-guide/)定义了一个`pubdate`属性。它是一个布尔值，表明给定的日期是父`article`元素的发布日期，或者在没有父`article`元素的情况下，是整个文档的发布日期。

下面显示了一个使用示例:

```
<article>
  <h1>The title of my article</h1>
  <p>Lorem ipsum dolor sit amet, consectetur 
  adipiscing elit. Nunc laoreet luctus rhoncus.</p>
  <footer>
    <p>Published on <time datetime="2014-10-25" pubdate>October the 25th, 2014</time></p>
  </footer>
</article>
```

在这种情况下，2014 年 10 月 25 日将是`article`元素中内容的发布日期。

不幸的是，`pubdate`被从规范中删除。但是，您仍然可以通过使用[博客发布模式](http://schema.org/BlogPosting)及其`datePublished`值来提供相同的信息，如下例所示:

```
<article itemscope itemType="http://schema.org/BlogPosting">
  <h1 itemprop="headline">The title of my article</h1>
  <p itemprop="articleBody">Lorem ipsum dolor sit amet, consectetur 
  adipiscing elit. Nunc laoreet luctus rhoncus.</p>

  <footer>
    <p>Published on <time datetime="2014-10-25" itemprop="datePublished">October the 25th, 2014</time></p>
  </footer>
</article>
```

## 3.`scoped`属性

另一个布尔值属性`scoped`是用于`style`元素的，它允许你指出目标`style`属性中的 CSS 在应用中只限于同一`style`元素的父元素中的内容。

该属性的目标不是违反众所周知的关注点分离原则，即您希望保持结构、风格和行为的分离。它的目的是解决一些特定的用例，比如添加页面上有限元素集所需的样式，或者向 wiki 或 CMS 内容添加用户定义的样式。

`scoped`也是为了使内容“可移植”,所以你可以从一个文档中取出一整块 HTML，然后把它放到一个新文档中(可能通过 JavaScript 注入，或者辛迪加),样式就会和内容一起出现。

其使用示例如下所示:

```
<article>
  <h1>The title of my article</h1>
  <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. 
  Nunc laoreet luctus rhoncus.</p>
  <section>
    <style scoped> p {
         color: red;
      } </style>
    <h2>Another title</h2>
    <p>This paragraph will be red.</p>
  </section>
</article>
```

你可能已经注意到，在讨论这个属性时，我用了现在时而不是过去时。原因是虽然 Chrome 已经取消了对`scoped`的支持，但 Firefox 仍然支持[。](http://caniuse.com/#feat=style-scoped)

## 4.`command`元素

`command`元素是一个 void 元素，表示用户可以调用的命令。一旦定义了一个命令，界面的其他部分就可以引用同一个命令，允许多个访问点访问一个功能，共享禁用状态等方面。关于这个元素的信息不多，但是你不应该在意，因为它现在已经过时了。

下面显示了一个来自 MDN 页面的使用示例:

```
<command type="command" label="Save" 
         icon="icons/save.png" onclick="save()">
```

## 5.`center`元素

你可能想知道我为什么提到这个标签，因为这个标签在 HTML 4 中已经被否决了。`center`元素是一个块级元素，允许您在包含它的元素内水平居中放置它的所有内容。它被弃用，取而代之的是 CSS 的`text-align`属性，有助于将表示性的 HTML 排除在文档之外。

在 HTML5 中，这个元素由于其表示性质而被认为是过时的。

我相信你们都知道这个元素不应该被使用，但是我有一个很好的理由把它包括进来。

几个月前，[他在推特上发了下面这条消息:](https://twitter.com/rem/status/499629513983406080)

> <center>标签很牛逼。为什么这些年我一直在逃避。</center>
> 
> —雷米·夏普(@ rem)[2014 年 8 月 13 日](https://twitter.com/rem/status/499629513983406080)