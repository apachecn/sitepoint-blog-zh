# RDFa:下一代搜索引擎唯一的网络可扩展元数据格式

> 原文：<https://www.sitepoint.com/why-rdfa-is-the-only-web-scaleable-metadata-format-for-next-generation-search-engines/>

雅虎！即将推出他们的下一代网络搜索系统，名为 [SearchMonkey](https://tools.search.yahoo.com/newsearch/open.html) 。这意味着内容开发者拥有了一个强大的新工具。这在以前几乎是不可能的。这里有一个来自雅虎的快速预览！

![search 1.0](img/65b3d5b467488baa217d711d5026c6d1.png)

![next-generation search ](img/e7ec9e51dcb3a5825dfd3d30157baf75.png)

**不再依赖谷歌**

你不再需要依靠谷歌的恩惠(和他们聪明的人)来理解你努力创造的内容。你可以明确地指定**你所指的**是什么，没有歧义。

我本打算几天后写这篇文章，但是 [OpenBible.info](https://www.openbible.info/) 上的一篇[帖子](https://www.openbible.info/blog/2008/03/yahoo-bibleref-and-rdfa/)迫使我不得不这么做——谢谢:)

> 雅虎！上周宣布它将开始索引语义数据，包括对某些微格式的支持。
> 
> Bibleref 不是这些微格式中的一种。Bibleref 的支持者应该游说雅虎吗？为了索引 Bibleref，或者 Bibleref 应该改变它的语法以兼容 RDFa 或另一个语义 web 标准？
> 
> ….
> 
> 那么 Bibleref 的支持者应该怎么做呢？我们有可能说服雅虎！索引 Bibleref，给它起飞所需的牵引力。然而，我不一定期望雅虎！做好理解数据的工作，部分是因为标准的松散(我认为这是一件好事)。如果雅虎！没有很好的理解，那么基于 Bibleref 的搜索结果质量不会很高。但这在很大程度上取决于雅虎。公开数据。(而且他们可能甚至不想索引 Bibleref。)
> 
> ##### RDFa
> 
> 另一种可能性是改变 Bibleref，使其与 RDFa 兼容。确实明白…

他们解释得比我好！在 Bibleref 上工作的好人们现在的处境，我相信你们中的许多人很快也会如此。

**[6.4 万美元的问题](https://en.wikipedia.org/wiki/The_$64,000_Question)**

![//en.wikipedia.org/wiki/The_$64,000_Question](img/f631d53f91d54d201fdbd258c7970ddb.png)

我们如何以雅虎能理解的格式发布我们的智能信息？SearchMonkey 和其他下一代搜索引擎？在[链接数据网](https://talk.talis.com/archives/2008/02/sir_tim_berners.html "Tim Berners-Lee Talks with Talis about the Semantic Web and Linked Data")/语义网的新前沿，你如何获得有价值的元数据？

目前有两个主要选项: [RDFa](https://en.wikipedia.org/wiki/RDFa) 和[微格式](https://microformats.org/)。但当然没那么简单。

**微格式的问题**

微格式的主要问题是，每次创建新的微格式时，搜索索引器都需要开发一个定制的提取器来理解微格式。这就是为什么雅虎微搜索只索引了 3 种最流行的格式，以及为什么当 SearchMonkey 推出时，它只索引了 5 种格式。在[主](https://microformats.org/wiki/Main_Page)维基页面上列出的 20 个中有 5 个，在[探索](https://microformats.org/wiki/exploratory-discussions)页面上有 74 个。

这意味着如果你使用 94 种微格式中的任何一种，SearchMonkey 将只能看到其中的 5 种。

还存在其他人之前已经注意到的其他问题。很难[混搭](https://wiki.digitalbazaar.com/en/Haudio-case-study#Problems_Encountered_with_Microformats)不同的微格式；这给布局灵活性带来了一个[的大限制](https://www.glazman.org/weblog/dotclear/index.php?post/2008/03/14/Microformats-20)。没有简单的方法来[验证你的工作](https://my.opera.com/tomheath/blog/2008/02/26/microformat-authoring-not-necessarily-easy)。微格式的使用也引发了[可访问性问题](https://www.snee.com/bobdc.blog/2008/03/accessibility_problems_with_mi.html)。

> 这就是微格式的问题所在。如果没有底层的抽象数据模型，验证就变得有点像站在后面看着一辆二手车，踢踢轮胎，得出“是的，看起来不错”的结论，然后交出现金。

搜索引擎是做什么的？

那么作为一家搜索引擎公司，你会倾向于什么呢？编写一个 RDFa 解析器，并接受用 RDFa 创建的所有元数据。或者为现在可用的每种微格式加上将来的每种新格式编写一个新的解析器？

**网络可扩展元数据**

RDFa 很快将成为 W3C 标准(或者他们所谓的推荐标准)。把所有的碎片拼在一起需要一段时间，但任何如此重要的东西都需要时间。与此同时，一个经过深思熟虑的解决方案出现了:

1.  **可扩展**–任何你想要的词汇。创造你自己的，尽情发挥吧！
2.  **Mixable**–在你想要的任何布局中混合搭配你想要的任何词汇。
3.  W3C 标准–一个解析器读取任何词汇的原因是，验证是微不足道的。
4.  **全球可识别的**–给你页面上的任何东西一个 URL，它就变成了网络上一个“活的”数据点；任何人都可以轻易找到。
5.  您的页面成为一个独立的链接数据客户端；**像数据库一样可查询**。这真的很酷。

**了解有关 RDFa 的更多信息**

RDFa 小组刚刚推出了一个 [wiki](https://rdfa.info/wiki/RDFa_Wiki) (信息主体不断增加)和一个[邮件列表](https://lists.w3.org/Archives/Public/public-rdfa/)。也可以通过 IRC/#swig 联系他们。请继续关注，因为我计划添加一些关于如何在你自己的网页中使用 RDFa 的新帖子。

*(图片来自[维基百科](https://en.wikipedia.org/wiki/Image:Takeitorleaveit.jpg) )*

## 分享这篇文章