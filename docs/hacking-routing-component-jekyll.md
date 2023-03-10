# 破解 Jekyll 中的路由组件

> 原文：<https://www.sitepoint.com/hacking-routing-component-jekyll/>

最初，我打算将这篇文章命名为*在 GitHub 页面上使用 Markdown、Liquid 和 YAML* 托管时，在 Jekyll 中破解路由组件。显然，那有点长，所以我不得不缩短它。无论如何，重点没有改变:[杰基尔](http://jekyllrb.com/)缺少某种路由器，我想我找到了一个*不那么可怕的*解决方案来模仿一个。

但首先:我来自一个有着 [Symfony](http://symfony.com/) 背景的地方，所以我所说的*路由器*是指一个将 URL 映射到名称的组件——也就是*路由*——所以你可以安全地更改 URL，而不必抓取你的代码库来更新所有指向过时位置的链接。

## 我们为什么需要这样的东西？

我目前正在努力编写 [SassDoc](http://sassdoc.com) v2 的文档(在撰写本文时尚未发布)。我们有相当多的内容；超过 20 页，分为 4 个不同的部分，包含许多代码示例和交叉引用。

在重写过程中，有几次我想要更改一个 URL。问题是，当我改变一个网址时，我必须更新所有指向这个网址的链接，如果我不想它们被破坏的话。爬了 20 多页，以确保所有的网址都没问题，这是远远不够理想的…

这就是为什么我们需要一个路由组件。然后，我们将通过名称而不是路径来引用 URL，允许我们在保持名称完全有效的同时更改路径。

## 它是如何工作的？

那么，我们需要什么来实现这一目标呢？如果你正在运行 Jekyll，但没有被限制在安全模式下(不幸的是，当使用 [GitHub Pages](https://pages.github.com/) 进行托管时就是这种情况)，你肯定可以找到/构建一个 Ruby 插件来做到这一点。这将是最好的解决方案，因为这通常是由服务器端语言处理的。

现在，如果你在 GitHub 页面上托管你的站点(这是 Jekyll 经常出现的情况)，你不能使用插件，也不能用定制的 Ruby 代码扩展 Jekyll 的核心，所以你最终只能用可用的东西来破解解决方案: [Liquid](http://docs.shopify.com/themes/liquid-documentation/basics) 和 [Markdown](http://daringfireball.net/projects/markdown/) 。

主要的想法是有一个文件包含我们所有的路由映射到实际的网址。谢天谢地，Jekyll 允许我们通过存储在`_data`文件夹中的 YAML/JSON/CSV 文件来定义[自定义全局变量，稍后通过`site.data.<filename>`访问。因此，我们可以从它们的名称直接在我们的页面中访问这些 URL。](http://jekyllrb.com/docs/datafiles/)

为了在上面添加一点语法糖，我们将创建 Markdown 链接引用，这将允许更友好的语法-但我们不要走得太快太快。

## 创建路由器

路由器的作用是暴露映射到 URL 的路由(又名*名*)(一对一)。可以在任何 Jekyll 项目的`_data`文件夹中创建 YAML/JSON/CSV 文件，所以让我们使用名为`routes.yml`的 YAML 文件:

```
home: "/"
about: "/about-us/"
faq: "/frequently-asked-questions/"
repository: "https://github.com/user/repository"
```

你可能已经注意到，我们当然不局限于内部链接。如果外部 URL 经常出现，我们完全可以为它们定义路径，以避免反复输入。同样，在我们的例子中，我们停在了 4 条路由上，但是路由器可以包含数百条路由。

因为文件在`_data`中，所以我们可以用`site.data.<filename>.<key>`在任何地方访问它的内容。假设我们有一个包含以下代码的页面:

```
---
layout: default
title: "About us"
---

<!-- Content about us -->

Go to our [GitHub repository]({{ site.data.routes.repository }}).
Or read the section dedicated to [Frequently Asked Questions]({{ site.data.routes.faq }}).
```

如您所见，我们不再使用 URL，而是使用 routes。这不是魔术，它只是告诉 Jekyll 访问存储在给定路径的全局变量(例如`site.data.routes.faq`)。

现在，如果存储库不再托管在 GitHub 上，或者“关于我们”页面的 URL 现在是`/about/`，不用担心！通过更新路由器，我们使它工作，而不必回到我们的页面来更新我们的链接。

## 添加语法糖

在这一点上，我们有一个功能路由器，允许我们改变任何网址，而不必抓取我们的网站来修复损坏的链接。所以你可以说它已经很酷了。然而，必须键入`site.data.routes.faq`不是很方便。我们当然可以让它更优雅一点！

是也不是。起初，我想到构建一个小的`route()`函数，接受一个键名并返回存储在`site.data.routes.<key>`的值。问题是我们在安全模式下运行 Jekyll，所以我们不能用 Ruby 代码扩展它。运气不好。

然后我想到了一个以前没用过的 Markdown 功能:[链接引用](http://daringfireball.net/projects/markdown/syntax#link)。这是链接在 Markdown 中的表示方式:

```
[I am a link](http://link.url/)
```

您还可以将链接设置为 head to a reference——顺便说一下，它是完全不可见的，在页面中的任何位置定义，如下所示:

```
[I am a link][id_reference]

​[id_reference]: http://link.url
```

*注意:当使用引用而不是 URL 时，圆括号被替换为方括号。*

这可以让你在同一个地方定义所有的链接(比如在底部)，而不是整个文档。我必须说我没怎么用过这个功能，但在这种情况下，它派上了用场。

这个想法是从我们的路由器自动生成链接引用，所以我们可以在任何文件中使用我们的路由作为引用。事实证明，在液体中做到这一点非常容易:

```
# _includes/route.html
{% for route in site.data.routes %}
[{{ route[0] }}]: {{ route[1] }}
{% endfor %}
```

通过在页面的任何地方添加这个 for 循环，它指示 Jekyll 将其作为液体代码处理，然后作为 Markdown 引用处理。例如，回到前面的例子，我们可以这样做:

```
---
layout: default
title: "About us"
---

<!-- Content about us -->

Go to our [GitHub repository][repository].
Or read the section dedicated to [Frequently Asked Questions][faq].

{% for route in site.data.routes %}
[{{ route[0] }}]: {{ route[1] }}
{% endfor %}
```

现在我们在谈话，对吗？唯一的问题是必须在任何页面中包含这个循环。起初，我想把它添加到布局中，这样它就会自动添加到任何使用相关布局的页面中。问题是，在 Jekyll 中，布局不被处理为 Markdown，所以引用实际上出现在 DOM 的底部。更糟糕的是，它们在我们的页面中不可用，因为它们没有被作为降价处理…太糟糕了。

然而，我们仍然可以做一些事情来使它稍微好一点。我们可以把这个循环放在一个液体片段中，并在每一页中包含这个片段，而不是复制粘贴这个循环。假设我们在`_includes`文件夹中创建一个`routes.html`片段:

```
{% for route in site.data.routes %}
[{{ route[0] }}]: {{ route[1] }}
{% endfor %}
```

然后，在我们的页面上:

```
---
layout: default
title: "About us"
---
{% include routes.html %}

<!-- Content about us -->

Go to our [GitHub repository][repository].
Or read the section dedicated to [Frequently Asked Questions][faq].
```

*注意:您可以在页面的任何地方包含部分内容，而不仅仅是在顶部。你完全可以把它放在文件的最后一行。*

## 最后的想法

伙计们，我们在 Jekyll 安全模式下破解了一个路由组件。这有什么缺点呢？有几个小问题:

*   它在 Markdown 中工作；如果你碰巧有一个普通 HTML 格式的链接，你不得不退回到`{{ site.data.routes.<key> }}`，这并不太糟糕，因为它仍然可以保护链接免受 URL 更新的影响。
*   您不能向链接添加锚点；如果需要的话，回落到`{{ site.data.routes.<key> }}#anchor`(还是那句话，不算太差)。
*   您必须在所有页面中包含 HTML 部分，这可能很乏味(即使您认为这是 YAML 前台事务的扩展，您也必须编写)。

除了那些缺点，它都很好，闪闪发光。你怎么想呢?

## 分享这篇文章