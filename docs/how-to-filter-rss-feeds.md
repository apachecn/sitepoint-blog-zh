# 如何过滤 RSS 源

> 原文：<https://www.sitepoint.com/how-to-filter-rss-feeds/>

虽然我们总是希望你们喜欢我们在 SitePoint 上创建的内容——无论是技术、商业、设计相关的内容，还是网络技术新闻、趋势或分析——但我们也注意到你们中的一些人对 SitePoint 主页的新方向不太感兴趣。作为我们的评论者之一，heggaton，[上周写道](https://www.sitepoint.com/20-sites-to-get-you-in-shape/#comment-783267)，“有什么办法可以让我用谷歌阅读器过滤掉 Josh 发的任何帖子吗？”

你很幸运，海格顿，因为确实有办法过滤掉我的帖子。(不过，正如我在那篇文章的评论中提到的，我几乎是专门为 SitePoint 网站上的[新闻&趋势](https://www.sitepoint.com/newsletters/)博客撰写文章的。如果你访问 [SitePoint 博客](https://www.sitepoint.com/)页面，你可以订阅特定网络开发主题的博客，包括[技术](https://www.sitepoint.com/blog/)、[商业](https://www.sitepoint.com/entrepreneur/business/)和[设计](https://www.sitepoint.com/design-ux/)的总括类别，而不是完整的博客提要。)

当然，我希望你*不要*把我从你的 SitePoint 体验中过滤掉，但是如果你不喜欢强调网络技术新闻、趋势、分析、应用评论，这篇文章将向你展示如何用两种方法过滤我们的 RSS 提要，希望只得到你最感兴趣的内容。

## 方法 1:饲料冲洗

我们发现的最好也是最容易使用的饲料过滤应用是 [FeedRinse](http://feedrinse.com/) 。FeedRinse 一度是一项付费的“免费增值”服务，但自 2006 年 11 月以来，它已经完全免费了。一个免费帐户可以过滤多达 500 个过滤规则的无限数量的饲料。

过滤提要是一个非常简单的过程。首先，手动向网站添加提要，或者上传一个 OPML 文件。然后你为每个博客设置规则。

![](img/f65c915c3522a9eb2ea5ef5c8d6d2530.png "feedrinse-screen")

FeedRinse 允许非常复杂的过滤规则，允许你阻止或允许符合任何或所有指定规则的帖子。您可以将规则应用于整个帖子、作者字段、标题字段、标签或仅正文。如果帖子包含、不包含、以您输入的任何关键字开头或结尾，规则可以进行过滤。因此，举例来说，你可以使用 FeedRinse 过滤掉由我(乔希·卡顿)创作的 SitePoint 完整博客订阅源中的任何帖子。这个练习的结果是这里的。

你也可以使用 FeedRinse 过滤掉(或加入)关于特定主题的帖子。例如，你可以设置一个过滤器，让任何关于 Photoshop 的文章通过设计博客，但排除任何关于 CSS 的文章。通过使用一个独立的 RSS 混合应用程序，比如 [RSS Mixer](http://www.rssmixer.com/) ，你可以将一个主题(比如说设计)上的多个 feed 组合起来，然后针对一个特定的子主题(比如说 Photoshop)对它们进行过滤，并创建一个针对你感兴趣的领域的 feed。

雅虎！Pipes 也可以做这种类型的 feed 过滤，但是比 FeedRinse 有更陡峭的学习曲线。

## 方法 2:辅助工具

[AideRSS](http://www.aiderss.com/) 是我最喜欢的 RSS 过滤应用之一。它不是像 FeedRinse 那样的精确过滤器，而是使用社交互动来过滤帖子，以确定人群认为博客的最佳内容。AideRSS 目前会查看评论、美味书签、Diggs、谷歌对话和推文，为每篇帖子分配一个“PostRank”(10 分制的数字排名)。PostRank 是相对于每个博客而言的，所以虽然 20 条评论可能意味着一个博客的高 PR，但对于一个更活跃的博客来说，它对该帖子的 PR 的贡献可能要低得多。

一旦 AideRSS 分析了一个提要，它就可以通过 PostRank 过滤出最好的内容，并将其放在一个新的 RSS 提要中。[例如，这个 feed](http://www.aiderss.com/great/www.sitepoint.com/feed) 只显示了在过去几天里 AideRSS 认为“很棒”的 SitePoint 完整博客 feed 中的帖子。

AideRSS 的一个伟大之处在于，它的底层过滤算法已经作为独立的 API 发布，这意味着有进取心的开发人员可以轻松地将社交 RSS 订阅过滤添加到他们的应用程序中。 [NewsGator Online](http://www.newsgator.com/) 已经在使用 PostRank， [Trawlr](http://www.trawlr.com/) 也在使用，还有一个[谷歌阅读器插件](http://gr.aiderss.com/)。

## 结束语

很明显，我希望你不要打算从 SitePoint 的订阅源中过滤掉我的任何内容——你可以随时通过电子邮件与我联系，就你希望在 SitePoint 的新闻和趋势博客([josh.catone@sitepoint.com](mailto:josh.catone@sitepoint.com))上看到的内容类型提出任何建议或担忧。但是这个选择是存在的，我们希望这个帖子在这方面有所帮助。

请记住，您可以从我们的[订阅页面](https://www.sitepoint.com/newsletters/)订阅 SitePoint 的任何博客、论坛订阅源或时事通讯。

当然，这里具有讽刺意味的是，如果 heggaton 遵循了这个帖子中的建议，并决定阻止我未来的帖子，他就会在发现我的帖子有用的那一刻决定过滤它们。

## 分享这篇文章