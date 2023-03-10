# 维基百科和 Mediawiki 上的 Brion Vibber

> 原文：<https://www.sitepoint.com/brion-vibber-on-wikipedia-and-mediawiki/>

看看 alexa 的全球 500 大热门网站的前 20 名，有一点很突出，大多数主要是“只读”网站——新闻、搜索或其他内容更新主要由网站运营者管理的网站。

这里的三大例外是 myspaces(运行。现在我相信是——是 Coldfusion)、ebay(他们已经完全移植到 J2EE 了吗，或者一些本土的 C++还在吗？)和维基百科(LAMP)。在某种程度上，所有这些都是协作网站，内容主要由用户创建。换句话说，它们必须能够支持大量的*写入*以及读取。这很有趣，因为就扩展而言，您提供的数据越不稳定，就越难扩展，这就提出了“如何缓存”这样的问题，“你如何处理事务/锁定？”、“如何分发更新”等。

无论如何，维基百科运行 LAMP 使得它有点像海报中的孩子，正如你可能知道的，维基百科上使用的软件是用 PHP 编写的 [mediawiki](http://www.mediawiki.org/) 。鉴于[维基媒体基金会](http://wikimediafoundation.org/wiki/Home)不得不解决的技术问题的规模，在过去从相关人员那里找到他们如何做的细节有点令人沮丧。感谢 [Brion Vibber](http://en.wikipedia.org/wiki/User:Brion_VIBBER) 我们现在有了更多的信息…

首先是他上月底与谷歌的对话。这里有一些有趣的细节和琐事(例如，他们目前平均每秒更新 1 次)，考虑到他们“只有”大约 100 台应用服务器(运行 mediawiki 代码)，总体印象几乎是“这就够了吗？互联网有多小”——Brion 用“需要一点努力”这样的话淡化了为实现这一目标所付出的努力。他还提到了他们的 [wiki 语法解析器](http://svn.wikimedia.org/viewvc/mediawiki/trunk/phase3/includes/Parser.php?view=markup)的一些问题，这些问题与我们之前在其他地方见过的[的问题类似](https://www.sitepoint.com/oop-and-performance/)——他们*似乎*试图用一个基于 C 的解析器来替换它，这个解析器暴露了作为 PHP 扩展的[，但是考虑到最后一次更改的日期，这是一个已经停止的努力吗？此外，挖苦地指出，与维基媒体如何融资相关的问题数量——鉴于这是一个技术谈话和位置，让你去“嗯嗯……”。](http://svn.wikimedia.org/viewvc/mediawiki/trunk/flexbisonparse/)

接下来，更多的细节(带有更强烈的 PHP 倾向)来自 [php architects](http://www.phparch.com) 对 Brion Vibber 的网络广播采访——[Marcus](http://www.marcuswhitney.com/)在提出相关问题方面做得很好——也许最大的一项是维基百科服务器已经在运行 PHP 5，即使代码还没有利用这一事实。旁注:想象一下如果维基百科运行在类似。NET——你能想象成功升级到最新版本后会有多少营销噪音吗？有趣的是，灯的世界以不同的方式移动。无论如何——你将不得不听更多的细节。

伟大的东西，感谢 Brion 这样做。

## 分享这篇文章