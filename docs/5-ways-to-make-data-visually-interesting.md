# 让数据看起来有趣的 5 种方法

> 原文：<https://www.sitepoint.com/5-ways-to-make-data-visually-interesting/>

作为一名开发人员，有时将表格数据转储到网页上并称之为“好”实在是太容易了你知道，只是做一个简单的表格，给它一些基本的 CSS 来美化它，你就完成了，对不对？不对。如果你是一个有思想的设计师，那么你会意识到数据在页面上是为了证明一个观点，而你的工作就是让那些乏味的数据变得有趣和引人注目。

我们生活在一个数据丰富的时代。有些人可能称之为“信息过载时代”。这就是为什么将这些数据转化为可操作的信息非常重要。那么，我们如何获取海量数据并创造有趣、吸引人的信息呢？以下是几个可以入手的地方。虽然其中大多数都有点技术性，但您可以查看最后一个技巧以及最后的示例，为数据的设计收集灵感。

### 使用“While”循环自定义表格

我知道我只是在取笑那些只是将数据转储到一个表中并称之为可以使用的开发人员，但是仍然有一些强大的方法来使用表，这些方法需要的不仅仅是处理一些 CSS 并离开。我总是使用 PHP 的“while”循环制作动态数据集。以这种方式生成表格很容易，但要让它们看起来更好并吸引用户并不总是那么容易。

### 第 n 个子 CSS 伪类

我经常不得不使用 PHP 或其他脚本语言来生成大量的表格数据。在处理动态数据时，使您的表在视觉上更具吸引力的最简单的方法之一是使用第 n 个子 CSS 调用。这里有一个 CSS 的例子:

[sourcecode language="html"]
。some class tr:n-child(奇数){ background-color:# CCC；}
。some class tr:n-child(偶数){ background-color:# fff；}
[/源代码]

当您对表进行编码时，它应该是这样的:

[sourcecode language="html"]
<【table class = " some class ">
<【tbody】>
【tr27】><>first name< </tr>
【tr64】
<【TD】>【Sammy】/TD>
<TD>billes<

现在，只需使用这种伪类技术。要知道 IE 不支持这个伪类，但是有一些 jQuery 的变通方法。我发现实现这一点的最简单方法是添加另一组 CSS 类，如下所示:

[source code language = " html "]
/* CSS for IE 使用伪类 n-child */
。some class tr:n-child(奇数){ background-color:# CCC；}
。some class tr:n-child(偶数){ background-color:# fff；}
[/源代码]

然后，使用以下 jQuery 使其工作:

[source code language = " html "]
<script type = " text/JavaScript " src = " https://Ajax . Google APIs . com/Ajax/libs/jQuery/1 . 8 . 3/jQuery . min . js "></script>//从 Google CDN 或 wherevs 调用 jQuery<script type = " text/JavaScript ">//<！[CDATA[
$(文档)。ready(function() {
$(”。some class tr:n-child(偶数)")。addClass(" even ")；
$(”。some class tr:n-child(奇数)")。addClass(" odd ")；
})；
/]]></script>
[/source code]

我把上面所有的代码放在我的 HTML 文档的 header 部分。

### 可排序的列

我也喜欢让我的专栏可排序。这使得用户可以根据自己的目的和理解快速操作和排列数据。

不过，你不能只勾选一个框就让表格变得可排序。您将不得不使用 jQuery 或其他 JavaScript 来让它工作。我使用一个简单的名为 TableSorter 的库，它允许我向表中添加一个类，我就完成了。

1.  从[TableSorter.com](https://tablesorter.com)下载 JS 库
2.  在 HTML 中添加引用(参见他们的文档)
3.  将该类添加到您的表中。

记住，任何 HTML 元素都可以有几个类；只需使用:

[source code language = " html "]
<table class = " first class second class nth class etc class "></table>
[/source code]

在 TableSorter.com[的](https://tablesorter.com/docs/example-pager.html)查看 [Pager.js](https://tablesorter.com/addons/pager/jquery.tablesorter.pager.js) 代码，它可以很容易地翻阅表格结果。如果您有非常大的表，那么您会喜欢它是多么容易实现。

### 筛选列

最后，我越来越喜欢在表格中使用过滤器的能力。尽管我们不愿意承认，但人类大脑的活动记忆有限，因此专注于数据的某些选择或方面可以产生更强的影响，并更好地理解信息。如果您熟悉 Excel，用户可以选择筛选列，然后从列表中选择或使用搜索功能来筛选出他们想要的数据。

已经有许多强大的过滤包可供使用。我喜欢 PicNet 表格过滤器，因为它很容易与我经常使用的动态表格挂钩。

如果你正在寻找一套更坚固的桌子，看看 [Flex Grid](https://flexigrid.info/) ，它拥有你想要的一切。这并不容易绑定到动态表中，但是即使您确实想花时间开发一个完整的系统，它也能做得很好。

### 有趣的布局

对于那些对数据格式化的开发人员不太感兴趣的人，您可以简单地创建一个有点与众不同的布局。不要使用简单的折线图或饼图，而是使用信息图，或者在数据中加入图形字符。如果你正在处理的数据不断变化，你也可以利用[动态图表库](https://www.sitepoint.com/visualize-your-data-and-speed-up-your-site-with-dynamic-chart-libraries/ "Visualize Your Data And Speed Up Your Site With Dynamic Chart Libraries")。以下是一些我最喜欢的数据例子，设计师们让这些数据变得足够有趣，可以作为墙面艺术来展示:

![Reasons to Do Business in NYC](img/86b1807f76d93323eac72c091b192173.png)
由 [NYCEDC](https://www.nycedc.com/competition) 担任。浏览更多[数据可视化](https://visual.ly)。

![seasonal pie filling and pie recipes](img/4c6021b294f7a0d887d199e6f23e73bd.png)

[![](img/76e07dfc9a2d8ca66d37f485cbce6dde.png)](https://walyou.com/most-useful-infographic-tools/)

![](img/19f4f79f624f98e38948a49bac2b4a4f.png)

[![](img/956d2985a76d429998dbbc107d20b5cd.png)](https://www.behance.net/gallery/Pattern-Matters-Tangible-Paper-Infographic/3411991) 
实物 3D 纸图由[项青](https://www.behance.net/siangching)

[![](img/5137ef5674086e70453e8509a717dd66.png)](https://www.behance.net/gallery/Bloodlife-Interactive-Infographic-System/5477515) 
互动信息图系统由[马丁·利维拉托](https://www.behance.net/martinliveratore)

[![](img/5d3c04b51362ab6cf05fd57000b4f810.png)](https://www.behance.net/gallery/Information-graphics-in-context/924345)

彼得·奥恩托夫特

[![](img/23e3fac53a5059127ed8850c68e9eb23.png)](https://www.behance.net/gallery/TATTOO-INFOGRAPHICS/3065641)

保罗·马辛科夫斯基的纹身信息图

[![](img/e045ec7b2791292005850ec833f214c7.png)](https://blog.printplace.com/print-is-big/)

[![](img/8a85cff2a74c2982045cd1e25c654a45.png)](https://www.behance.net/gallery/Lunar-Tides-InfoGraphic/1155757)

丹尼尔·奥科米特制作的月球潮汐物理信息图

你有什么有趣的数据故事吗？你有没有遇到过将大量数据转化为可操作的可视化设计的任务？

## 分享这篇文章