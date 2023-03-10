# 为数据网络准备您的站点

> 原文：<https://www.sitepoint.com/preparing-your-sites-for-the-data-web/>

好了，[数据网](http://www.australianit.news.com.au/story/0,24897,23368264-16123,00.html)(来自**男子本人——蒂姆·伯纳斯·李)来了。[链接数据](http://esw.w3.org/topic/SweoIG/TaskForces/CommunityProjects/LinkingOpenData)将主宰这一天，如果你不赶快行动，你的网站将被甩在后面。或者更有可能的是，你的网站将只是罢工，要求更好的工作条件，并遵守这样一个事实，即它能够做得更多，而你没有足够的关心去看到它。**

 **[![Semantic Web](img/36f22def209e1db129fc4cbc4a175d14.png)](http://esw.w3.org/topic/SweoIG/TaskForces/CommunityProjects/LinkingOpenData)

所以，给你的网站一些爱，给他们应得的尊重。

好吧，那么到底是什么让你想这么做呢？

*   其他人都在这么做——嗯，不尽然。[马克 Z 不是](http://www.readwriteweb.com/archives/yahoo_supports_semantic_web.php)。但是你比他更聪明，不是吗？
*   雅虎和谷歌正在这么做。现在我们有所进展。
*   [麻线](http://twine.com/)、 [Dbpedia](http://dbpedia.org/About) (维基百科的链接数据版本)、 [Revyu](http://revyu.com/) 目前都是链接数据公民，我们都想成为他们那样，不是吗？
*   Drupal 7 [将成为一个成熟的数据网络客户端](https://www.sitepoint.com/drupal-7-a-living-breathing-semantic-web-citizen)

> …如果没有一个面向消费者的杀手级语义 web 应用，网站所有者就不愿意支持像 [RDF](https://www.w3.org/RDF/) 甚至[微格式](http://microformats.org/)这样的标准。我们认为该应用程序可以进行网络搜索。
> 
> 通过支持语义网标准，雅虎！搜索和网站所有者可以给消费者带来更丰富、更有用的搜索体验…
> 
> 在接下来的几周内，我们将发布更详细的规范，描述我们对语义网标准的支持。最初，我们计划支持一些[微格式](http://microformats.org/)，包括 [hCard](http://microformats.org/wiki/hcard) 、 [hCalendar](http://microformats.org/wiki/hcalendar) 、 [hReview](http://microformats.org/wiki/hreview) 、 [hAtom](http://microformats.org/wiki/hatom) 和 [XFN](http://microformats.org/wiki/xfn) 。雅虎！搜索将与网络社区一起发展嵌入结构化数据的词汇框架。首先，我们计划支持来自 [Dublin Core](http://en.wikipedia.org/wiki/Dublin_Core) 、 [Creative Commons](http://creativecommons.org/) 、 [FOAF](http://www.foaf-project.org/) 、 [GeoRSS](http://www.georss.org/) 、 [MediaRSS](http://search.yahoo.com/mrss) 以及其他基于反馈的词汇表组件。而且，我们将支持 [RDFa](http://en.wikipedia.org/wiki/RDFa) 和 [eRDF](http://en.wikipedia.org/wiki/Embedded_RDF) 标记，将它们嵌入到现有的 HTML 页面中。最后，我们宣布支持 [OpenSearch](http://opensearch.org/) 规范，并扩展了对 deep web 数据源的结构化查询。
> 
> 来源:雅虎！搜索博客

上个月我讲过这个[，但那时候是由](https://www.sitepoint.com/one-small-step-for-yahoo-one-giant-leap-for-embedded-metadata/ "One small step for Yahoo, one giant leap for embedded metadata")[彼得·米卡](http://research.yahoo.com/bouncer_user/66)开发的一个研究项目。看起来他们动作很快！干得好雅虎。

Google Open Social 已经将微格式和 FOAF 编入索引，以帮助生成其社交图。这是一个自然的过程。正如蒂姆·伯纳斯·李最近所说:

> 对脸书和 MySpace 等社交网站的“当前狂热”最终将被连接所有类型事物(不仅仅是人)的网络所取代，这要归功于一项被称为“语义网”的开创性技术。
> 
> —来源[澳大利亚 IT](http://www.australianit.news.com.au/story/0,24897,23368264-16123,00.html)

**反思基础知识**

这个即将到来的变化是巨大的。这意味着对你的网站，你的信息，你的订阅源，所有东西的一种新的思考方式。每一条信息都能够并且应该能够钩入数据网。用雅虎！考虑到未来，在正确的方向上，你可以肯定谷歌、微软和其他搜索引擎会紧随其后。我确信他们也一直在追踪这个问题，甚至可能已经有所进展。

**你现在能做什么**

开始学习微格式比如: [hCard](http://microformats.org/wiki/hcard) 、 [hCalendar](http://microformats.org/wiki/hcalendar) 、 [hReview](http://microformats.org/wiki/hreview) 、 [hAtom](http://microformats.org/wiki/hatom) 。这些是一些更常见的格式，这也是搜索引擎会选择的。如果你创建自定义的微格式或者使用其他格式，你就有可能根本不会被发现。我将在另一篇博文中解释原因。

学习 [RDFa](http://en.wikipedia.org/wiki/RDFa) 。RDFa 将成为 W3C 在网页中嵌入元数据的标准。这是释放关联数据真正力量的关键。RDFa 说的是语义网的母语 RDF。这创造了直接的好处，如链接数据，混合词汇(想象一个艺术家的音乐曲目和他们的 FOAF 文件在一行)和创建自定义词汇。

RDFa 不能取代微格式——尽管您可以用它们来代替。它有自己的标记，目前只对 XHTML 有效(对于 HTML 4 有一些动作来获得配置文件),还有自己的学习曲线。RDFa 阵营现在正全力以赴地收集材料来建立一个 [wiki](http://rdfa.info/wiki/RDFa_Wiki) 来帮助每个人开始。

**SEO 新技能和新市场优势**

了解数据网络的细节将变得越来越重要。当雅虎这样的网站。开始赋予链接数据更多的相关性，并开始以更有趣的方式呈现链接数据网站。你可以打赌，你的客户[会要求](http://searchengineland.com/080313-110000.php)进行嵌入式元数据/链接数据审计。

对于任何愿意更进一步的人来说，这是一个好时机。** 

## **分享这篇文章**