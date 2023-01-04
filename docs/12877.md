# RSS 过滤现在是带有 PostRank 的 API

> 原文：<https://www.sitepoint.com/rss-filtering-now-an-api-with-postrank/>

加拿大创业公司 [AideRSS](http://www.aiderss.com/) 是自切片面包以来最酷的东西之一。不，真的。这听起来可能有点夸张，但这是我在过去一年中遇到的最有用的工具之一。2006 年 12 月，当我第一次读到开发者伊利亚·格里戈利克关于 RSS 过滤算法的[计划时，我的第一个想法是，“我必须给这个家伙打电话，说服他把这个变成一个网络服务。”我拖延了，但格里戈利克没有。2007 年 7 月，他推出了 AideRSS，一个基于他最初 PostRank 想法的 RSS 过滤应用。](http://www.igvita.com/2006/12/29/postrank-rss-filtering/)

PostRank 通过查看用户互动的多少来对 RSS 提要条目进行排名——用户互动越多，排名越高。每个 feed 的排名都是标准化的，因此，一个博客上的 10 条评论和 40 次挖掘可能会导致 PR 值为 10，而在一个更受欢迎的网站上，PR 值可能只有 3。PostRank 使用 diggs，del.icio.us saves，Twitter tweets，Google Reader shares，comments，以及 today noglia 书签和 Pownce 消息来确定排名。

今天，AideRSS】还宣布将 PostRank 剥离出来作为一个 API，可以在新的 [PostRank 网站](http://www.postrank.com/)上获得。开放 PostRank 算法作为一项服务做了两件事:这意味着任何人现在都可以创建“主题”版本的助手，混合和匹配来自不同来源的特定主题的文章——你现在可以对来自不止一个单一来源的帖子进行排名——以及为任何应用程序添加帖子过滤，这实际上可能会改善 PostRank 本身。

因为浏览量和文章点击量被计算到 PostRank 算法中(目前通过 AideRSS 小部件和 RSS 阅读器集成——如通过今天更新的[谷歌阅读器扩展](http://gr.aiderss.com/)，或通过 NewsGator Online，它使用 PostRank)，任何通过新 API 添加 PostRank 的 RSS 阅读器或应用程序理论上可能会将数据发送回 aiders，从而改进算法本身。

只要你能认同社交参与度的提高意味着内容质量的提高，那么 AideRSS 和 PostRank 就是非常有用的工具，可以降低任何重度媒体用户感受到的噪音。PostRank 真的很出色，因为每个帖子的排名都是根据该博客的平均水平进行标准化的——因此排名不会偏向流量大的网站。结果是，在 Digg 或 del.icio.us 上很少出现的网站仍然可以从 PostRank 的排名中获益。

## 分享这篇文章