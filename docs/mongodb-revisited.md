# MongoDB 重访

> 原文：<https://www.sitepoint.com/mongodb-revisited/>

在我以前的文章[MongoDB 简介](https://www.sitepoint.com/introduction-to-mongodb/)中，我讨论了安装 Mongo，它的 PHP 扩展，以及如何执行简单的插入和查找操作。当然，还有很多很多比我提到的更多的特性，所以我想写另一篇文章向你展示其中的一些。在本文中，您将了解游标、附加查询过滤器以及在数组和嵌入式文档上运行查询。

## 光标

首先让我们谈谈 MongoDB 中的游标。在上一篇文章中，您看到了一个查找操作的示例，如下所示，它选择在集合中找到的与传递的条件匹配的所有文档:

```
<?php
$cursor = $collection->find(array("author" => "shreef"));
```

我当时只简单地提到过,`find()`方法返回一个`MongoCursor`实例(不是找到的实际文档的列表)。在从游标调用结果之前，不会从 MongoDB 请求任何东西。

Mongo 的光标有两个生命阶段。第一个阶段是“预查询阶段”此时，光标还没有尝试执行查询，您有机会添加更多的细节和约束。例如，如果您想要指定要返回的文档的最大数量，您可以使用游标的`limit()`方法。

```
<?php
$cursor = $collection->find(array("author" => "shreef"));
$cursor = $cursor->limit(5);
```

您经常会看到方法调用像这样链接在一起:

```
<?php
$cursor = $collection->find(array("author" => "shreef"))->limit(5);
```

一旦您尝试通过直接调用`next()`方法或通过迭代游标来从游标读取结果，游标实际上执行查询并移动到它的第二个阶段，即“查询后阶段”:

```
<?php
foreach ($cursor as $doc) {
    // do something
}
```

另外值得一提的是，并不是所有符合查询条件的文档都会同时返回。如果结果的总大小很大，您可能不希望将所有数据都加载到内存中。MonogDB 的返回结果限制为 4-16MB。当您遍历完第一批结果后，光标将透明地检索下一批文档。所有这些都是在后台发生的，所以你在编写代码时不必担心，但是值得一提的是，这样你就知道实际发生了什么。

默认情况下，MongoDB 会让光标在服务器上保持活动状态，直到您阅读完分配给它的所有结果，或者自它创建以来已经过了 10 分钟。您可以使用`MongoCursor`的`timeout()`方法来增加或减少光标的寿命(以毫秒为单位)。您也可以将-1 传递给`timeout()`来禁用超时行为，但是这样您就必须迭代所有的结果，否则光标将永远存在并耗尽服务器的资源。

## 查询运算符

只看了几个例子，MongoDB 中的查询就很容易掌握。您已经看到，可以将查询作为包含要匹配的值的数组发送，并且可以使用 MongoDB 支持的特殊的`$`-操作符来微调匹配标准。现在我想向您展示用于逻辑比较的操作符，但是首先有一个重要的注意事项:永远记住对`$`-操作符使用单引号，或者对它们进行转义。你大概能猜到原因。

### $lt，$lte，$gt，$gte

`$lt`、`$lte`、`$gt`、`$gte`操作员相当于`<`、`<=`、`>`、`>=`。要查找浏览次数大于或等于 50，000 的 *blog* 集合中的所有文档，可以构建如下查询:

```
<?php
$collection->find(array("views" => array('$gte' => 50000)));
```

`views`是应该包含大于或等于 50，000 的值的字段的名称。

### $and、$or 和$nor

有时您会希望确保一个值满足多个条件，或者至少满足几个条件中的一个。`$and`和`$or`操作符用于提供布尔条件，和您已经习惯的一样。如果您想要查找由“Shreef”或“Timothy”撰写的浏览量大于或等于 50，000 的所有博客帖子，您可以编写如下查询:

```
<?php
$collection->find(array(
    "views" => array('$gte' => 50000),
    "$or" => array(
        array("author" => "Shreef"),
        array("author" => "Timothy"))));
```

类似地使用`$nor`操作符，但是确保不满足任何条件。

### $in 和$nin(不在)

当您想要传递一个值列表，其中一个值应该与您正在检查的字段相匹配时,`$in`操作符非常有用。`$nin`操作符执行相反的操作，检查字段是否与任何值都不匹配。当你做一个简单的查询时，这通常比使用前面提到的布尔操作符更具可读性。

```
<?php
$collection->find(array(
    "authors" => array('$in' => array("Shreef", "Timothy"))));
```

## 对数组的查询

前面的例子演示了创建和查询包含单个值的字段的能力，但是 MongoDB 也支持数组值。例如，要提供组织博客文章的标签列表，可以简单地将它们指定为一个数组。

```
<?php
$collection->insert(array(
    "title"  => "More Mongo",
    "author" => "Shreef",
    "tags"   => array("php", "mongodb")));
```

现在，要查找带有“php”标签的文档，您可以执行以下操作:

```
<?php
$collection->find(array("tags" => "php"));
```

查询数组与查询具有单个值的字段相同，任何将“php”列为其标签值之一的数组都将匹配。您还可以对数组使用所有前面提到的`$`-操作符，加上`$all`操作符，它允许您检查数组是否包含所有传递的值。

```
<?php
$collection->find(array(
    "tags" => array('$all' => array("php", "mongodb")));
```

现在有了`$all`，这个查询将只匹配带有标签“php”和“mongodb”的文档。只有其中一个值不足以匹配。

## 对嵌入文档的查询

如果您将 MongoDB 用于任何严肃的应用程序，嵌入文档是您可能会经常处理的事情之一。例如，将一篇文章的所有评论嵌入到同一个博客文章文档中可能是合理的。让我们假设 Sophia 添加了一个新的注释；您可以通过将她的评论推送到 comments 数组来更新您的博客文档，如下所示:

```
<?php
$postId = "xxx";
$collection->update(
    array("_id" => new Mongo($postId)),
    array('$push' => array(
        "comments" => array(
            "author"  => "Sophia",
            "content" => "hi..."))));
```

正如你所看到的，我使用了一个名为`$push`的操作符，从它的名字你就可以猜到，它会把一个新的项目推到一个数组上。就性能而言，这种方法比从数据库加载整个文档，修改它，然后将其写回数据库要好。

现在，当您想要检索 Sophia 所做的所有评论时，您可以像这样查询它们:

```
<?php
$collection->find(array("comments.author" => "Sophia"));
```

因为 MongoDB 支持点符号，所以您可以像这样编写字段名。点符号允许您编写字段的名称，就像它们是对象属性一样；通过编写“comments.author ”,我可以引用存在于`comments`对象中的`author`字段的值。

## sort()和 skip()方法

我已经提到过`limit()`方法，它接受查询时返回的文档数。MongoCursor 还提供了其他一些你肯定会觉得有用的方法，比如`sort()`和`skip()`。

`sort()`方法类似于 SQL 中的`ORDER BY`子句——您提供许多用于对结果进行排序的字段，并指定每个字段的排序方式。1 代表升序，-1 代表降序。

```
<?php
$collection
    ->find()
    ->sort(array("createdAt" => -1 , "author" => 1));
```

这将首先按创建日期降序排列匹配的文档，然后按作者升序排列。

`skip()`方法传递所提供的匹配查询的文档数量。例如:

```
<?php
$collection
    ->find(array("author" => "Shreef"))
    ->sort(array("createdAt" => -1))
    ->limit(5)
    ->skip(10);
```

该查询搜索由 Shreef 创作的所有文档，按照文档的创建时间排序，然后跳过原本要返回的前 10 个文档，而只返回后面的 5 个文档。

对文档进行排序引出了非常重要的一点:MongoDB 中的索引与 MySQL 等 RDBMS 中的索引一样重要。

## 指数

在没有索引的情况下运行查询在任何数据库中都没有多大意义。您必须在查询中引用的字段上创建索引，包括那些用于排序的字段。您可以使用`ensureIndex()`方法在 MongoDB 中创建索引。该方法接受一个字段列表作为第一个参数，一个可选的选项列表作为第二个参数。这里有一个例子:

```
<?php
$collection->ensureIndex(
    array("author" => 1), 
    array("name" => "idx_author"));
```

这将使用`author`字段创建一个升序索引，我可以选择将该索引命名为“idx_author”。您可以创建一个包含多个字段的索引，方法是将字段名称作为键添加到第一个参数中传递的数组中，并将它们的值设置为 1 或-1。使用 1 意味着您希望字段的索引是升序，而使用-1 意味着您希望它是降序。

您可能需要的其他选项是`unique`和`dropDups`选项。您可以创建一个索引，通过将`unique`设置为“真”来确保一个字段在集合中的所有文档中是唯一的。如果您将`dropDups`设置为 true，MongoDB 将丢弃除一个副本之外的所有副本。

```
<?php
$collection->ensureIndex(
    array("title" => 1), 
    array("unique" => true, "dropDups" => true));
```

当执行查询时，MongoDB 试图猜测使用的最佳索引，但有时它无法选择正确的索引。您使用`hint()`方法告诉它要使用的字段。

```
<?php
$collection
    ->find(array("author" => "shreef"))
    ->hint(array("author" => 1));
```

在这个例子中，我告诉 Mongo 使用由按升序排序的`author`字段组成的索引。你*必须*以同样的顺序通过你用来创建索引的同样的标准。如果没有由相同顺序的传递字段组成的索引，将会引发异常。

您可能想知道为什么不能只使用索引的名称。实际上，这是 Mongo 支持的，但看起来它并没有在 PHP API 中实现。PHP API 中的`hint()`方法只接受数组。让我们希望这将很快得到解决！

## 摘要

MongoDB 每个版本都在变得越来越好，还有很多我在这里没有提到的特性。PHP 手册为您提供了一些信息，但是最好阅读 MongoDB 文档来了解最新和最棒的特性。这篇文章充满了您可以尝试的东西，比如使用`$`-操作符、查询嵌入的文档以及排序和跳过结果。请随意修改它们，并在评论中留下你的问题和发现。

<small>图片 via[Pakhnyushcha](http://www.shutterstock.com/gallery-102849p1.html)/[Shutterstock](http://shutterstock.com)</small>

## 分享这篇文章