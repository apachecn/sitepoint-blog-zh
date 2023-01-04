# MongoDB 简介

> 原文：<https://www.sitepoint.com/introduction-to-mongodb/>

作为一名 PHP 开发人员，您可能习惯于看到使用 MySQL 或其他关系数据库管理系统(RDBMS)的应用程序和文章。但是在过去的几年中，一种新的数据库在软件开发社区中得到了采用。这种新型数据库专注于文档对象，而不是严格定义的记录和关系，并被昵称为“NoSQL”

NoSQL 概念有很多实现，但是最著名和广泛使用的 NoSQL 数据库之一是 MongoDB。我认为它是目前可用的最有趣的 NoSQL 数据库之一，并且被许多人认为是最容易使用的数据库之一(这帮助它获得了广泛的采用)。

在本文中，我将向您介绍使用 MongoDB 的 NoSQL。您将学习如何为 PHP 安装 MongoDB 扩展，以及如何添加、更新和检索文档对象。如果您习惯于使用像 MySQL 或 PostgreSQL 这样的 RDBMSs，您会发现使用 MongoDB 的一些概念有点奇怪，但是您很快就会喜欢上 MongoDB 带给您的灵活性和强大功能！

## 关于 MongoDB

MongoDB 是一个面向文档的数据库，每个文档都有自己的结构。与 RDBMS 中每个记录必须符合其表的结构不同，MongoDB 中的每个文档可以有不同的结构；在将文档保存到数据库中之前，不必为文档定义模式。

MongoDB 将文档对象分组到*集合*中。您可以将集合想象成一个表，就像您在 RDBMS 中创建的表一样，但是正如我前面所说的，不同之处在于，它们不会强迫您在存储某个东西之前定义一个模式。

使用 MongoDB，您可以将一个文档嵌入到另一个文档中，这对于存在一对一关系的情况非常有用。在典型的 RDBMS 中，您需要创建两个表，并用外键将它们链接在一起，以达到相同的结果。MongoDB 不支持连接，有些人认为这是一个缺点。但是如果你正确地组织你的数据，你会发现你不需要连接，这是一个优点，因为你会从非常高的性能中受益。

值得一提的是，MongoDB 和 NoSQL 的目标并不是消灭 RDBMS。RDBMSs 对于大多数开发领域的需求来说仍然是一个非常好的解决方案，但是它们也有弱点，最明显的是需要为你的数据定义一个严格的模式，这是 NoSQL 试图解决的一个问题。

## 装置

MongoDB 很容易设置。你可以从其网站下载最新版本[的压缩文件，并在你的服务器上解压。然后，为数据库创建一个目录，并使用数据库目录的路径运行`mongod`二进制文件。](http://www.mongodb.org/downloads "Downloads - MongoDB")

```
shreef@indigo:~$ tar zxf mongodb-<version>.tgz
shreef@indigo:~$ mv mongodb-<version> mongodb
shreef@indigo:~$ mkdir mongodb/data
shreef@indigo:~$ mongodb/bin/mongod --dbpath mongodb/data &
```

然后，您需要安装来自 PECL 的 PHP 的 [MongoDB 扩展](http://www.php.net/mongo "PHP: Mongo - Manual")。

```
shreef@indigo:~$ sudo pecl install mongo
```

在您的`php.ini`中启用扩展，并在必要时重启 Apache。

```
extension=mongo.so
```

## 使用 MongoDB

使用 PHP 的 MongoDB 扩展很容易。我将向您展示如何执行最常用的基本操作，并强调一些您可能会遇到的常见陷阱。

### 连接

您需要做的第一件事是建立到 MongoDB 服务器的连接，这就像创建一个新的`Mongo`类实例一样简单。默认情况下，新的`Mongo`对象将尝试使用端口 27017 连接到本地主机上运行的 MongoDB 服务器。您可以通过在实例化对象时传递连接字符串来更改这一点。

```
<?php
// connects to localhost on port 27017 by default
$mongo = new Mongo();

// connects to 192.168.25.190 on port 50100
$mongo = new Mongo("mongodb://192.168.25.190:50100");
```

接下来，您需要选择将要使用的数据库的名称。如果你想访问名为*博客*的数据库，那么只要选择它，就好像它是`$mongo`的一个属性。

```
<?php
$db = $mongo->blog;
```

`$db`现在拥有一个代表博客数据库的`MongoDB`对象。

选择要保存对象的集合与选择数据库非常相似；通过将集合 *posts* 作为`MongoDB`对象的属性来访问，选择该集合。

```
<?php
$collection = $db->posts;
```

`$collection`现在拥有一个代表 posts 集合的`MongoCollection`对象。

如果不存在与您提供的名称相同的数据库或集合，MongoDB 将在您插入第一个文档对象后创建一个同名的新数据库或集合。或者，您可以使用`createCollection()`方法强制 MongoDB 更早地创建集合。

```
<?php
$collection = $db->createCollection("posts");
```

### 插入文档

要在 posts 集合中插入一个新对象，可以使用`MongoCollection`对象的`insert()`方法，传入一个要保存的数据数组。

```
<?php
$document = array(
    "title" => "cat with a hat",
    "content" => "once upon a time a cat with a hat ...");
$collection->insert($document);
```

你可以看到这里的`$document`只是一个简单的关联数组。在您调用`insert()`之后，数组被修改为新的键 *_id* 和一个添加了`MongoId`对象实例的值。

```
Array
(
    [title] => cat with a hat
    [content] => once upon a time a cat with a hat ...
    [_id] => MongoId Object
        (
            [$id] => 4ea2213af7ede43c53000000
        )
)
```

*_id* 是文档的主键，对于集合中的每个文档对象必须是唯一的。一般来说，让 MongoDB 分配密钥是个好主意，因为这样可以确保不会有任何冲突。

如果您试图用`insert()`再次使用同一个数组，您将收到一个`MongoCursorException`，因为集合中已经存在这个 ID。继续尝试将同一文档插入两次…

```
<?php
$document = array(
    "title" => "cat with a hat",
    "content" => "once upon a time a cat with a hat ...");
$collection->insert($document);
$collection->insert($document);
```

嗯，那是什么？没有抛出异常？

你没有得到一个错误，因为你没有做一个*安全插入*。默认情况下，MongoDB 扩展异步执行所有操作，因此您不必等到服务器响应数据已成功保存。此功能可让您继续执行下一个任务，而无需等待确认数据是否已保存，这非常方便，但对于重要数据，您可能希望确保文档对象确实已保存且没有发生错误。您可以通过将关键字 *safe* 设置为 true，将一个数组作为`insert()`的第二个参数来实现这一点。

再试一次，但是这次将第二个参数传递给`insert()`。

```
<?php
$insertOpts = array("safe" => true);
$collection->insert($document, $insertOpts);
$collection->insert($document, $insertOpts);
```

现在，您将得到我前面提到的异常，因为您正在执行安全插入，并且集合中已经存在另一个具有相同 ID 的对象。

### 更新文档

如果要修改现有的文档对象，可以使用`save()`方法。

```
<?php
$document["author"] = "Shreef";
$collection->save($document);
```

如果传递的数组没有 *_id* 键，那么将插入文档并分配一个主键。如果数组已经有一个 *_id* 键，那么文档对象将被更新。和`insert()`一样，`save()`也接受可选的第二个参数。

`save()`方法不是更新现有对象的唯一方法；也可以用`update()`的方法。`update()`将您想要更新的对象的标准作为第一个参数，将更新的对象作为第二个参数。

假设我想找到所有其 *author* 键等于“Shreef”的对象，并将值更新为“Timothy”。我可能会尝试以下方法:

```
<?php
$collection->update(
    array("author" => "Shreef"),
    array("author" => "Timothy"));
```

虽然这样做是可行的，但它也有一个意想不到的后果，即删除匹配文档中的所有其他字段；他们唯一拥有的字段是*作者*！这不是我想要的。要仅更新*的*作者*键的值，而保持其他键/值不变，您需要使用`$set`修饰符。*

```
<?php
$collection->update(
    array("author" => "Shreef"),
    array('$set' => array("author" => "Timothy")));
```

但是这仍然不是我想要的，因为 MongoDB 只会更新它找到的第一个匹配对象。要更新所有匹配文档对象的*，需要将一个数组作为第三个参数传递给`update()`，并将 key *multiple* 设置为 true。*

```
<?php
$collection->update(
    array("author" => "Shreef"),
    array('$set' => array("author" => "Timothy")),
    array("multiple" => true));
```

最后，我能够按照自己的意愿更新所有匹配的记录。`$set`修饰符确保只更新*作者*键的值，而*多重*键告诉 MongoDB 更新它找到的每个匹配的文档对象。

### 选择文档

要从一个集合中选择所有符合某些标准的存储文档，可以使用`find()`方法。该方法将查询条件作为第一个参数，也可以作为第二个参数，它可以接受要返回的字段名数组，而不是整个对象。

```
<?php
$cursor = $collection->find(array("author" => "shreef"));
foreach ($cursor as $document) {
    print_r($document);
}
```

`find()`返回一个`MongoCursor`对象，您可以用它来遍历结果。

如果您只想检索一个值，您可以使用`findOne()`方法，该方法将只返回一个包含第一个匹配对象的字段的数组。

```
<?php
$document = $collection->findOne(array("author" => "shreef"));
print_r($document);
```

## 摘要

MongoDB 是一个非常简单但功能强大的数据库，如果您有大量非结构化数据，可以使用它。除了我在这篇文章中提到的，还有很多很多更多的特性，也许将来我会写另一篇文章来讨论其中的一些。在那之前，试着按照文章中提到的例子来做，如果你有任何问题，请随意发表评论。

<small>图像通过[图形](http://www.shutterstock.com/gallery-368671p1.html "Stock Photos | Shutterstock: Royalty-Free Subscription Stock Photography & Vector Art") / [快门](http://www.shutterstock.com "Stock Photos and Royalty-Free Images by Subscription")</small>

## 分享这篇文章