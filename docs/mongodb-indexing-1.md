# MongoDB 索引，第 1 部分

> 原文：<https://www.sitepoint.com/mongodb-indexing-1/>

索引是使用 MongoDB 的更重要的概念之一。正确理解是至关重要的，因为索引可以通过减少要读取的完整文档的数量来显著提高性能和吞吐量，从而提高应用程序的性能。因为索引可能有点难以理解，所以这个由两部分组成的系列文章将仔细研究它们。

在本文中，我们将探讨以下五种类型的索引:

1.  默认标识索引
2.  辅助索引
3.  复合指数
4.  多键索引
5.  多关键字复合索引

还有一些其他类型需要讨论，但是我逻辑上把它们留在第 2 部分，以提供清晰的理解并避免任何混淆。

尽管可以在一个集合上定义多个索引，但一个查询在执行过程中只能使用一个索引。MongoDB 的查询优化器在运行时决定从可用选项中选择最佳索引。

本文假设您对 MongoDB 概念(如集合、文档等)有基本的了解。)并使用 PHP 执行基本查询(比如 find 和 insert)。如果没有，建议你看一下我们的初学者文章:[MongoDB 简介](https://www.sitepoint.com/introduction-to-mongodb/ "Introduction to MongoDB")和 [MongoDB 重温](https://www.sitepoint.com/mongodb-revisited/ "MongoDB Revisited")。

对于这个系列，我们假设我们有一个名为`posts`的集合，其中有 500 个文档，结构如下:

```
{
    "_id": ObjectId("5146bb52d852470060001f4"),
    "comments": {
        "0": "This is the first comment",
        "1": "This is the second comment"
    },
    "post_likes": 40,
    "post_tags": {
        "0": "MongoDB",
        "1": "Tutorial",
        "2": "Indexing"
    },
    "post_text": "Hello Readers!! This is my post text",
    "post_type": "private",
    "user_name": "Mark Anthony"
}
```

现在，让我们详细探讨各种类型的索引。

## 默认标识索引

默认情况下，MongoDB 为每个集合在`_id`字段上创建一个默认索引。每个文档都有一个惟一的`_id`字段作为主键，一个 12 字节的 ObjectID。当没有其他任何索引可用时，默认情况下，这用于所有类型的查询。

要查看集合的索引，请打开 MongoDB shell 并执行以下操作:

![MongoDB Indexing, Part 1](img/39c10a5ba4b9d1c7cfc1ca12eca4f3d5.png)

`getIndexes()`方法返回集合的所有索引。如您所见，我们有名为 *_id_* 的默认索引。`key`字段表示索引在`_id`字段上，值 1 表示升序。我们将在下一节学习订购。

## 辅助索引

对于我们想要在除了`_id`字段之外的字段上使用索引的情况，我们必须定义自定义索引。假设我们想要基于`user_name`字段搜索帖子。在这种情况下，我们将在集合的`user_name`字段上定义一个自定义索引。除了默认索引之外，这种自定义索引称为辅助索引。

为了演示索引对数据库的影响，我们先简要分析一下没有索引时的查询性能。为此，我们将执行一个查询来查找所有带有“Jim Alexandar”的帖子。

```
<?php
// query to find posts with user_name "Jim Alexandar"
$cursor = $collection->find(
    array("user_name" => "Jim Alexandar")
);
//  use explain() to get explanation of query indexes
var_dump($cursor->explain());
```

索引经常使用的一个重要方法是`explain()`，它返回与索引相关的信息。上述`explain()`的输出如下所示:

![MongoDB Indexing, Part 1](img/3370e1bb7b96b20b28c04579a41c9663.png)

值得关注的一些重要关键因素包括:

1.  **`cursor`**–表示查询中使用的索引。 *BasicCursor* 表示使用了 default _id 索引，MongoDB 必须搜索整个集合。接下来，我们将看到当我们应用索引时，将使用 *BtreeCursor* 而不是 *BasicCursor 或*。
2.  **`n`**–表示查询返回的文档数(本例中为一个文档)。
3.  **`nscannedObjects`**–表示查询搜索的文档数量(在本例中，搜索了集合中的所有 500 个文档)。如果集合中的文档数量非常大，这可能是一个开销很大的操作。
4.  **`nscanned`**–表示数据库操作过程中扫描的文档数量。

理想情况下，`n`应该等于或接近于`nscanned`，这意味着搜索了最少数量的文档。

现在，让我们执行相同的查询，但是使用辅助索引。要创建索引，请在 MongoDB shell 中执行以下命令:

![MongoDB Indexing, Part 1](img/c2bf5372cbe04b2cc8aa22fadebd1a5c.png)

我们使用`ensureIndex()`方法在`posts`集合中的`user_name field`上创建了一个索引。我相信您已经为该方法设置了 order 参数的值，它表示搜索的升序(1)或降序(-1)。为了更好地理解这一点，请注意每个文档都有一个时间戳字段。如果我们首先想要最近的帖子，我们将使用降序排列。对于最老的帖子，我们首先选择升序。

创建索引后，使用与之前相同的`find()`和`explain()`方法来执行和分析查询。的输出是:

![MongoDB Indexing, Part 1](img/20603a58354b7d7f2cc332ffa51f8876.png)

输出显示，该查询使用了一个名为*用户名 1* 的 *BtreeCursor* (我们在前面定义过了)，并且只扫描了一个文档，而不是在没有索引的情况下搜索了 500 个文档。

现在，请理解所有 MongoDB 索引在其算法中都使用 BTree 数据结构，BtreeCursor 是它的默认游标。对 BTreeCursor 的详细讨论超出了本文的范围，但这并不影响任何进一步的理解。

上面的比较表明了索引如何显著提高查询性能。

## 复合指数

有时一个查询会使用多个字段。在这种情况下，我们可以使用复合索引。考虑以下使用了`post_type`和`post_likes`字段的查询:

```
<?php
// query to find posts with type public and 100 likes
$cursor = $collection->find(
    array(
        "post_type" => "public",
        "post_likes" => 100
    ),
    array()
);
```

用`explain()`分析这个查询，给出下面的结果，它显示查询使用了`BasicCursor`，并且扫描了所有 500 个文档以检索一个文档。

![MongoDB Indexing, Part 1](img/24d344277676687ae23d3c15b56c2c43.png)

这是非常低效的，所以让我们应用一些索引。我们可以如下定义字段`post_type`和`post_likes`的复合索引:

![MongoDB Indexing, Part 1](img/099f7fbc008e21e0c0ecced73bd66b5b.png)

现在，分析查询会得到以下结果:

![MongoDB Indexing, Part 1](img/1573e87d1bfb4f6fe55059bc4ecc0745.png)

这里非常重要的一点是，在多个字段上定义的复合索引可以用来查询这些字段的子集。例如，假设有一个复合指数`{field1,field2,field3}`。该索引可用于查询:

*   `field1`
*   `field1, field2`
*   `field1, field2, field3`

因此，如果我们已经定义了索引`{field1,field2,field3}`，我们就不需要定义单独的`{field1}`和`{field1,field2}`索引。然而，如果我们在查询`field2`和`field2,field3`时需要这个复合索引，如果优化器没有选择想要的索引，我们可以使用`hint()`。

`hint()`方法可用于强制 MongoDB 使用我们指定的索引，并覆盖默认的选择和查询优化过程。您可以将索引中使用的字段名称指定为参数，如下所示:

```
<?php
// query to find posts with type public and 100 likes
// use hint() to force MongoDB to use the index we created
$cursor = $collection
    ->find(
        array(
            "post_type" => "public",
            "post_likes" => 100
        )
    )
    ->hint(
        array(
            "post_type" => 1,
            "post_likes" => 1
        )
    );
```

这确保了查询使用在`post_type`和`post_likes`字段上定义的复合索引。

## 多键索引

对数组字段进行索引时，称为多键索引。再次考虑我们的`post`文档；我们可以对`post_tags`应用多键索引。多键索引将索引数组的每个元素，因此在这种情况下，将为`post_tags`值创建单独的索引: *MongoDB* 、*教程*、*索引*，等等。

![MongoDB Indexing, Part 1](img/6dfba4688f810dfd5b67ab16da2d0235.png)

但是，必须非常有选择地使用数组字段的索引，因为索引每个值会消耗大量内存。

## 多关键字复合索引

我们可以创建一个多键复合索引，但是限制是索引中最多只能有一个字段是数组。因此，如果我们把`field1`作为一个字符串，把`[field2, field3]`作为一个数组，我们不能定义索引`{field2,field3}`，因为两个字段都是数组。

在下面的例子中，我们在`post_tags`和`user_name`字段上创建了一个索引:

![MongoDB Indexing, Part 1](img/03e030528e0615f85a670db8f1e6ce96.png)

## 索引限制和注意事项

重要的是要知道，索引不能用于使用正则表达式、求反运算符(即`$ne`、`$not`等)的查询。)、算术运算符(即`$mod`等。)、`$where`子句中的 JavaScript 表达式，以及其他一些情况。

索引操作也有自己的成本。每个索引都会占用空间，并且会导致集合上的每个插入、更新和删除操作产生额外的开销。您需要考虑每个集合的读写比率；索引有利于读取量大的集合，但可能不适用于写入量大的集合。

MongoDB 将索引保存在 RAM 中。确保总索引大小不超过 RAM 限制。如果是这样，一些索引将从 RAM 中删除，因此查询会变慢。此外，一个集合最多可以有 64 个索引。

## 摘要

这部分到此为止。总之，如果选择了合适的索引方法，索引对应用程序是非常有益的。[在下一部分](https://www.sitepoint.com/mongodb-indexing-2/ "MongoDB Indexing, Part 2")中，我们将看看如何在嵌入文档、子文档和排序中使用索引。敬请期待！

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章