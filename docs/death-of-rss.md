# RSS 死了吗？

> 原文：<https://www.sitepoint.com/death-of-rss/>

RSS 的消亡被以惊人的频率预测，谷歌搜索会返回 4 亿个结果。虽然这本身并不是一个可靠的指标，但许多主要的技术网站在超过 5 年的时间里都以*【再见 RSS】*文章为特色。现在其他人将会出现 [Bloglines 已经加入了 RSS 天堂的许多其他系统](https://www.sitepoint.com/bloglines-rip-2003-2010/)。

RSS 在 2005 年被广泛采用。这是一个激动人心的时代，网络营销商开始大肆宣传一场新的技术革命。RSS 被与 Ajax 和 Web2.0 相提并论。很少有人理解这些术语的含义或该技术将如何应用，这并不重要。

RSS 的意思是“真正简单的联合”。这是一种基于 XML 的标准数据格式，可以由大多数 web 框架和语言创建和解析。一个基本的 RSS2.0 单链接示例:

```
 <?xml version="1.0" encoding="utf-8"?>
<rss version="2.0">
<channel>
	<title>Example feed</title>
	<link>http://www.example.com/</link>
	<description>This is RSS</description>
	<lastBuildDate>Mon, 11 Oct 2010 12:00:00 GMT</lastBuildDate>
	<pubDate>Mon, 10 Oct 2010 12:00:00 GMT</pubDate>

	<item>
		<title>My article</title>
		<description>My first RSS article</description>
		<link>http://www.example.com/my-article</link>
		<guid>1</guid>
		<pubDate>Mon, 10 Oct 2010 12:01:00 GMT</pubDate>
	</item>

</channel>
</rss> 
```

RSS 很快被 IT 专家和高级用户采用。它(有效地)允许网站向你推送文章——不需要访问一个网站并手动检查内容更新。每周都会出现新的桌面和网络 RSS 聚合器。浏览器和电子邮件供应商迅速跟上潮流，实现了 RSS 功能。

尽管有这些优势，但很少有主流网络用户使用过 feed 阅读器。也许这并不奇怪:

*   术语和行话远比它所代表的技术复杂。
*   很少有人知道浏览器是什么；“feed 阅读器”或“新闻聚合器”还有什么希望？
*   聚合器通常需要了解 RSS URL——对许多人来说，这又是一个陌生的话题。
*   用户理解网络搜索和页面请求，但是“推送”内容是一个更难理解的概念。

许多文章指责社交网络的迅速崛起导致了 RSS 的死亡。Twitter 流或脸书更新更容易理解，并吸引更广泛的用户受众。这些系统的发展见证了 RSS 聚合器产品和服务的相应失败。

然而，RSS 还远未消亡。用户可能没有意识到这一点，但这项技术正在为网站间的交流和互动提供动力。大多数社交网络都提供或使用 RSS。混搭通常使用提要来组合数据。谷歌吞噬 RSS 数据，为网站和产品搜索提供动力。

RSS 已经成为一种透明的数据交换协议。像 TCP/IP 一样，用户永远不需要知道它的存在，为什么使用它，或者它是如何工作的。很少有人直接与 feeds 互动，所以新闻聚合器的日子可能屈指可数了——但是 RSS 已经存在了。它可能不会收到同样的营销炒作，但 RSS 在后台默默有效地工作。

现已推出… [**博客的 5 个 RSS 阅读器替代品…**](https://www.sitepoint.com/5-rss-reader-news-aggregators/)

## 分享这篇文章