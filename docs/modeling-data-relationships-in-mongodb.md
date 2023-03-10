# 在 MongoDB 中建模数据关系

> 原文：<https://www.sitepoint.com/modeling-data-relationships-in-mongodb/>

从开发一开始就考虑应用程序的数据需求是很重要的。但是，如果您的应用程序将使用 NoSQL，并且您来自 RDBMS/SQL 背景，那么您可能会认为根据 NoSQL 来查看数据可能会很困难。本文将向您展示一些基本的数据建模概念如何应用于 NoSQL 领域，从而为您提供帮助。

我将使用 MongoDB 进行我们的讨论，因为它是领先的开源 NoSQL 数据库之一，这是由于它的简单性、性能、可伸缩性和活跃的用户基础。当然，本文假设您了解基本的 MongoDB 概念(比如集合和文档)。如果没有，我建议您阅读 SitePoint 上以前的一些文章，开始使用 MongoDB。

## 理解关系

关系显示了您的 MongoDB 文档是如何相互关联的。为了理解组织文档的不同方式，让我们看看可能的关系。

### 一对一关系(1:1)

当一个实体的一个对象与另一个实体的一个且仅一个对象相关时，存在 1:1 关系。例如，一个用户只能有一个出生日期。因此，如果我们有一个存储用户信息的文档和另一个存储出生日期的文档，它们之间将存在 1:1 的关系。

![mongo-model-01](img/95bd5b06810971675d92bccff0ea7fe6.png)

### 一对多关系(1:N)

当一个实体的一个对象可以与另一个实体的许多对象相关时，就存在这种关系。例如，在用户和他的联系号码之间可以有 1:N 的关系，因为一个用户可能有一个以上的号码。

![mongo-model-02](img/3f121d062c744e6f6c0a9851e27e3b86.png)

### 多对多关系(多对多)

当一个实体的一个对象与另一个实体的许多对象相关时，存在这种关系，反之亦然。如果我们把它与用户和他们购买的商品联系起来，一个用户可以购买多个商品，一个商品可以被多个用户购买。

![mongo-model-03](img/0c7165c3a9de133acf2682f28a57ea91.png)

## 对一对一关系建模(1:1)

考虑下面的例子，我们需要存储每个用户的地址信息(现在让我们假设每个用户只有一个地址)。在这种情况下，我们可以设计一个具有以下结构的嵌入式文档:

```
{
    "_id": ObjectId("5146bb52d8524270060001f4"),
    "user_name": "Mark Benzamin" 
    "dob": "12 Jan 1991",
    "address": {
       "flat_name": "305, Sunrise Park",
       "street": "Cold Pool Street",
       "city": "Los Angeles" 
    }
}
```

我们将`address`实体嵌入到用户实体中，将所有信息呈现在一个文档中。这意味着我们可以通过一个查询找到并检索所有内容。

```
<?php
// query to find user 'Mark Benzamin' and his address 
$cursor = $collection->find(
    array("user_name" => "Mark Benzamin"),
    array("user_name" => 1,"address" => 1)
 );
```

嵌入文档大致类似于反规范化，在两个实体之间存在“包含”关系时非常有用。也就是说，一个文档可以存储在另一个文档中，从而将相关的信息放在一个文档中。因为所有信息都在一个文档中，所以这种方法具有更好的读取性能，因为文档中的查询操作对于服务器来说成本较低，并且我们在同一个查询中找到并检索相关数据。

相比之下，规范化的方法需要两个文档(理想情况下在不同的集合中)，一个存储基本的用户信息，另一个存储地址信息。第二个文档将包含一个`user_id`字段，指示地址所属的用户。

```
{
    "_id": ObjectId("5146bb52d8524270060001f4"),
    "user_name": "Mark Benzamin",
    "dob": "12 Jan 1991"
}
```

```
{
    "_id": ObjectId("5146bb52d852427006000de4"),
    "user_id": ObjectId("5146bb52d8524270060001f4"), 
    "flat_name": "305, Sunrise Park",
    "street": "Cold Pool Street",
    "city": "Los Angeles" 
}
```

我们现在需要执行两个查询来获取相同的数据:

```
<?php
// query to find user information
$user = $collection->findOne(
    array("user_name" => "Mark Benzamin"),
    array("_id" => 1, "user_name" => 1)
);

// query to find address corresponding to user
$address = $collection->findOne(
    array("user_id" => $user["_id"]),
    array("flat_name" => 1, "street" => 1, "city" => 1)
);
```

第一个查询获取用户的`_id`,然后在第二个查询中使用它来检索他的地址信息。

在这种情况下，嵌入方法比引用方法更有意义，因为我们经常一起检索`user_name`和`address`。您应该使用什么方法最终取决于您如何在逻辑上连接您的实体，以及您需要从数据库中检索什么数据。

## 建模嵌入式一对多关系(1:N)

现在让我们考虑一个用户可以有多个地址的情况。如果所有地址都应与基本用户信息一起检索，那么理想的做法是将地址实体嵌入到用户实体中。

```
{
    "_id": ObjectId("5146bb52d8524270060001f4"),
    "user_name": "Mark Benzamin"
    "address": [
        {
            "flat_name": "305, Sunrise Park",
            "street": "Cold Pool Street",
            "city": "Los Angeles"
        },
        {
            "flat_name": "703, Sunset Park",
            "street": "Hot Fudge Street",
            "city": "Chicago" 
        }
    ]
}
```

我们仍然能够通过一个查询获取所有需要的信息。引用/规范化方法需要我们设计三个文档(一个用户，两个地址)和两个查询来完成相同的任务。除了效率和方便之外，我们应该在操作中需要原子性的情况下使用嵌入式方法。因为任何更新都发生在同一个文档中，所以原子性总是有保证的。

## 建模引用一对多关系(1: N)

请记住，在应用程序的生命周期中，嵌入文档的大小会不断增长，这会严重影响写入性能。每个文档的最大大小也有 16MB 的限制。如果嵌入的文档太大，当嵌入方法会导致大量重复数据时，或者如果您需要对文档之间的复杂或层次关系进行建模，那么规范化方法是首选方法。

考虑维护用户发布的帖子的例子。假设我们希望每个帖子都有用户的姓名和个人资料图片(类似于脸书的帖子，我们可以看到每个帖子的姓名和个人资料图片)。非规范化方法会将用户信息存储在每个 post 文档中:

```
{
    "_id": ObjectId("5146bb52d8524270060001f7"),
    "post_text": "This is my demo post 1", 
    "post_likes_count": 12, 
    "user": {
        "user_name": "Mark Benzamin",
        "profile_pic": "markbenzamin.jpg"
    }
}

{
   "_id": ObjectId("5146bb52d8524270060001f8"),
   "post_text": "This is my demo post 2", 
   "post_likes_count": 32, 
    "user": {
        "user_name": "Mark Benzamin",
        "profile_pic": "markbenzamin.jpg"
    }
}
```

我们可以看到这种方法在每个 post 文档中存储了冗余信息。向前看一点，如果用户名或个人资料图片发生了变化，我们将不得不更新所有相应帖子中的相应字段。因此，理想的方法是将信息标准化，并通过引用将它们联系起来。

```
{
    "_id": ObjectId("5146bb52d852427006000121"),
    "user_name": "Mark Benzamin",
    "profile_pic": "markbenzamin.jpg"
}
```

```
{
    "_id": ObjectId("5146bb52d8524270060001f7"),
    "post_text": "This is my demo post 1", 
    "post_likes_count": 12, 
    "user_id": ObjectId("5146bb52d852427006000121")
}

{
    "_id": ObjectId("5146bb52d8524270060001f8"),
    "post_text": "This is my demo post 2", 
    "post_likes_count": 32, 
    "user_id": ObjectId("5146bb52d852427006000121")
}
```

post 文档中的`user_id`字段包含对用户文档的引用。因此，我们可以使用如下两个查询获取用户发布的帖子:

```
<?php
$user = $collection->findOne(
    array("user_name" => "Mark Benzamin"),
    array("_id" => 1, "user_name" => 1, "profile_pic" => 1)
); 

$posts = $collection->find(
    array("user_id" => $user["_id"])
);
```

## 对多对多关系建模(多对多)

让我们以前面的例子为例，存储用户和他们购买的商品(理想情况下在单独的集合中)，并设计引用的文档来说明 M:N 关系。假设存储用户信息文档的集合如下，每个文档包含用户购买的商品列表的引用 id。

```
{                                 
    "_id": "user1",
    "items_purchased": {
        "0": "item1",
        "1": "item2" 
    } 
}

{
    "_id": "user2",
    "items_purchased": {
        "0": "item2",
        "1": "item3" 
    } 
}
```

类似地，假设另一个集合存储可用项目的文档。这些文档将依次存储已经购买它的用户列表的参考 id。

```
{                                 
    "_id": "item1",
    "purchased_by": {
        "0": "user1"
    } 
}

{                                 
    "_id": "item2",
    "purchased_by": {
        "0": "user1",
        "1": "user2" 
    } 
}

{                                 
    "_id": "item3",
    "purchased_by": {
        "0": "user2"
    } 
}
```

为了获取用户购买的所有商品，我们将编写以下查询:

```
<?php
// query to find items purchased by a user
$items = $collection->find(
    array("_id" => "user1"),
    array("items_purchased" => 1)
);
```

上述查询将返回 user1 购买的所有商品的 id。我们可以稍后使用这些来获取相应的项目信息。

或者，如果我们想获取购买了特定商品的用户，我们可以编写以下代码:

```
<?php
// query to find users who have purchased an item
$users = $collection->find(
    array("_id" => "item1"),
    array("purchased_by" => 1)
);
```

上面的查询返回购买了 item1 的所有用户的 id。我们可以稍后使用这些 id 来获取相应的用户信息。

这个例子演示了在某些情况下非常有用的 M:N 关系。但是，您应该记住，很多时候这种关系可以使用 1:N 关系和一些智能查询来处理。这减少了要在两个文档中维护的数据量。

## 结论

本文到此为止。我们已经了解了一些基本的建模概念，这些概念肯定会帮助您开始自己的数据建模:一对一、一对多和多对多关系，以及一些关于数据规范化和反规范化的知识。您应该能够轻松地将这些概念应用到您自己的应用程序的建模需求中。如果你对这篇文章有任何问题或评论，欢迎在下面的评论区分享。

## 分享这篇文章