# JavaScript 库和模式:雅虎！AJAX 是否

> 原文：<https://www.sitepoint.com/javascript-libraries-and-patterns-yahoo-does-ajax/>

*这是从 SitePoint Tech Times #132* 重新发布的

JavaScript 很难，但可能会更难。如今，选择工具实际上是大部分工作。

很多年前(好吧，五年前)，网站上的 JavaScript 代码是精通代码的开发人员有额外时间的标志。像鼠标悬停图像交换(和不那么可爱的破碎的右键菜单)这样的可爱效果通常是结果。即使在这样技术冒险的网站上，编写超过 100 行的 JavaScript 代码几乎是闻所未闻的。

今天，web 应用程序成功的典型代表是由大量强大且相互依赖的 JavaScript 文件组成的，并带有少量的 HTML 和 CSS。随着 JavaScript 代码库轻而易举地超过标记和样式表，敏感的新时代 web 开发人员的大部分时间应该花在编写脚本上吗？

好吧，除非你想解决一个以前没有人解决过的问题，否则答案是“可能没有”

web 开发领域的大量精力正被投入到构建可重用的解决方案中。在 SitePoint，我们刚刚给<cite>发送了 JavaScript 选集</cite>，将近 600 页这样的解决方案，以供印刷。今天雅虎！加入到越来越多的提供可重用 JavaScript 功能库的实体中来[雅虎！用户界面库](https://developer.yahoo.net/yui/)。

与此同时，使用这些快速发展的工具所创造的体验每天都在变得更加丰富。当代码猴子们不断挑战可能性的界限时，可用性专家们正在编辑关于如何最好地创建人们能够(并且愿意)使用的应用程序的文档。雅虎！设计模式库是进入这个领域的一个新的有前途的入口。

在这篇文章中，我将浏览雅虎！的新产品，并回顾一些您应该考虑的其他选项。如果你还没有选择你的马，现在将是一个好时机。JavaScript 很难，但是在你的网站上使用它并不困难。

## 如何做:JavaScript 库

我是一名手工程序员。我非常满意用自己的血、汗和泪来制作一个复杂系统的每一个部分。然而，这些天来，当谈到 JavaScript 时，我似乎总是在重新发明轮子。

谢天谢地，有些人喜欢重新发明轮子。或者更确切地说，他们喜欢拥有镇上最好的车轮，这样每个人都会想看看他们的性感合金，并为自己买一套。

有适合各种口味的 JavaScript 库。这里有一个最重要的概述，包括雅虎！的全新产品:

## 柔道训练学校

JavaScript 库的劳斯莱斯， [Dojo](https://dojotoolkit.org/) 为各种场合提供工具。事实上，这个库非常大，除了最复杂的 web 应用程序之外，您可能不需要在任何应用程序中使用它的每个方面。因此，您可以选择下载包含您可能需要的特性的库版本，这样您的访问者就不必下载超过 300KB 的组成 Dojo 的 JavaScript 代码。

Dojo 占据了现代浏览器领域不稳定的位置，并用一组可靠的接口取而代之。当 Dojo 处理浏览器兼容性问题时，您可以为动画、事件处理、远程脚本(AJAX)等等编写简单的代码。

最强大的是，Dojo 允许您构建由小部件组成的基于浏览器的用户界面，这些小部件提供桌面应用程序的所有交互性。您可以在 Dojo 提供的框架内构建自己的小部件，或者使用内置小部件库。Dojo 甚至提供了对拖放的复杂支持，完全没有确保浏览器兼容性的麻烦。

Dojo 的缺点是它的复杂性，以及它的大部分目前没有文档记录的事实。毫无疑问，这将随着时间的推移而改进，但是如果您现在打算进入 Dojo，就要通过示例来学习。

## 原型

与其像 Dojo 那样提供一个全新的框架，不如说,[prototype](https://prototype.conio.net/)增强了浏览器现有的 JavaScript 特性，这样使用起来就不那么痛苦了。使用 Prototype，您可以利用现有的技能，同时利用该库提供的巧妙快捷方式。

除了像`$`函数这样宝贵的快捷方式，它是`document.getElementById`的快捷方式(例如`$('content')`)，Prototype 还提供了事件处理、数组处理、表单脚本、DOM 操作和远程脚本(AJAX)的函数。

因为 Prototype 非常擅长于减轻低级脚本编写的痛苦，所以许多高级库都是以 Prototype 为基础构建的。最值得注意的是:

*   script.aculo.us 是视觉糖果之王，提供易于应用的动画“电影效果”,可用于增强大多数 web 应用程序。
*   Rico 的目标与 Dojo 相同，提供从一个小部件库构建类似桌面的 web 应用程序所需的一切。不幸的是，它的文档甚至比 Dojo 还要少。

## 阿杰克斯

Zimbra Ajax Toolkit(Ajax tk)是作为与 T2 Zimbra(一种协作服务器，允许整个公司管理电子邮件、日历和联系人，并通过浏览器或微软 Outlook 等桌面客户端访问它)的 web 界面的基础而开发的。您可以单独下载 AjaxTK 用于您自己的应用程序。

AjaxTK 现在处于一个尴尬的位置，它有一个非常令人印象深刻的例子(Zimbra web 客户端)，但除此之外没有看到太多的社区采用。随着最近宣布 AjaxTK 将作为开源 AJAX 工具包 Apache Kabuki 的基础，它的未来看起来更加光明。

目前，AjaxTK 主要致力于构建界面像桌面应用程序一样丰富的 web 应用程序。提供了一整套小部件，以及用于构建更多部件的框架。

与大多数竞争对手不同，AjaxTK 被设计成可以在“普通”HTML 文档中的任何地方嵌入小部件，或者突破框架，在任何小部件中呈现一些“普通”HTML。这种在需要的地方使用工具包，在单个应用程序中的其他地方使用更传统的方法的能力可能是 AjaxTK 最大的卖点。

## 雅虎！用户界面库

就在昨天，雅虎宣布 UI 库还在起步阶段，但是有了雅虎的势头！在其背后，它有望成为一个非常有影响力的玩家。

如果说这个库有什么吸引人的地方，那就是文档。从第一天起，每一个可用的组件都有完整的 API 文档，以及一个简短的“入门”指南，并附有工作示例。不需要浏览开发者的博客，不需要拆开复杂的应用程序:这个工具包拥有商业产品的所有精华。

目前，图书馆的范围有些有限。除了动画、远程脚本(AJAX)、事件处理、DOM 操作和拖放等核心实用工具之外(这些当然不容忽视！)，该库目前只提供了三个用于构建用户界面的小部件:

*   [日历](https://developer.yahoo.net/yui/calendar/)用于选择日期
*   [滑块](https://developer.yahoo.net/yui/slider/)用于从一系列数值中进行视觉选择
*   [树形视图](https://developer.yahoo.net/yui/treeview/)用于浏览项目的层次结构

虽然选择并不多，但这三个小部件已经解决了一些传统上需要大量工作才能解决的问题。毫无疑问，未来的小部件版本将解决 web 应用程序用户界面的一些更普通的需求。

看到雅虎特别让人耳目一新！打开它的盒子，与 web 开发社区分享它们。这一举措与谷歌通过隐藏在模糊代码中来保护其 JavaScript 资产的方法形成鲜明对比。

当然，除了我在这里提到的四个，还有许多其他的图书馆，所以如果我漏掉了你最喜欢的，请不要沮丧。也就是说，我选择这四个是因为社区支持、积极的开发以及它们给开发人员带来的自由。如果你认为你选择的图书馆属于这个列表，让我知道为什么。

## 怎么办:网页设计模式

就在我开始理解网页设计的时候——logo 在顶部，导航在左边，烦人的 Flash 广告在右边——新一波的网页应用似乎正在打破所有的规则。突然之间，到处都有新的可用性陷阱。值得庆幸的是，有很多方法可以从别人的错误中吸取教训。

以及它的用户界面库 Yahoo！昨天[宣布](https://yuiblog.com/blog/2006/02/13/yahoo_patterns_released/)出版[雅虎！设计模式库](https://developer.yahoo.net/ypatterns/)，常见网页设计问题的解决方案集合。以下是您可以在库中找到的一些模式:

*   [自动完成](https://developer.yahoo.net/ypatterns/pattern_autocomplete.php)
*   [面包屑](https://developer.yahoo.net/ypatterns/pattern_breadcrumbs.php)
*   [拖放模块](https://developer.yahoo.net/ypatterns/pattern_dragdropmodules.php)
*   [模块标签](https://developer.yahoo.net/ypatterns/pattern_moduletabs.php)
*   [导航标签](https://developer.yahoo.net/ypatterns/pattern_navigationtabs.php)
*   [对象分页](https://developer.yahoo.net/ypatterns/pattern_objectpagination.php)
*   [搜索分页](https://developer.yahoo.net/ypatterns/pattern_searchpagination.php)
*   [回顾架构](https://developer.yahoo.net/ypatterns/pattern_architectureofreview.php)
*   [评价一个对象](https://developer.yahoo.net/ypatterns/pattern_ratinganobject.php)
*   [写评论](https://developer.yahoo.net/ypatterns/pattern_writingareview.php)

这些模式中的每一个都有一个动画屏幕截图，显示了正在使用的模式的典型实现，以及它所解决的问题的要点形式的分解，在实现解决方案时要正确对待的重要细节，这些细节背后的基本原理，以及解决方案的可访问性考虑。

虽然雅虎！设计模式库目前只包含少量的模式(他们的公告说有 13 种，但我只能找到 10 种)，该库承诺在不久的将来会像 Yahoo！润色内部文件供公众阅读。

和它的用户界面库一样，Yahoo！并不是第一个生产这种东西的。谈到 web 设计模式，还有许多其他的好资源:

welie.com——交互设计中的模式提供了一些非常巧妙的模式，尽管它们中的大多数只适用于传统的网页设计，而不适用于丰富的网页应用。特别值得注意的是“站点类型”模式，它提供了不同类型站点(例如博物馆站点)通常需要的设计元素列表。

![](img/295a62268c598019d22691e926775a6c.png) [设计界面](https://designinginterfaces.com/)是一个通用用户界面模式的精选集合，其中许多应用于网页设计。这些模式最近被收集在 O'Reilly 的书<cite>设计界面</cite>中，本网站是该书的配套。

顺便说一句，SitePoint 有一本类似的关于可用性的书，预计今年晚些时候发行，专门针对网页设计。

最后，John Allsop 的 [WebPatterns](https://webpatterns.org/) 网站看起来像是社区制作的 web 设计模式的未来家园，但现在才刚刚起步(缓慢)。

简而言之，当代码猴子们继续探索网络的极限时，一些真正的解决方案正在由像雅虎这样的公司产生。足够稳定，可以在日常的 web 开发中使用。不要被落下！

无论您是钻研一个新的 JavaScript 库，还是探索一些适用于您的工作类型的设计模式，现在都是为您的 web 开发技能注入新活力的好时机。

## 分享这篇文章