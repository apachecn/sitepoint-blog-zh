# rel (HTML 属性)

> 原文：<https://www.sitepoint.com/rel-html-attribute/>

## 描述

`rel`属性定义了链接的资源与引用它的文档之间的关系。在大多数情况下，这个资源只是“`stylesheet`”，这意味着“引用的文档是一个样式表”，这并不奇怪与该值相关的是“alternate”，它与“stylesheet”值(`rel="alternate stylesheet"`)一起使用，表示有另一种样式与该页面相关联。事实上，您可以定义几种可选的样式，尽管这种方法的主要问题在于让用户清楚地知道可选的样式表是可用的。在 Firefox 中，您可以选择 View >页面样式，并从提供的可用样式表中挑选，但在任何浏览器中都没有明显的迹象表明这些替代方案存在——通常由开发人员提供某种基于 JavaScript 的样式切换器，作为页面上的控件呈现。下面的标记显示了一个包含一个主样式表和两个替代样式表的页面:

```
<link rel="stylesheet" href="main.css"
    type="text/css" media="screen"/>
<link rel="*alternate* stylesheet" *title="Higher Contrast"*
    href="contrast.css" type="text/css" media="screen"/>
<link rel="*alternate* stylesheet" *title="Gratuitous CSS"*
    href="hot.css" type="text/css" media="screen"/>

```

请注意，当您使用替代样式表时，您还应该提供一个简要描述样式的 title 属性，它将出现在浏览器的页面样式菜单选项中，如下所示。

![The title of the style sheet, as shown in Firefox](img/83349b0d2b8293b6db7d86dfa1906860.png)

样式表的标题，如 Firefox 所示

Opera 还为我们提供了从视图菜单选项中改变页面样式的能力，但是并没有引起对替代样式的注意——你必须去寻找它！

“`alternate`”属性值也可以用在 XML 提要的上下文中，即 RSS 或 Atom，它们用 type 属性表示:

```
<link rel="alternate" *type="application/rss+xml"*
    href="/rss.xml" title="RSS 2.0">
<link rel="alternate" *type="application/atom+xml"*
    href="/atom.xml" title="Atom 1.0">
```

如果要将自定义图标与您的网站相关联(在大多数浏览器中，该图标会出现在地址栏中 URL 的旁边，但也可能在将网页保存为桌面上的收藏夹或快捷方式时使用，这取决于浏览器或操作系统)，可以按如下方式使用 rel 属性:

```
<link rel="*shortcut icon*" href="/favicon.ico"/>
```

该链接引用了位于 web 服务器的文档根文件夹中的一个图标，惯例是将其命名为`favicon.ico`(必须是. ico 文件，而不是. gif。jpg 或者。png)。您可以将收藏夹图标放在 web 服务器上的其他地方，但是您必须相应地修改 href 属性中指定的位置。

当属性用于定义页面如何相互关联时，`rel`的关系方面在上下文和方向方面变得更加丰富。例如，在具有逻辑、线性流程的页面序列中，您可以使用 rel 属性来定义序列中的“`next`”和“`prev`”页面。

使用 rel = " license " href = " link-to-license . html "，还可以使用`rel`属性来指示应用于页面内容的许可证。关于这一点的更多信息，请参考微格式对`rel`的使用。

您可以使用 link 元素定义页面之间的许多自定义关系，方法是超越预定义的值来指定您自己的值。

## 例子

在这个例子中，`rel`属性表示引用的文档是一个样式表:

```
<link *rel="stylesheet"* href="basic.css"/>
```

## 价值

有关可接受的预定义值，请参考语法图。但是，请记住，您并不仅限于这些:您可以定义自己的 rel 属性值，但是它对任何 web 浏览器都没有多大用处(尽管您可以将该信息用于其他目的，例如，使用 JavaScript 和 DOM 查询或访问属性值)。

## 分享这篇文章