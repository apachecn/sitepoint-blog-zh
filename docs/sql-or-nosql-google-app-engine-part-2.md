# SQL 或 NoSQL:谷歌应用引擎第二部分

> 原文：<https://www.sitepoint.com/sql-or-nosql-google-app-engine-part-2/>

在本系列的第一部分[中，我们研究了关系数据库，以及 NoSQL 与关系数据库相比有何不同。在这一部分中，我们将了解“](https://www.sitepoint.com/nosql-and-the-appengine-datastore/) [Google App Engine 数据存储库](https://developers.google.com/appengine/docs/python/datastore/overview)”，这是使用 Google App Engine 存储数据的选项之一。在其他选项中，第一个是[Google Cloud SQL](http://developers.google.com/cloud-sql/)——这是一个基于 MySQL 的云中的关系数据库。第二个选择是[谷歌云存储](https://cloud.google.com/products/cloud-storage)，这是一种存储服务，用于存储文件和大小高达 TB 的对象。

# 谷歌应用引擎数据存储

数据存储是一个无限可伸缩的、无模式的对象存储，完全由您支配。它处理数据的方式与 RDBMS 有很大不同，这就是为什么它为 RDBMS 的许多缺点提供了解决方案。与此同时，它也有自己的一套限制，以及建模和构建应用程序数据访问层的不同方式。让我们看看 Google App Engine 数据存储的一些特性。

## 无模式

数据存储不需要固定的数据模式。这是一个对象存储，你可以向它扔对象，它会存储它们。让我们联系一个实例来谈一谈。假设我们需要为一个应用程序存储名片信息。名字、姓氏和电子邮件地址是必填字段，还有可选字段，如手机号码、LinkedIn URL 和 Twitter 句柄。现在，当我们存储“Business”类型的实体时，我们当然需要确保提供必填字段，但是可选字段只有在可用时才能存储。因此，一个实体可能存储了 twitter 句柄，而另一个实体可能存储了 twitter 句柄和手机号码。让我们看一个对象的 JSON 表示来理解实体的样子:

#### 实体 1

{

```
"firstName" : "John",
"LastName" : "Taylor",
"Email" : "john@gmail.com",
"twitter_id" : "john_t"
}
```

#### 实体 2

{

```
"firstName" : "Tom",
"LastName" : "Rogers",
"Email" : "trogers@gmail.com",
"twitter_id" : "trogers",
"mobileNumber" : "567 555 1256"
}
```

让我们比较一下这在关系数据库中是如何建模的。将创建一个包含所有必填字段和所有可选字段的表。对于没有任何可选字段值的实体，将填充“null”。任何要添加到实体的新字段都意味着表结构的改变以及为所有实体填充该字段的值。

## 无限可扩展

您可以根据需要在数据存储中存储尽可能多的数据(暂且不考虑每 GB 成本)，您的任何查询都不会变慢。就性能而言，从 5000 万中提取 5 个实体与从 5000 万中提取 5 个实体没有什么不同。查询运行时间只会随着结果集的大小而增加，而不会随着要扫描的数据集的大小而增加。

如果您还记得我们在本系列第 1 部分中的讨论，您会很快问自己，当 RDBMS 不能自动分片时，数据存储是如何自动分片的。这一惊人的特性是由于数据在数据存储中的建模方式。关于单个实体的所有信息都保存在一个地方，而不是将属性分布在几个关系中。然后，所有实体按其唯一 id 排序。现在，一个简单的算法可以根据 id 分割实体列表，并将结果存储在不同的机器上。现在可以使用相同的算法将每个请求路由到适当的机器。

## 强一致性与最终一致性

数据存储将只保证读取和“祖先查询”的高度一致性，其他所有查询最终都将保持一致。因此，当一个实体最近更新时，用户可能看不到它的最新版本，这种可能性很小。对于许多用例来说，这不是什么大事(“天哪！这条推文现在才出现，两秒钟前才发的！”).有很多方法可以用性能换取强大的一致性，但我不会在这里深入讨论(以[这个](https://developers.google.com/appengine/docs/python/datastore/structuring_for_strong_consistency)作为起点)。

牺牲完美的一致性意味着没有必要立即同步所有的机器，RDBMS 必须等到每台机器完成数据更新。数据存储也不会检查引用完整性，因为这意味着必须从其他机器读取数据以确保有效的更新。能够水平扩展的另一个好处是，数据存储具有惊人的容错能力。数据中心中的许多机器可能会出现故障，而您的数据很可能仍然会毫不犹豫地得到服务。

## 为您的数据建模

在数据存储中，您必须根据您希望在应用程序中运行的查询来建模您的数据。因为一个实体的所有数据都必须保存在一个地方，所以数据存储必须为您需要的每个查询建立一个索引，然后才能处理这些查询。这意味着即席查询不是数据存储的选项。App Engine 提供了像 MapReduce 这样的(很棒的)工具来处理巨大的数据集和执行分析任务，但是这样的任务将比您可能习惯的优雅的 SQL 语句花费更多的时间来实现。

## 在 SQL 和 NoSQL 数据存储之间进行选择

总结到目前为止的讨论

> **“数据存储提供了一种保存‘哑’数据的方法，应用程序将这些数据转化为信息，RDBMS 提供了一种保存结构化数据的方法，应用程序可以直接使用这些数据”**

还记得我们在[第 1 部分](https://www.sitepoint.com/nosql-and-the-appengine-datastore/)中围绕 RDBMS 的讨论吗？RDBMS 需要关于数据结构的信息来提供独立于应用程序的服务，比如数据库级的数据聚合。数据存储不会这样做。它只关心构建索引所需的数据片段，实体的其余部分被视为一个密封的字节块。

让我们看一些场景和用例，并尝试评估 RDBMS 是更好的解决方案还是 NoSQL

1.  单台服务器能提供我们需要的性能吗？也许通过利用缓存？在这种情况下，RDBMS 是正确的选择。例如，看看 AppEngine 的纯关系产品[云](https://developers.google.com/cloud-sql/) [SQL](https://developers.google.com/cloud-sql/) 。
2.  您是否计划在同一数据集上开发一个多应用程序环境？根据您的容量，RDBMS 可能是您所需要的，因为它将数据库层与应用程序严格分离。
3.  您需要即席查询吗？就查询灵活性而言，SQL 是明显的赢家。
4.  你需要完美的一致性吗？尽管有方法可以实现数据存储区的高度一致性，但这并不是它的设计初衷。同样，RDBMS 是更好的选择。
5.  您期望每秒数百万次读写吗？数据存储提供了无限的自动扩展能力，您可以在 AppEngine 中使用它。
6.  您是否需要一种简单、可伸缩的方法来持久化具有可变属性的实体？尽管您必须自己处理一致性和数据聚合，但无模式数据存储应该是您所需要的。它被集成到 AppEngine 中，所以它是快速原型和变化实体的理想选择。

选择正确的数据库是一个庞大的主题，但是有了两个很好的选项(CloudSQL 和 Datastore)，您至少不必避开 App Engine。我希望这篇文章能让你更容易地做出决定，并祝你在任何你心目中的伟大应用中一切顺利。

## 进一步阅读

*   [SQL](http://www.youtube.com/watch?v=rRoy6I4gKWU&feature=g-vrec)
*   [伟大的](https://developers.google.com/appengine/docs/python/datastore/) [AppEngine](https://developers.google.com/appengine/docs/python/datastore/) [文档](https://developers.google.com/appengine/docs/python/datastore/):提供与数据存储相关的一切信息
*   [更多](http://highlyscalable.wordpress.com/2012/03/01/nosql-data-modeling-techniques/) [细节](http://highlyscalable.wordpress.com/2012/03/01/nosql-data-modeling-techniques/) [上](http://highlyscalable.wordpress.com/2012/03/01/nosql-data-modeling-techniques/)[NoSQL](http://highlyscalable.wordpress.com/2012/03/01/nosql-data-modeling-techniques/)[–](http://highlyscalable.wordpress.com/2012/03/01/nosql-data-modeling-techniques/)[数据](http://highlyscalable.wordpress.com/2012/03/01/nosql-data-modeling-techniques/) [造型](http://highlyscalable.wordpress.com/2012/03/01/nosql-data-modeling-techniques/)
*   [谷歌应用引擎:数据库策略](https://www.sitepoint.com/google-app-engine-database-strategies/)

# 最后的话

为您的应用选择正确的数据存储以及了解 NoSQL，这两者都是重要的话题。我们在这两部分系列中所涵盖的只是冰山一角。任何遗漏提及这一领域的主要或新兴参与者都纯属无意。我们在 ***CloudSpring*** 希望在不久的将来覆盖更多这个伟大的主题，继续关注。

## 分享这篇文章