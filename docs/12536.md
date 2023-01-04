# 如何使用条件注释获得更好的 CSS

> 原文：<https://www.sitepoint.com/how-to-use-conditional-comments-for-better-css/>

我是 SitePoint 博客的新撰稿人，主要负责前端开发和语义网技术。我期待着加入 SitePoint 社区！

有时候，最简单的事情也会被忽视很长时间。举个例子，今晚在赶读一些 [WSG](//webstandardsgroup.org/) 的文章时，我看到了一个链接，链接到[保罗·哈蒙德为 Internet Explorer](http://www.paulhammond.org/2008/10/conditional/) 编写的<cite>条件类名</cite>。在一句话中，他展示了如何使用条件注释定制`<body>`元素的`class`名称来简化 CSS 选择器，以达到许多有利的目的。

保罗展示的代码如下所示:

> [T]他的 HTML:
> 
> ```
>  <!--[if IE ]>
>   <body class="ie">
> <![endif]-->
> <!--[if !IE]>-->
>   <body>
> <!--<![endif]--> 
> ```
> 
> CSS 看起来像这样:
> 
> ```
>  div.foo {
>     color: inherit;
> }
> .ie div.foo {
>     color: #ff8000;
> } 
> ```

如此简单却又如此强大。使用这种技术的一些优势包括:虽然没有广泛使用，但从 IE5 开始，IE 的条件注释“特性”就已经在技术上可用了。

*   避免额外的 IE 专用样式表，通过减少 HTTP 开销来提高网站性能(正如 Paul 在他的帖子中提到的)。
*   Eliminating (traditional) IE-specific CSS hacks from style sheets entirely. This could be accomplished by using [more specific conditional comments](http://msdn.microsoft.com/en-us/library/ms537512.aspx) with a wider set of class names. For example, the following code could be used to specifically identify IE7 in the HTML:

    ```
    <!--[if IE ]>
      <body class="ie">
    <![endif]-->
    <!--[if IE 7]>
      <body class="ie ie7">
    <![endif]-->
    <!--[if !IE]>-->
      <body>
    <!--<![endif]--> 
    ```

    在这种情况下，以*为前缀的 CSS 选择器* `.ie`或`.ie7`都可以指向它。

*   与 HTML 文档交互的其他技术(CSS 之外的)将会意识到它们特定于 IE 的环境。JavaScript 跃入脑海。是的，是的，我知道浏览器嗅探比基于能力的对象检测要糟糕得多，但事实仍然是，有时 IE 不会给你“全部的真相，除了真相什么都没有…”

这种技术肯定有许多“哲学的”或“风格的”观点，例如将黑客隔离到他们自己的文件中对于可维护性来说是一件好事。我个人对这类事情的看法是，黑客攻击应该尽可能地显而易见，这样你就可以在第一时间修复它，而不是忘记它。这就是为什么我长期以来更喜欢将`* html` hack 直接放在低于 7 的 IE 版本的 CSS 规则之后，但这通常是一种风格上的选择。

我几乎不好意思承认我以前没有遇到过这种技术。当然，这个事实告诉我，没有这项技术，成功实现高保真网页设计是完全可能的。尽管如此，IE 的条件类名的概念确实提出了一些关于如何简化纯 IE 代码的有趣问题，我认为任何提高代码理解的技术都至少值得再看一眼。

## 分享这篇文章