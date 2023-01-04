# Rails 使用 Redis 简介

> 原文：<https://www.sitepoint.com/introduction-to-using-redis-with-rails/>

![](img/94839ba6ebdec48938490656b28799c8.png "redis-300dpi") [Redis](http://redis.io) 是一个与众不同的键值存储，比如 [memcached](http://memcached.org/) ，因为它内置了对列表、集合和散列等数据结构的支持，并且可以将数据持久化到磁盘上。因此，无论是作为缓存存储还是作为成熟的 NoSQL 数据存储，它都非常有用。在本文中，我们将通过一个基本的使用示例来学习如何在 Rails 应用程序中使用 Redis。

## 一个基本例子

假设我们用 Rails 编写了一个简单的博客平台。我们使用 MySQL 作为我们的主要数据库，在那里我们存储所有的帖子内容、评论和用户帐户。假设它在 myrailsblog.com 举办。多亏了我们改变游戏规则的社交媒体策略，我们的帖子直接获得了固定流量。互联网上到处都有这些帖子的直接链接。

我们并不疯狂的一件事是，一篇文章的 URL 看起来像这样:http://myrailsblog.com/321.除了它有点难看并暴露内部 id 的事实之外，如果我们为文章 URL 实现 [slugs](http://en.wikipedia.org/wiki/Slug_%28web_publishing%29) ，我们可以获得更好的搜索引擎排名。我们希望 URL 看起来像这样，而不是:http://myrailsblog.com/using-redis-with-rails.

所以我们决定从现在开始，帖子将会有标题自动生成的 slugs。我们将通过 slug 而不是 id 来查找帖子。当一个新的帖子被创建时，它将生成一个 slug，存储在我们的 posts 表的一个新列中。然而，我们的平台允许编辑文章标题，此外，我们希望作者能够定制 slug，以防自动生成的标题由于任何原因而变得不理想。这意味着帖子的 URL 在发布后可以更改。当有人使用旧的网址时会发生什么？我们的平台应该从老鼻涕虫那里找到正确的帖子，而不是笨拙地吐回一个 404。这意味着所有的老鼻涕虫都需要继续引用帖子。

作为 Rails 开发人员，我们通过一个帖子的 slug 来查找该帖子的自然方法是实现类似这样的东西: