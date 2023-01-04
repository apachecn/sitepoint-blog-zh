# 使用 MongoDB 和 Analog 记录错误

> 原文：<https://www.sitepoint.com/error-logging-with-mongodb-and-analog/>

随着对文档数据库的大肆宣传，特别是围绕 MongoDB，我们收到了很多关于人们应该如何将他们的应用程序转移到使用它的问题。建议通常是相同的——尤其是对于现有的应用程序——一步一步来。也就是说，我们想展示一下我们认为是一个很好的起点:使用 MongoDB 进行错误日志记录。

由于许多原因，MongoDB 非常适合日志记录(当然还有其他事情)。首先，它写数据非常非常快。它可以异步执行写操作；你的应用程序不会挂起，因为你的日志程序被阻止。这使您可以集中日志，从而更容易查询日志来发现问题。此外，它的查询界面易于使用，非常灵活。您可以查询任何字段名，或者使用 map/reduce 或 MongoDB 2.2 即将推出的聚合框架执行聚合函数。

本文将展示如何使用现有代码向代码中添加日志库，并将错误记录到 MongoDB 中。您还将看到如何从命令行查询 MongoDB，以及如何过滤这些查询以找到您感兴趣的信息。

## 设置记录器

在为 PHP 编写 MongoDB 日志类之前，我们快速浏览了一下，看看还有什么已经存在，并在 GitHub 上找到了一个很好的微型日志工具，名为 [Analog](https://github.com/jbroadway/analog) 。为了不重复发明轮子，我们将在我们的例子中使用它。

我们真正喜欢 Analog 的是它代码的简单性和你可以记录的东西的数量！它被设计成可扩展的，所以你应该能够容易地在你自己的项目中构建任何你可能需要的东西。

logger 是相当独立的，所以要使它的功能可用，您需要做的就是包含它的主文件`Analog.php`。这将负责自动加载和名称空间注册，以便找到它的依赖项。由于它使用了`spl_autoload_register()`，它将很高兴地与你已有的任何其他自动加载装置共存。

要开始使用 logger，您需要初始化想要使用的日志处理程序，然后将其传递给主日志类。项目中包含了一些例子，可以很容易地看出特定平台需要什么。对于 MongoDB，我们有以下内容:

```
<?php
Analog::handler(AnalogHandlerMongo::init(
	"localhost:27017",
	"testing",
	"log"));
```

这里我们要做的就是将 Analog 指向我们的 MongoDB 安装(我们的与 web 服务器在同一台机器上，使用默认端口)，告诉它使用`testing`数据库，并写入`log`集合。有了这个脚本的顶部，可能还有其他各种引导任务，我们就可以开始了。

## 记录错误

此时，我们可以在应用程序中任何需要的地方使用日志功能。要记录错误，只需执行以下操作:

```
<?php
Analog::log("Oh noes! Something went wrong!");
```

要查看数据库中有什么，请打开 mongo shell。

```
lorna@taygete:~$ mongo
type "help" for help
> use testing
> db.log.find();
{ "_id" : ObjectId("4f268e9dd8562fc817000000"), "machine" : "localhost", "date" : "2012-02-29 11:11:16", "level" : 3, "message" : "Oh noes! Something went wrong!" }
```

如您所见，这为我们提供了错误消息、严重性、创建错误的日期和时间，以及错误来自哪台计算机。如果设置，机器标识符来自`$_SERVER["SERVER_ADDR"]`，否则使用“localhost”。

## 日志记录级别

模拟库带有一组很好的常数，可以用来设置每个误差的等级。这是课堂上给他们看的一个片段:

```
<?php
...
class Analog {
    /**
     * List of severity levels.
     */
    const URGENT   = 0; // It's an emergency
    const ALERT    = 1; // Immediate action required
    const CRITICAL = 2; // Critical conditions
    const ERROR    = 3; // An error occurred
    const WARNING  = 4; // Something unexpected happening
    const NOTICE   = 5; // Something worth noting
    const INFO     = 6; // Information, not an error
    const DEBUG    = 7; // Debugging messages
...
```

默认值为 3 级，表示错误。要记录任何其他级别的错误，将所需级别作为第二个参数传递给`log()`方法:

```
<?php
Analog::log("FYI, a log entry", Analog::INFO);
```

现在查看数据库，我们可以知道我们的日志消息集合将如何增长。

```
> db.log.find();
{ "_id" : ObjectId("4f268e9dd8562fc817000000"), "machine" : "localhost", "date" : "2012-02-29 11:11:16", "level" : 3, "message" : "Oh noes! Something went wrong!" }
{ "_id" : ObjectId("4f268e9dd8562fc817000001"), "machine" : "localhost", "date" : "2012-02-29 12:35:41", "level" : 6, "message" : "FYI, a log entry" }
```

虽然(和所有日志一样)在实际应用中，我们将建立一个大型数据集，但使用数据库意味着我们可以轻松地生成摘要信息或过滤数据，只找到重要的条目。

## 过滤和总结 MongoDB 日志

使用数据库存储意味着搜索结果的能力，MongoDB 被设计为即使对于大型数据集也易于开发人员使用。大量平面文件日志的时代已经结束了！我们可以很容易地过滤数据，只显示我们感兴趣的内容。

```
> db.log.find({level: 3});
{ "_id" : ObjectId("4f268e9dd8562fc817000000"), "machine" : "localhost", "date" : "2012-02-29 11:11:16", "level" : 3, "message" : "Oh noes! Something went wrong!" }
```

由于我们有许多不同级别的日志记录，所以数据库中还有一些更高级别的条目。为了显示所有错误严重性及以上的内容(一个较低的错误级别常数)，我们可以使用操作符`$lte`进行查询:

```
> db.log.find({level: {$lte: 3}});
{ "_id" : ObjectId("4f268e9dd8562fc817000000"), "machine" : "localhost", "date" : "2012-02-29 11:11:16", "level" : 3, "message" : "Oh noes! Something went wrong!" }
{ "_id" : ObjectId("4f26aaafd8562fcb27000009"), "machine" : "localhost", "date" : "2012-02-29 13:01:04", "level" : 0, "message" : "To the lifeboats!" }
```

我们还可以查找日期范围，例如，使用`$gt`比较从我的数据库中提取最近的几个日志条目:

```
> db.log.find({date: {$gt: "2012-02-29 14:35:30"}});
{ "_id" : ObjectId("4f26aaafd8562fcb2700000a"), "machine" : "localhost", "date" : "2012-02-29 14:35:31", "level" : 4, "message" : "Empty variable $a on line 127" }
{ "_id" : ObjectId("4f26aaafd8562fcb2700000b"), "machine" : "localhost", "date" : "2012-02-29 14:35:35", "level" : 4, "message" : "Empty variable $a on line 93" }
{ "_id" : ObjectId("4f26aaafd8562fcb2700000c"), "machine" : "localhost", "date" : "2012-02-29 14:35:40", "level" : 4, "message" : "Empty variable $a on line 277" }
{ "_id" : ObjectId("4f26aaafd8562fcb2700000d"), "machine" : "localhost", "date" : "2012-02-29 14:35:45", "level" : 6, "message" : "FYI, it seems to be snowing" }
```

如果您经常查询特定字段的数据，可以通过添加索引来加快查询速度。例如，如果您经常查询`level`和日期，您可以创建一个复合索引:

```
> db.log.ensureIndex({ date : -1, level : 1 } );
```

如果还没有索引，上面的行将创建一个索引。然而，这里有几件事值得注意。首先，我们将`date`放在第一位，因为它将具有最大的变化，因此该指数将发挥最大的作用。我们还创建了`date`作为反向索引，因为我们通常想要查询最近的条目。其次，我们增加了`level`作为索引的一部分。这个复合索引将使对日期的任何查询以及对`date`和`level`的任何查询更加高效。它不能仅用于查询`level`而不能用于查询`date`。

有时您会希望在日志中寻找总体趋势，因此您会对特定错误发生的次数进行分组。在本例中，我们按照错误级别对错误集进行了分组，以显示每种错误集的数量:

```
> db.log.group({key: {level: true}, initial: {count: 0}, reduce: function (obj, prev){prev.count++}});
[
    {
        "level" : 3,
        "count" : 1
    },
    {
        "level" : 6,
        "count" : 4
    },
    {
        "level" : 4,
        "count" : 8
    },
    {
        "level" : 0,
        "count" : 1
    }
]
```

您可以使用`group()`功能计算每天的错误数，或者根据您的选择计算特定机器的错误数。但是一定要小心，因为这种方法只对小数据集有用。如果您有超过 10，000 个结果，那么您需要使用 map/reduce 来生成结果。

## 摘要

在考虑将 MongoDB 添加到现有应用程序时，从小处着手是有意义的，日志记录是理想的选择。不同类型的错误可以包含不同类型的信息，您还可以将当前对象或任何其他信息保存到 MongoDB，因为它有一个灵活的模式。任何新技术都可能有一点学习曲线，但是希望命令行示例能够帮助您更加接近您正在从事的工作。在新事物中只实现一个功能是一个很好的尝试方式——希望你和我们一样喜欢 MongoDB！

图片 via[mama-art](http://www.shutterstock.com/gallery-252451p1.html)/[Shutterstock](http://www.shutterstock.com)

## 分享这篇文章