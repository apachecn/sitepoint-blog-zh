# 谷歌印刷品上的丹尼·沙利文

> 原文：<https://www.sitepoint.com/danny-sullivan-on-google-print/>

丹尼·沙利文在[索引与缓存&谷歌打印如何不重印](http://blog.searchenginewatch.com/blog/051021-113341)中发布了一篇关于谷歌打印相关技术问题的精彩分析。

我 100%同意丹尼的观点，他认为给一本书的内容编制索引以使其变得可搜索与创作或出版这本书的副本不是一回事。他在这一点上是正确的，但他的帖子延续了对搜索引擎如何工作的误解。这种误解是出版商认为谷歌“窃取”他们知识产权的部分原因。

Danny 将搜索引擎索引描述为类似于一个大的电子表格(着重号后加):

> 我把这个索引描述成“一本网络大书”但其实不是，真的。它更像一个巨大的电子表格，其中一页的所有单词都在电子表格的一行中，每个单词在不同的列中，**然后是下一行的下一页**，以此类推。

实际上，索引的可读性远不如电子表格，因为搜索引擎在创建索引时存储的是出现的单词，而不是文档。它不是每个文档都有一行，而是每个单词出现的表格。

如果单词“defenestration”出现在网页上，像 Google 这样的搜索引擎会存储一个文档 ID(引用 URL)，在页面中的位置(第 342 个单词)，以及其他一些东西，比如它是否是斜体或其他什么。**该事件将与所有其他“解除隔离”事件一起存储在数据库中，而不是存储在该文件的单独记录中。**

按单词索引非常方便，因为人们用单词搜索。如果我搜索“去隔离指导手册”，搜索引擎可以很快找到所有 3 个单词的索引中列出的所有文档。搜索 3 个单词的索引比搜索 80 亿个文档要快得多。

文档本身没有单独的“索引”。单词出现次数被存储，文档是一次性的。要重建一个文档，您必须查看每个单词索引，找到所有与文档 ID 匹配的单词，然后将它们放回一起。

就网页而言，通常会保存一份缓存副本，但 Google Print 不提供这种功能。如果你用 Google Print 搜索一些单词，他们会告诉你这些单词出现在哪些书籍中，并给你一个非常小的上下文片段，这是你会发现的最接近“合理使用”的内容…我刚刚引用了 Danny 的知识产权中比 Google Print 更实质性的一部分，我完全在合理合理使用的范围内。

我知道丹尼完全理解这些东西，他只是想表明自己的观点。所以让我反复强调这一点，因为我同意他的观点。

我曾经在联邦快递金科公司工作，该公司是文件管理解决方案的世界领导者。我知道企业愿意花多少钱将他们的遗留文档转换成可搜索的电子格式。我不明白的是，当出版商看到谷歌免费为他们做这项工作时，他们为什么不翻跟头。

## 分享这篇文章