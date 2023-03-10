# 蒙古入门

> 原文：<https://www.sitepoint.com/getting-started-with-mongolab/>

MongoDB 是那些迅速流行起来的 NoSQL 数据库之一。即使你对 NoSQL 运动有点怀疑，花一些时间在 MongoDB 上会是一个很好的体验。尤其是当您了解到它是一个开源项目，并且在所有主流操作系统上都受支持时。

远程托管 MongoDB 出现了许多 PaaS 选项，这并不奇怪。本文中我要给大家介绍的这款叫做 [MongoLab](https://mongolab.com) 。

## 免费账户选项

有一个免费的帐户选项。你可以在一个共享账户上获得 240MB 的空间，它可以托管在亚马逊、Rackspace 或 Joyent 上。如果您的需求增长，还可以选择扩展。您会看到一个 web 管理区域，向您显示数据库、用户和集合的概述。它还向您展示了所需的连接字符串，以便您可以从自己的机器上通过 MongoDB shell 连接到您的数据库。

## 获得一个帐户

注册一个免费账户只需要几秒钟。只需[到这里注册](https://mongolab.com/signup/)，您马上就可以开始运行了。

## 安装 MongoDB

您需要在自己的计算机上安装 MongoDB。有各种选项可以使用，在 [MongoDB 网站](http://www.mongodb.org/display/DOCS/Quickstart)上有详细描述。我建议您按照他们的说明创建一个数据目录。我已经在几台不同的机器上安装了 MongoDB，过程每次都一样详细。

如果你用的是 Mac，我建议你[下载 64 位二进制版本](http://fastdl.mongodb.org/osx/mongodb-osx-x86_64-2.0.3.tgz)。我已经在几台不同的机器上使用过这种方法，效果很好。您可以将文件夹放在您喜欢的任何地方；我将我的文件夹放在我的主目录中，以便于从终端调用 bin 文件夹。

## 关于司机的一句话

所有流行的编程语言都有 MongoDB 驱动程序。

### 服务器端编程语言（Professional Hypertext Preprocessor 的缩写）

如果您想使用 PHP 和 MongoDB，如本文所述，您需要安装 [Mongo PHP 驱动程序](https://github.com/mongodb/mongo-php-driver/downloads)。所有主要操作系统都有可用的驱动程序。

### 计算机编程语言

也支持其他编程语言。例如，如果 Python 是你的东西，那么你将需要[安装 PyMongo](http://api.mongodb.org/python/current/installation.html) 。

一旦您完成了这些，从 shell 中运行 Python 应该允许您:

```
import pymongo
```

…没有任何错误。

### 红宝石

红宝石有一颗蒙哥宝石。首先，确保 RubyGems 是最新的:

```
gem update --system
```

然后就可以安装 Mongo gem 了:

```
gem install mongo
```

最佳建议还建议您安装 bson_ext gem 来优化性能:

```
gem install bson_ext
```

现在，您可以启动 Ruby 交互式 shell 并执行以下操作:

```
require 'mongo'
```

按回车键，您应该得到以下响应:

```
=> true
```

这告诉你一切都好。

## 创建数据库

在 MongoLab web 管理面板中，您可以创建一个数据库，然后通过本地 shell 连接并使用它。登录您的帐户后，单击“添加”按钮创建一个数据库:

![create a database](img/78daa09d87061eb276a3b505470d30b8.png)

然后会提示您输入一些基本的配置信息:

![enter database info](img/2efa4ee98d713e3c1df3c40a0190dcf4.png)

完成后，您的数据库就可以连接了。

## 连接到您的 MongoLab 帐户

乍一看可能有点奇怪，但是要连接到您的 MongoLab 帐户，您需要在您自己的机器上运行 MongoDB。一旦运行了它，就可以在 MongoLab 帐户上连接到 MongoDB 实例。

首先，从终端/控制台启动本地主机服务器:

```
./mongodb/bin/mongod
```

这假设您已经在主目录中安装了 MongoDB。然后在浏览器中打开你的 MongoLab 账户。您将在 web 管理区域的数据库窗口顶部看到您需要的连接信息:

![MongoLab connection information](img/28ffa044c9f7fdd6789c04c2be2f0a43.png)

我建议您使用 shell 访问方法，因为并非所有驱动程序都支持标准的 URI 方法。

打开另一个终端选项卡，执行以下操作，而不是通常的`./mongodb/bin/mongo`:

```
./mongodb/bin/mongo dbh23.mongolab.com:27237/my_new_db -u <db-user> -p <db-password>
```

您需要使用的确切字符串将显示在您帐户的 web admin 部分。您可以在 web 管理区域中为您的数据库创建一个用户，方法是单击 users 选项卡:

![MongoLab creating users](img/01aab26d9374c6f9bdffebe6d64c3fc1.png)

## 创建一个集合并添加一些记录

MongoDB 的一个好处是您不必首先定义数据结构。我们可以直接开始在 shell 中创建记录。然后，我们将能够在 MongoLab 帐户的 web 管理区域查看这些记录。

因此，在连接到 MongoLab 数据库的控制台/终端会话中，尝试以下操作:

```
db.task.insert({title:"create a web app that uses Mongodb",status:"in progress",context:"today"})
```

当你点击回车键时，你的记录将被保存。此时，您的集合已经在您所连接的数据库中创建，并且您的文档已经保存到其中。在 MongoDB 中，集合在上下文上相当于一个表，文档相当于 MySQL 等传统数据库中的一行。

继续以同样的方式添加几个文档。

然后，我们可以在 MongoLab web 管理区检查我们的文档。点击收藏列表中的“任务”:

![MongoLab collections](img/5808503252f9ee4cf6e9ff556c071d65.png)

您将看到您创建的文档列表:

![MongoLab collections](img/54d1593b6f0e88e8f980fb2371a7982f.png)

## 使用 PHP 访问 MongoLab 数据库并检索文档

这里假设您已经为本地系统安装了 MongoDB PHP 驱动程序。

我们简单应用程序的第一次迭代要求我们:

1.  连接到 MongoLab 数据库
2.  建立我们想要使用的数据库
3.  建立我们想要使用的集合

我们可以通过创建一些变量并使用 MongoLabs 为我们提供的连接字符串信息来做到这一点。这一次，我们将使用驱动程序(标准 URI)版本。我们可以将我们的连接尝试放在一个 try/catch 块中，这样如果我们由于某种原因无法连接，应用程序就会正常失败:

```
<?php
try
{
    $connection = new Mongo('mongodb://<username>:<password>@ds031347.mongolab.com:31347/your_database');
    $database   = $connection->selectDB('your_database');
    $collection = $database->selectCollection('tasks');
}
catch(MongoConnectionException $e)
{
    die("Failed to connect to database ".$e->getMessage());
}

$cursor = $collection->find();

?>
```

希望大部分内容都是不言自明的。然而，这里有一些 PHP MongoDB 类特有的东西，比如:

```
$cursor = $collection->find();
```

这一行代码使用 MongoCursor 方法来检索集合中的所有文档。

接下来，我们将在现有代码下创建一个 HTML 文档，并遍历文档以在页面上显示它们:

```
<!DOCTYPE html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=iso-8859-1" />
    <title>Tasks Viewer</title>

    <link type="text/css" rel="stylesheet" href="" />
    <!--[if lt IE 9]>
        <script src="http://html5shim.googlecode.com/svn/trunk/html5.js"></script>
    <![endif]-->

</head>
<body>
<h1>My Tasks</h1>

<?php while ($cursor->hasNext()):
    $task = $cursor->getNext(); ?>
    <h2><?= $task['title'] ?></h2>
    <strong>Status:</strong> <?= $task['status']?> <br />
    <strong>Context:</strong> <?= $task['context']?><br />
<?php endwhile;?>
</body>
</html>
```

同样，如果你在 PHP 上花了很多时间，这应该看起来相当简单。我们使用 while 循环遍历返回的文档，打印出文档中的数据。注意 PHP 类有专门用于该目的的`hasNext()`和`getNext()`方法。

你可以在这里阅读更多关于 PHP MongoDB 类的内容。

## 最后…

希望这篇文章已经吊足了您的胃口，让您认真研究一下 MongoDB。MongoLab 是一个优秀的服务，它提供了对 MongoDB 的基于云的访问，并且运行速度非常快。如果你正确地安装了所有的东西，你可以用你最喜欢的技术马上编写连接到 MongoDB 的应用程序。

在下一篇文章中，我们将扩展这个简单的应用程序，并添加我们用于其他数据库的常见“CRUD”操作的 C、U 和 D 部分。

[树叶](http://www.shutterstock.com/cat.mhtml?lang=en&search_source=search_form&version=llv1&anyorall=all&safesearch=1&searchterm=leaf&search_group=&orient=&search_cat=&searchtermx=&photographer_name=&people_gender=&people_age=&people_ethnicity=&people_number=&commercial_ok=&color=&show_color_wheel=1#id=61372102&src=64d9fba491e06cb7536e633767809678-1-19)图片通过 Shutterstock

## 分享这篇文章