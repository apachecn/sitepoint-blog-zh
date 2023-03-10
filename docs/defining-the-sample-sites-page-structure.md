# HTML5 页面结构基础

> 原文：<https://www.sitepoint.com/defining-the-sample-sites-page-structure/>

![htmlcss2thumb](img/d1b531ecd770df998ea32dbc2cf0f23c.png)

以下是我们的书的摘录，由 Alexis Goldstein、Louis Lazaris 和 Estelle Weyl 撰写的《真实世界的 CSS3，第二版。世界各地的商店都有出售，或者你可以在这里买到电子书。

既然我们已经分解了模板的基础，让我们开始给骨骼添加一些肉，给我们的页面一些结构。

在这本书的后面，我们将专门讨论添加 CSS3 特性和 HTML5 的其他优点；现在，我们将考虑在构建我们网站的整体布局时要使用哪些元素。我们将在这一节和接下来的章节中讨论很多关于语义的内容。当我们使用术语“语义”时，我们指的是给定 HTML 元素描述其内容含义的方式。

如果你回头看看 HTML5 先驱报(或在线查看网站)的截图，你会看到它被分成如下几部分:

*   带有徽标和标题的页眉部分

*   导航栏

*   正文内容分为三列

*   栏目内的文章和广告板块

*   包含一些作者和版权信息的页脚

在我们决定哪些元素适合页面的这些不同部分之前，让我们考虑一些选项。首先，我们将向您介绍一些新的 HTML5 语义元素，它们可以用来帮助划分我们的页面，并为我们的文档结构添加更多的含义。

## `header`元素

自然，我们要看的第一个元素是`header`元素。该规范将其简洁地描述为[“一组介绍性或导航性的辅助工具。”](https://html.spec.whatwg.org/multipage/semantics.html#the-header-element)

与您通常的假设相反，您可以包含一个新的 header 元素来介绍内容的每个部分。它不仅仅是为页面标题保留的(通常你可以用`<div id="header">`来标记)。当我们在这里使用单词“section”时，我们并不局限于下一部分中描述的实际的`section`元素；从技术上讲，我们指的是 HTML5 所说的“内容分段”这意味着任何内容块可能需要自己的标题，即使这意味着在一个页面上有多个这样的块。

一个`header`元素可以用来包含介绍性的内容或导航帮助，这些内容特定于页面的任何一个部分，或者应用于整个页面，或者两者兼有。

虽然一个`header`元素经常被放在页面或部分的顶部，但是它的定义是独立于它的位置的。你的站点布局可能要求文章或博客文章的标题放在内容的左边、右边，甚至下面；不管哪种，你还是可以用`header`来描述这个内容。

## `section`元素

您应该熟悉的下一个元素是 HTML5 的`section`元素。规格[对`section`的定义如下:](https://html.spec.whatwg.org/multipage/semantics.html#the-section-element)

> `section`元素表示文档或应用程序的通用部分。在这种情况下，节是内容的主题分组，通常带有标题。

它进一步解释了一个`section`不应该被用作一个通用的容器，它的存在仅仅是为了样式或者脚本的目的。如果您不能使用`section`作为通用容器——例如，为了实现您想要的 CSS 布局——那么您应该使用什么呢？我们的老朋友，`div`元素，在语义上毫无意义。

回到规范中的定义，`section`元素的内容应该是“主题化的”，所以以通用的方式使用它来包装不相关的内容是不正确的。

`section`元素的一些可接受用法包括:

*   选项卡式界面的各个部分

*   “关于”页面的片段；例如，公司的“关于”页面可能包括公司历史、使命陈述和团队等部分

*   冗长的“服务条款”页面的不同部分

*   在线新闻网站的各个部分；例如，文章可以被分组为涵盖体育、世界事务和经济新闻的部分

### 注意:正确使用`section`

每当新的语义标记提供给 web 设计者时，都会有关于什么构成了这些元素的正确使用、规范的意图是什么等等的争论。您可能还记得在以前的 HTML 规范中关于适当使用`dl`元素的讨论。不出所料，HTML5 也不能幸免于这种现象，尤其是当它涉及到`section`元素时。甚至 HTML5 备受尊敬的权威布鲁斯·劳森也承认过去错误地使用了`section`。为了澄清一点，很有必要阅读一下 [Bruce 解释他的错误的帖子。](http://html5doctor.com/the-section-element/)

简而言之:

*   `section`是 *的总称* ，所以如果更具体的语义元素合适(比如`article`、`aside`或者`nav`，就用那个代替。

*   `section` *有语义*；这意味着它包含的内容在某种程度上是相关的。如果你不能用几个词简洁地描述你试图放入一个`section`中的所有内容，很可能你需要一个语义中立的容器:谦逊的`div`。

也就是说，语义总是这样，在某些情况下可以有不同的解释。如果你觉得你可以解释为什么你使用一个给定的元素而不是另一个，那就去做吧。万一有人就此打电话给你，由此产生的讨论对每个参与者来说都是有趣和丰富的，甚至可能有助于更广泛的社区对规范的理解。

还要记住，如果合适的话，可以在现有的`section`元素中嵌套`section`元素。例如，对于在线新闻网站，世界新闻部分可以进一步细分为每个主要全球区域的部分。

## `article`元素

`article`元素类似于`section`元素，但是有一些显著的区别。下面是根据规范的[定义:](https://html.spec.whatwg.org/multipage/semantics.html#the-article-element)

> article 元素表示文档、页面、应用程序或站点中的完整或自包含的组合，并且原则上是可独立分发或可重用的，例如在联合中。

该定义中的关键术语是*和*。一个`section`可以包含任何可以按主题分组的内容，而一个`article`必须是一个独立的内容。这种区别可能很难让你理解，所以当你有疑问时，试试辛迪加的测试:如果一段内容可以不加修改地重新发布在另一个网站上，或者如果它可以通过 RSS 更新，或者在 Twitter 或脸书等社交媒体网站上发布，它就具备了成为`article`的条件。**

 **最终，由你来决定一篇文章的内容，但是这里有一些与规范中的建议一致的建议:

*   论坛帖子

*   杂志或报纸文章

*   博客条目

*   用户提交的对博客条目或文章的评论

最后，就像`section`元素一样，`article`元素可以嵌套在其他`article`元素中。你也可以在一个`article`中嵌套一个`section`，反之亦然。这完全取决于你标记的内容。

## `nav`元素

可以有把握地假设,`nav`元素会出现在几乎每个项目中。`nav`顾名思义:一组导航链接。虽然`nav`最常见的用途是包装无序的链接列表，但是还有其他选择。例如，您可以将`nav`元素放在包含页面或页面部分的主要导航链接的一段文本周围。

在任何一种情况下，`nav`元素都应该保留给最重要的导航。因此，建议您避免使用`nav`来显示页脚中的简短链接列表。

### 注意:跳过导航链接

你可能在很多网站上看到过的一种设计模式是“跳过导航”链接。这个想法是让屏幕阅读器的用户快速跳过你的站点的主导航，如果他们已经听过的话——毕竟，当你每次点击进入一个新的页面时，没有必要去听一个大站点的整个导航菜单！`nav`元素有可能消除这种需要；如果屏幕阅读器看到一个`nav`元素，它可以允许用户跳过导航而不需要额外的链接。该规范指出:“用户代理(如屏幕阅读器)针对的用户可以从初始呈现中忽略的导航信息中受益，或者可以从导航信息立即可用中受益，可以使用该元素作为一种方式来确定页面上最初跳过或应请求提供(或两者都提供)的内容。”

虽然不是所有的辅助设备都能识别`nav`，但是通过建立现在的标准，你可以确保随着屏幕阅读器的改进，你的页面随着时间的推移会变得更容易访问。

### 注意:用户代理

浏览规范时，您会经常遇到“用户代理”这个术语。实际上，这只是浏览器的一个花哨术语——用户用来访问页面内容的软件“代理”。规范没有简单地说“浏览器”的原因是用户代理可以包括屏幕阅读器或任何其他阅读网页的技术手段。

您可以在给定页面上多次使用`nav`。如果你有一个站点的主导航栏，这将需要一个`nav`元素。此外，如果你有指向当前页面不同部分的第二组链接(使用页面内锚或“本地”链接)，这也可以包装在一个`nav`元素中。

和`section`一样，关于什么构成了`nav`的可接受用法，以及为什么在某些情况下(比如在页脚)不推荐使用它，也有一些争论。一些开发人员认为这种元素适合于分页或面包屑链接，或者适合于构成网站导航主要手段的搜索表单(如 Google)。

这个决定最终取决于你，开发者。WHATWG 的 HTML5 规范的主要编辑伊恩·希克森直接回答了这个问题:“只要你会使用 class=nav，就使用[它]”。[<sup class="footnote">【7】</sup>](#ftn.d5e668)

## `aside`元素

这个元素表示页面的一部分，它与`aside`元素周围的内容有一点点关系，可以被认为是独立于该内容的。

`aside`元素可以用来包装与以下内容无关的内容部分:

*   特定的独立内容片段(如`article`或`section`)。

*   整个页面或文档，通常是在页面或网站上添加侧边栏。

`aside`元素永远不应该用于包装页面中属于主要内容的部分；换句话说，`aside`并不意味着带有括号。`aside`的内容可以独立存在，但它仍然应该是一个更大整体的一部分。

`aside`的一些可能用途包括侧边栏、二级链接列表或广告块。还应该注意的是，`aside`元素(和`header`的情况一样)不是由它在页面上的位置定义的。可能在边上，也可能在别的地方。定义它的是内容本身，以及它与其他元素的关系。

## `footer`元素

我们将在本章讨论的最后一个元素是`footer`元素。与`header`一样，在一个页面上可以有多个`footer`元素，并且应该使用`footer`而不是像`<div id="footer">`这样的通用元素。

根据规范，footer 元素表示作为其最近祖先的内容部分的页脚。内容部分可以是整个文档，也可以是一个`section`、`article`或`aside`元素。

通常一个`footer`会包含版权信息、相关链接列表、作者信息以及你通常认为会出现在内容块末尾的类似信息；然而，与`aside`和`header`非常相似，页脚元素不是根据它在页面上的位置定义的，所以它不必出现在一个部分的末尾，或者页面的底部。很可能会，但这不是必需的。例如，关于博客文章作者的信息可能显示在文章的上方而不是下方，但仍被视为页脚信息。

### 注:我们是怎么到这里的？

如果你对 HTML5 的发展道路以及我们是如何完成我们所做的标签有点好奇，你可能想看看卢克·斯蒂文斯的书《HTML5 的真相》。《路加福音》目前是第二版，这本书有些争议。除了涵盖视频和画布等许多 HTML5 技术之外，他还深入介绍了 HTML5 的历史，解释了新元素中固有的一些语义和可访问性问题，并就如何处理这些问题提供了一些建议。

* * *

[<sup class="para">【7】</sup>](#d5e668)见【http://html5doctor.com/nav-element/#comment-213.】

## 分享这篇文章**