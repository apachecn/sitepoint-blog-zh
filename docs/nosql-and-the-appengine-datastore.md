# SQL 或 NoSQL:谷歌应用引擎——第 1 部分

> 原文：<https://www.sitepoint.com/nosql-and-the-appengine-datastore/>

NoSQL 是一个热门话题，从谷歌到脸书，几乎每个人都对此有所了解。在这篇由两部分组成的文章中，我们试图回答 Sql 或 NoSQL 的两难问题。第一部分将解释每个普通的老 Sql 和新的闪亮的 NoSQL 的好处和机制。在第二部分中，我们将特别关注 Google App Engine 数据存储，并尝试回答对于给定的业务问题，它是否是更好的选择

## 传统的 RDBMS

可以肯定地说，大多数现实世界的应用程序依赖于某种类型的 RDBMS 来存储和检索它们的数据。您的应用程序可能有很多理由不选择 NoSQL，而是坚持使用 RDBMS。让我们来看看 RDBMS 擅长的关键点:

*   查询灵活性
*   维护整个数据集的一致性
*   管理交易
*   分离关注点/处理其下不断发展的应用程序

为了维护一致的数据集，RDBMS 实施完整性约束。数据集上的每个操作都必须发生在具有 [ACID](http://en.wikipedia.org/wiki/ACID) 属性的事务中。这保证了事务内部发生的任何事情都不会破坏数据的一致性。

无论您的应用程序可能面临什么样的新挑战，只要您的数据模型保持不变，RDBMS 都会愉快地处理您的查询。数据库和应用程序之间的这种分离对于访问同一个数据库的多个应用程序来说是非常理想的。数据中的结构(关系)由 RDBMS *处理。*这当然需要完全了解您的数据是如何连接的(由您创建的关系提供)，以及严格遵守关系模型的 [12 原则。这些原则为定义明确的数据的简单和高度结构化的存储提供了坚实的理论基础。](http://users.atw.hu/sqlnut/sqlnut2-chp-1-sect-1.html)

## 原力的扰动

不幸的是，所有这些出色的特性都有一些缺点，下面就举几个例子:

*   不太支持具有可变或复杂属性的实体
*   对分层或图形数据的支持较弱
*   没有简单的扩展方法

第一个和第二个都是功能缺陷:RDBMS 需要结构，它只能构造逻辑上符合关系的内容。例如，您可以将复杂的属性存储为二进制字符串，但是 RDBMS 不能有效地对它们进行操作。可变属性不适合静态模式，每一行都必须包含每个属性。模式更新很慢，并且需要预定的停机时间。从“关系的观点”来看，这绝对有意义。请记住，RDBMS 处理需要了解数据结构的任务。每当您打算更改该结构时(例如，通过添加或删除属性)，都必须“通知”它。

关系数据库不适合层次数据或图形数据。这些类型的建模需要大量的一对多和多对多关系，这在关系数据库中无法有效地建模。形象地说，您正在尝试将一棵树或一个网格放入本质上是一个表的东西中。

第三个弱点与性能相关。RDBMS 不容易横向扩展，这对于当今数百万用户的“web 级”应用程序来说是一个巨大的问题。

在非常基础的层面上，数据库通过分片来扩展。如果一台机器无法再处理该卷，数据集将被分割成子集和碎片，然后存储在多台机器上。主服务器处理负载平衡并将每个请求路由到适当的机器(“从”)。这种*主从*配置并不是唯一的一种可能，但为了简洁起见，我不会讨论其他模型。

这就是问题的关键，RDBMS 不能自动将数据分成子集，因为一个应用程序实体的信息(通常)存储在几个数据库关系中。如果您不熟悉关系数据建模，[规范化](http://en.wikipedia.org/wiki/Database_normalization)是一个很好的起点。是的，一些像 Twitter 这样的大型 web 应用程序运行在 RDBMS 上，但是开发人员必须实现一个特定于应用程序的分片层，这不是由 RDBMS 自动处理的。

## 一个新品种:NoSQL

NoSQL，“不仅是 SQL”是一个通用术语，用于描述各种数据库，其中许多数据库并不陌生。但是他们最近经历了一次复兴。NoSQL 数据库旨在通过将结构化工作放回到应用程序程序员——您——的手中来解决 RDBMS 的性能问题。幸运的是，应用程序并不总是需要数据库级别的聚合和结构，这就是为什么这些应用程序可以安全地从 NoSQL 数据库提供的巨大性能改进中受益。我们的目标是找出你的应用程序是否是其中之一，但首先让我介绍几个 NoSQL 数据库(大致按复杂程度排序):

*   (有序)键值存储*(阿帕奇卡珊德拉，迪纳摩，伏地魔项目)*
*   对象存储 *(AppEngine 数据存储)*
*   文档存储 *(CouchDB)*
*   树和图形数据库 *(Neo4J，Twitter 的 FlockDB)*

这些都解决了一些问题，当然也带来了一些自己的问题。

我们现在对关系数据库和 NoSQL 数据库的机制和约束有了相当扎实的理解。以此为背景，我们将在本文的下一部分看看谷歌应用引擎数据存储。继续看。

## 分享这篇文章