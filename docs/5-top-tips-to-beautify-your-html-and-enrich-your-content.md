# 美化 HTML 和丰富内容的 5 大技巧

> 原文：<https://www.sitepoint.com/5-top-tips-to-beautify-your-html-and-enrich-your-content/>

乍一看可能令人惊讶，但是好代码和伟大代码的一些最大的区别实际上是所有的小事情。缺少这些“小东西”会使原本漂亮的标记看起来像是从 Microsoft Word 导出的。这里有 5 件比你想象的更重要的事情，帮助你的标记从体面到华丽。

## 尽可能使用最短的 URI

每当你做一个链接，添加一张图片，或者写任何引用网络上不同文件的东西，你应该使用相对 URI，绝对，完全限定 URIs，还是…？URIs 的变体如此普遍，以至于人们理所当然地问“[我们应该用哪一个](/blogs/2009/01/21/which-url-is-right/ "SitePoint's own Kevin Yank answers this common reader question.")？”

一个简单的经验法则是尽可能使用最短的*值*。较短的 URIs 可能更便于携带，也有优化的好处。当`/my-page/`可以的时候，几乎没有理由用像`http://example.com/my-page/`这样的 URI 来编码链接。如果你的域名在将来改变了，并且你使用了完全合格的 URI，你将会以一个断开的链接结束。如果你链接到一个远程域，那么除非你自己的内容将通过多种协议发布(比如除了`http:`之外的`https:`，你甚至可以安全地[省略 URI](//no-http.org/ "The No-HTTP web site explains why the scheme of a URI is optional.") 的 scheme 部分。

从这个意义上说，URI 可以被认为是它自己的一种迷你语言。尽可能使用最短的代码会给你带来更高的可重用代码的可能性，这种现象可以用最小功率规则来解释。

## 一致地使用标记模式

模式是一种非常强大的技术，可以提高代码的重用性、易读性和功能性。从本质上来说，[微格式](//microformats.org "Visit the Microformats Wiki.")是简单的已经很好建立并被广泛理解的标记模式。他们的功能实际上来源于将这些模式的*批次发送到处理工具的能力。*

即使您不在自己的标记中使用微格式，也可以通过一致地使用自己的模式而受益。举个戏剧性的例子，想象一下不得不处理每个页面上不同标记的网站徽标的麻烦。在主页上你可能有`<h1 id="logo">…</h1>`，在文章页面上你可能有`<img id="sitelogo" … />`。你可能最终会将 CSS 规则分组以涵盖这两种情况，并在脚本中添加额外的代码以努力使[保持低调](/article/unobtrusive-javascript/ "Read about unobtrusive JavaScript and why you should make your scripts unobtrusive, too.")。太可怕了！

所以一个模式可以从使用相同的标记作为一个网站的标志开始，但是你遵循的惯例越多，它对你就越有价值。如果你要用`<div id="Header">`，那么你*就不应该*用`<div id="ftr">`。例如，在 SitePoint，代码块总是以相同的方式标记(使用`<pre><code>…</code></pre>`)，使得在整个站点范围内以统一的方式在这些元素之上分层样式和行为成为可能。

## 尽量少用`class`和`id`属性

模式的一个容易被忽视的好处是——当它们被很好地设计时——它们从根本上改善了你的标记的结构。这反过来又减少了在元素中使用 id 或类来设计样式的需要，从而使您的标记更加简洁。它还让你的样式表更容易理解，因为你的 CSS 选择器将使用标记的结构上下文在 CSS 文件中提供相同的上下文。

事实上，[避免 id 和课程是一个非常有益的练习](http://meiert.com/en/blog/20081021/exercise-for-emerging-css-experts/ "Jens Meiert suggests using structural context makes for good CSS selectors.")。如果你的标记结构良好，那么你可以使用简单的[后代组合子](https://reference.sitepoint.com/css/descendantselector "Look up the descendent combinator in SitePoint's CSS reference.")来实现你所需要的惊人数量的样式。随着对使用结构化上下文的 CSS 选择器支持的提高(如`:last-child`和`:only-child`)，对任意样式挂钩的需求进一步减少。

这与前一个技巧有着内在的矛盾:如果您过于依赖标记的结构上下文，那么您可能会发现自己添加了不必要的元素来容纳样式表。在这两个极端之间游走正是创建结构良好的语义标记的挑战。

## 添加`title`属性和其他元数据来丰富内容

尽管有其局限性，HTML 提供了许多机制，您可以使用这些机制在标记中添加元数据或附加内容。很少有网页充分利用了它们，但是在那些利用了它们的网页上，用户可以享受到丰富的、分层的交互，除了核心的 HTML 元素和属性之外，没有什么更好的了。一个典型的例子是`title`属性。

链接(即锚点或`a`元素)不是唯一可以被赋予`title`属性的东西。它可以应用于像`blockquote`、`ul`、`pre`和无数其他的元素。正如链接一样，`title`属性通常在用户将光标悬停在元素上时显示为工具提示，在所有情况下，它们都是为了给元素提供某种补充信息。

您可以以多种方式利用这些元数据，当然包括语义，但也可以用于交互。例如，如果你引用一篇演讲，考虑使用如下标记:

```
<blockquote title="Text of Barack Obama's Inaugural Address" cite="http://news.yahoo.com/s/ap/20090120/ap_on_go_pr_wh/inauguration_obama_text">
    <p>My fellow citizens:</p>
    <p>I stand here today humbled by the task before us, grateful for the trust you have bestowed, mindful of the sacrifices borne by our ancestors. I thank President Bush for his service to our nation, as well as the generosity and cooperation he has shown throughout this transition.</p>
    …
</blockquote>
```

当用户的光标悬停在你页面上的这段引文上时，他们会看到一个工具提示，上面写着“巴拉克·奥巴马就职演说的文本”，这给他们的体验增加了一些深度。这就好像你在邀请他们积极探索和发现你的内容。此外，使用如下的 CSS 规则，您可以提取`title`属性中的元数据，并可视化地显示它。

```
blockquote[title]::before { content: attr(title); display: block; text-align: center; } 
```

您可以使用的另一个属性是`rel`属性。例如，使用`rel="external"`是一个简单的模式，可以用来标记到远程站点的链接。如果你写了一篇被分割成多个独立块的文章，比如一系列文章，你也可以在页面的`head`中的`link`元素内使用`rel`属性，为正确的顺序导航提供[提示:](https://www.w3.org/TR/html401/struct/links.html#h-12.3 "The HTML specification uses the example of sequential navigation, as well.")

```
<head>
    <title>My Fantastic Article - Page 2</title>
    <link rel="prev" href="/page-1/">
    <link rel="next" href="/page-3/">
    <link rel="index" href="/article-index/">
</head>
```

使用这些额外的元素将触发界面，如 [Opera 的导航栏](http://www.opera.com/browser/tutorials/intro/ui/ "A run down of all the toolbars available in Opera.")。

## 使用注释和空白有助于可读性和易于维护

不管项目的规模如何，难读的代码是最令人沮丧的事情之一。可以说，你的同伴能读懂你的代码比你的机器更重要。因此，不仅要注意你写了什么，还要注意你如何写代码，这真的很有帮助。

HTML 中的注释不常使用，但它们会产生很大的影响。一个简单的例子是用它们来表示元素的*结束标签*。例如，许多典型的博客模板有许多嵌套的`div`元素，就像这个过于简化的例子:

```
<div id="Wrapper">
    <div id="MainContent">
        <div id="post-1" class="hentry">
            <p>Main article content here.</p>
        </div>
    </div>
</div><!-- Which element does this tag close, again? --> 
```

当然，实际上结束标记很少与开始标记接近，所以很难立即知道哪个结束标记结束了哪个元素。为了找到答案，你可能要花 30 秒钟在文件中向上滚动。这听起来可能不多，但是如果你一天做 100 次，那么你将花费*将近一个小时* (50 分钟)除了在文本文件中滚动什么也不做。

如果您的模板被分割到多个文件中，其中一个标签在一个文件中打开，而在另一个文件中关闭，这个问题会变得更糟。现在你要花更多的时间来搜索开始标签。这种情况是使用 HTML 注释的最佳场合:

```
<div id="Wrapper">
    <div id="MainContent">
        <div id="post-1" class="hentry">
            <p>Main article content here.</p>
        </div><!-- .hentry -->
    </div><!-- #MainContent -->
</div><!-- #Wrapper --> 
```

一致的空白也很重要。这不是制表符和空格的问题，这是挑选(或发明)一些适合你和你的团队的惯例，然后在项目的整个生命周期中虔诚地坚持它的另一个例子。最后，请记住，如果您使用智能/自动化构建系统，您在源模板中使用的任何空白都可以在生产版本中被压缩。

最终，好代码和伟大代码的区别在于，所有的“小事”都执行得非常好，以至于你甚至不会注意到它们。如果在编写标记时，这些技巧中的任何一个还没有成为你的习惯，那么它们应该成为了！

## 分享这篇文章