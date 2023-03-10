# 亚马逊发布 DynamoDB:弹性计算的 NoSQL

> 原文：<https://www.sitepoint.com/amazon-releases-dynamodb-fast-scaleable-nosql-database-for-elastic-computing/>

亚马逊首席技术官沃纳·威格尔在他的博客上发表了一篇文章，详细介绍了 DynamoDB 的发布，这是一个 NoSQL 数据库解决方案，从头到尾都是在云中构建的。

这是令人兴奋的，原因有很多，尤其是因为它提供了一个坚实的 NoSQL 平台，开发人员可以使用它进行弹性计算。DynamoDB 的出现意味着更多的开发人员可以开始在云中构建可扩展的应用程序，而不必担心单独的数据库扩展和性能。Amazon 已经与 NoSQL 和非关系数据库合作了很长一段时间，这次发布代表着开发者在 Amazon 上向前迈出了一大步。

根据沃格尔斯的说法，DynamoDB 是“一个完全托管的 NoSQL 数据库服务，可以在任何规模下提供快速性能。”。因此，现在我们不仅有了一个可行的、完全受支持的 NoSQL 平台，数据库层也将自动扩展和管理，因此数据端的延迟和可用性(传统上是任何应用程序性能的主要瓶颈之一)从一开始就得到解决。

点击查看原文[。](http://www.allthingsdistributed.com/2012/01/amazon-dynamodb.html "All Things Distributed Blog")

如果你打算使用 DynamoDB，或者已经在 Amazon 上使用过其他数据库解决方案，请在评论中与我们分享你的经验。

# DynamoDB 与 SimpleDB

*   DynamoDB 通过自动分发数据来节省您的时间，因此您可以扩展，而不是强迫您提供 10GB 的 SimpleDB 域，然后手动对数据进行分区。
*   DynamoDB 只索引主键，而 SimpleDB 索引所有列。你获得了速度，但失去了一些能力。
*   DynamoDB 让固态硬盘成为一种选择
*   定价不一样。DynamoDB 的定价基于每秒允许的读/写次数。SimpleDB 是根据在您的服务器上执行查询的计算开销来定价的——以机器小时来衡量。简单地说，对于大多数用例来说，DynamoDB 可能更便宜
*   DynamoDB 有 SLA 保证，SimpleDB 没有，因为磁盘 I/O 和复杂的请求类型

## 分享这篇文章