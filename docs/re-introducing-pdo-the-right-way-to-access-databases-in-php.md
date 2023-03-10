# 重新介绍 PDO——用 PHP 访问数据库的正确方法

> 原文：<https://www.sitepoint.com/re-introducing-pdo-the-right-way-to-access-databases-in-php/>

PDO 是 PHP 数据对象的首字母缩写。顾名思义，这个扩展使您能够通过对象与数据库进行交互。

![Stock graphic of database icon branching into other icons](img/1fddd24583ae843f13a4f641758a4dbf.png)

## 为什么不是`mysql`和`mysqli`？

面对新技术，人们会问一个非常合理的问题:为什么要升级？这项新技术给他们带来了什么，值得他们花大力气检查整个应用程序，并把所有东西都转换成这个新的库、扩展或其他东西？

这是一个非常合理的担忧。我们之前已经在某种程度上写过这个问题，但是让我们来看看为什么我们认为升级是值得的。

### PDO 是面向对象的

让我们面对现实吧:PHP 正在快速发展，并且正在成为一种更好的编程语言。通常，当这种情况发生在动态语言中时，该语言会增加其严格性，以便让程序员能够安心地编写企业应用程序。

就 PHP 而言，更好的 PHP 意味着 [*面向对象的 PHP*](http://blog.ircmaxell.com/2012/07/oop-vs-procedural-code.html) 。这意味着你使用对象越多，你就能更好地测试你的代码，编写可重用的组件，并且，通常，增加你的薪水。

使用 PDO 是使应用程序的数据库层面向对象和可重用的第一步。正如您将在本文的其余部分看到的，用 PDO 编写面向对象的代码比您想象的要简单得多。

### 抽象

假设你在当前的工作场所使用 MySQL 编写了一个杀手级应用程序。突然，链上的某个人决定您必须迁移您的应用程序以使用 [Postgres](http://www.postgresql.org/) 。你要怎么办？

你必须做许多混乱的替换，比如将 [mysql_connect](http://php.net/mysql_connect) 或 [mysqli_connect](http://php.net/mysqli_connect) 转换为 [pg_connect](http://php.net/pg_connect) ，更不用说你用于运行查询和获取结果的所有其他函数了。如果你使用 PDO，这将是非常简单的。只需要修改主配置文件中的几个参数，就可以了。

### 它允许参数绑定

参数绑定是一种允许您用变量值替换查询中的占位符的功能。意思是:

*   在运行时，你不必知道你将有多少占位符。
*   你的应用对 SQL 注入来说会安全得多。

### 您可以将数据提取到对象中

使用过像[教条](http://www.doctrine-project.org/)这样的 ORM 的人知道能够用对象表示表中数据的价值。如果你想拥有这个特性，但是不想学习 ORM，或者不想把它集成到一个已经存在的应用程序中，PDO 将允许你把表中的数据提取到一个对象中。

### 不再支持`mysql`扩展名！

是的，`mysql`扩展终于从 PHP 7 中移除了[。这意味着如果你要使用 PHP 7，你需要把所有这些函数都改成`mysqli_*`而不是`mysql_*`。这是一个直接升级到 PDO 的好时机，因为它可以帮助你用更少的努力编写可维护的、可移植的代码。](https://wiki.php.net/rfc/remove_deprecated_functionality_in_php7)

我希望上述原因已经说服你开始将 PDO 集成到你的应用程序中。不用担心设置问题；您的系统中可能已经有它了！

## 证明 PDO 的存在

如果您使用的是 PHP 5.5.X 和更高版本，那么您的安装可能已经包含了 PDO。要进行验证，只需在 Linux 和 Mac OS X 上打开终端，或者在 Windows 上打开命令提示符，然后键入以下命令:

```
php -i | grep 'pdo'
```

您还可以在 webroot 下创建一个 php 文件，并在其中插入一个`phpinfo()`语句:

```
<?php
phpinfo();
```

现在，在浏览器中打开这个页面，搜索`pdo`字符串。

如果你有 PDO 和 MySQL，跳过安装说明。如果你有 PDO，但没有 MySQL，你只需要按照下面的说明安装`mysqlnd`扩展。然而，如果你根本没有 PDO，你的路会更长，但不会更难！继续阅读，我们会告诉你如何通过安装 PDO 和`mysqlnd`装备！

## PDO 的安装

如果你已经通过一个包管理器(比如 apt、yum、pacman 等等)从一个库安装了 PHP，安装 PDO 是非常简单直接的；只需运行下面您各自的操作系统和发行版中列出的安装命令。如果你还没有，我也列出了我推荐的从头开始的方法。

### Fedora、RedHat 和 CentOS

首先，如果你还没有 Remi 库，使用他们的博客上提供的说明添加 Remi 库。完成后，您可以使用以下命令轻松安装`php-pdo`:

```
sudo yum --enablerepo=remi,remi-php56 install php-pdo
```

> **注意**:虽然需要启用`remi`库，但是您需要在上面的命令中用您想要的库替换`remi-php56`。

当然，如果您还没有它，您也需要使用下面的命令安装`mysqlnd`扩展:

```
sudo yum --enablerepo=remi,remi-php56 install php-mysqlnd
```

### Debian 和 Ubuntu

在 Ubuntu 上，您需要添加 [Ondrej](https://launchpad.net/~ondrej/+archive/ubuntu/php5-5.6/+index?batch=75) 存储库。该链接指向 5.6 版的 PPA，但是您也可以在那里找到以前版本的链接。

在 Debian 上，您应该将 [Dotdeb](https://www.dotdeb.org/instructions/) 库添加到您的系统中。

在这两个发行版中，一旦安装了`php5`元包，就已经准备好并配置了 PDO。你需要做的就是简单地安装`mysqlnd`扩展:

```
sudo apt-get install php5-mysqlnd
```

### Windows 操作系统

你[应该尝试使用](https://www.sitepoint.com/re-introducing-vagrant-right-way-start-php)一个 Linux 虚拟机在 Windows 上进行开发，但是如果你还没有准备好，请遵循下面的说明。

在 Windows 上，你通常使用 [Wamp](http://www.wampserver.com/en/) 或 [Xampp](https://www.apachefriends.org/) 来获得完整的灯堆栈。你也可以直接从 windows.php.net 的 T4 下载 PHP。显然，如果你做了后者，你将只有 PHP，而不是整个栈。

在这两种情况下，如果 PDO 还没有被激活，您只需要在您的`php.ini`中取消对它的注释。使用你的 lamp stack 中提供的工具来编辑`php.ini`，或者如果已经从[windows.php.net](http://windows.php.net)下载了 PHP，只需打开你选择作为安装目录的文件夹并编辑`php.ini`。一旦您这样做了，取消对该行的注释:

```
;extension=php_pdo_mysql.dll
```

## 从 PDO 开始:高层次的概述

当使用 PDO 查询数据库时，您的工作流程不会有太大变化。然而，有一些习惯你必须学会放弃，还有一些你必须学会。下面是在应用程序中使用 PDO 时需要执行的步骤。我们将在下面更详细地解释每一个。

*   连接到您的数据库
*   可选地，准备语句和绑定参数
*   执行查询

## 连接到您的数据库

要连接到您的数据库，您需要[实例化一个新的 PDO 对象](http://php.net/manual/en/pdo.construct.php)并传递给它一个数据源名称，也称为 DSN。

> 一般来说，DSN 由 PDO 驱动程序名、冒号和 PDO 驱动程序特定的连接语法组成。更多信息可从 [PDO 驱动程序专用文档](http://php.net/manual/en/pdo.drivers.php)中获得。

例如，下面是连接 MySQL 数据库的方法:

```
$connection =  new PDO('mysql:host=localhost;dbname=mydb;charset=utf8',  'root',  'root');
```

上面的函数包含 DSN、用户名和密码。如上所述，DSN 包含驱动程序名称(`mysql`)和特定于驱动程序的选项。对于`mysql`，这些选项是`host`(在`ip:port`格式中)、`dbname`和`charset`。

## 问题

与`mysql_query()`和`mysqli_query()`的工作方式相反，在 PDO 中有两种查询:返回结果的查询(例如`select`和`show`)，以及不返回结果的查询(例如`insert`、`delete`等等)。让我们先来看看更简单的选项:没有的选项。

### 执行查询

这些查询运行起来非常简单。让我们看一个插页。

```
$connection->exec('INSERT INTO users VALUES (1, "somevalue"');
```

从技术上讲，当我说这些查询不返回结果时，我撒了谎。如果将上面的代码改为下面的代码，您将看到`exec()`返回受影响的行数:

```
$affectedRows = $connection->exec('INSERT INTO users VALUES (1, "somevalue"'); echo $affectedRows;
```

您可能已经猜到，对于 insert 语句，这个值通常是 1。但是对于其他语句，这个数字会有所不同。

### 获取查询结果

使用`mysql_query()`或`mysqli_query()`，下面是如何运行查询:

```
$result = mysql_query('SELECT * FROM users');  while($row = mysql_fetch_assoc($result))  { echo $row['id']  .  ' '  . $row['name'];  }
```

然而，对于 PDO，事情要直观得多:

```
foreach($connection->query('SELECT * FROM users')  as $row)  { echo $row['id']  .  ' '  . $row['name'];  }
```

#### 抓取模式:`Assoc`、`Num`、`Obj`和`class`

就像使用`mysql`和`mysqli`扩展一样，您可以在`PDO`中以不同的方式获取结果。为此，您必须传入一个`PDO::fetch_*`常量，在[获取](http://www.php.net/manual/en/pdostatement.fetch.php)函数的帮助页面中有解释。如果你想立刻得到所有的结果，你可以使用 [fetchAll](http://php.net/manual/en/pdostatement.fetchall.php) 函数。

下面是一些我们认为最有用的获取模式。

*   PDO::FETCH_ASSOC:返回一个按列名索引的数组。也就是说，在我们之前的例子中，您需要使用`$row['id']`来获得`id`。
*   PDO::FETCH_NUM:返回一个按列号索引的数组。在我们之前的例子中，我们通过使用`$row[0]`得到了`id`列，因为它是第一列。
*   PDO::FETCH_OBJ:返回一个匿名对象，其属性名对应于结果集中返回的列名。例如，`$row->id`将保存`id`列的值。
*   PDO::FETCH_CLASS:返回所请求类的新实例，将结果集的列映射到类中的命名属性。如果 fetch_style 包括 PDO::FETCH_CLASSTYPE(例如，PDO::FETCH _ CLASS | PDO::FETCH _ CLASS type)，则类的名称由第一列的值确定。如果您还记得的话，我们注意到`PDO`，以其最简单的形式，可以将列名映射到您定义的类中。这个常数就是你要用到的。

**注意**:这个列表并不完整，我们建议查看前面提到的帮助页面，以获得所有可能的常量和组合。

例如，让我们将行作为关联数组:

```
$statement = $connection->query('SELECT * FROM users');  while($row = $statement->fetch(PDO::FETCH_ASSOC))  { echo $row['id']  .  ' '  . $row['name'];  }
```

> **注意**:我们建议总是选择一种获取模式，因为按照`PDO::FETCH_BOTH`(默认)获取结果需要两倍的内存，因为 PHP 通过关联数组和普通数组提供对不同列值的访问。

您可能还记得，在上面，当我们列出`PDO`的优点时，我们提到有一种方法可以让`PDO`将当前行存储在您之前定义的类中。你可能也见过上面解释的`PDO::FETCH_CLASS`常数。现在，让我们用它从数据库中检索数据到一个`User`类的实例中。下面是我们的`User`班:

```
class  User  {  protected $id;  protected $name;  public  function getId()  {  return $this->id;  }  public  function setId($id)  { $this->id = $id;  }  public  function getName()  {  return $this->name;  }  public  function setName($name)  { $this->name = $name;  }  }
```

现在，我们可以再次进行相同的查询，这一次使用我们的`User`类，在这些情况下，它也被称为`Model`、`Entity`，或者一个普通的旧 PHP 对象(取自 Java 世界中的[普通的旧 Java 对象](https://en.wikipedia.org/wiki/Plain_Old_Java_Object))。

```
$statement = $connection->query('SELECT * FROM users');

while($row = $statement->fetch(PDO::FETCH_CLASS, 'User')) {
    echo $row->getId() . ' ' . $row->getName();
}
```

## 准备好的语句和绑定参数

为了理解参数绑定及其好处，我们必须首先更深入地了解 PDO 是如何工作的。当我们调用上面的`$statement->query()`时，`PDO`在内部准备了一个语句，并执行它，将结果语句返回给我们。

当您调用`$connection->prepare()`时，您正在创建一个[准备好的语句](https://en.wikipedia.org/wiki/Prepared_statement)。准备好的语句是一些数据库管理系统的一个功能，允许它们像接收模板一样接收查询，编译它，并在接收占位符的值时执行它——把它们想象成呈现你的[刀片](http://laravel.com/docs/5.1/blade)或[树枝](http://twig.sensiolabs.org/)模板。

当您稍后调用`$statement->execute()`时，您正在传递这些占位符的值，并告诉数据库管理系统实际运行查询。这就像调用你的模板引擎的`render()`函数。

为了看到这一点，让我们创建一个从数据库返回指定的`id`的查询:

```
$statement = $connection->prepare('Select * From users Where id = :id');
```

上面的 PHP 代码将包括`:id`占位符在内的语句发送到数据库管理系统。数据库管理系统解析并编译该查询，并且根据您的配置，甚至可能缓存该查询，以便在将来提高性能。现在，您可以将参数传递给数据库引擎，并告诉它执行您的查询:

```
$id =  5; $statement->execute([  ':id'  => $id ]);
```

然后，您可以从语句中获取结果:

```
$results = $statement->fetchAll(PDO::FETCH_OBJ);
```

### 参数绑定的好处

现在，您对预准备语句的工作原理更加熟悉了，您大概可以猜到它们的好处了。

`PDO`已经把你从用户那里得到的输入值的逃避和引用的任务从你手中拿走了。例如，现在您不必编写这样的代码:

```
$results = mysql_query(sprintf("SELECT * FROM users WHERE name='%s'", mysql_real_escape_string($name)  )  )  or  die(mysql_error());
```

相反，你可以说:

```
$statement = $connection->prepare('Select * FROM users WHERE name = :name'); $results = $connection->execute([  ':name'  => $name ]);
```

如果对您来说还不够短，您甚至可以使它更短，通过提供非`named`的参数——这意味着它们只是编号的占位符，而不是像命名的变量一样:

```
$statement = $connection->prepare('SELECT * FROM users WHERE name = ?'); $results = $connection->execute([$name]);
```

同样，有一个准备好的语句意味着在多次运行查询时性能会得到提升。假设我们想从我们的`users`表中检索五个随机人员的列表:

```
$numberOfUsers = $connection->query('SELECT COUNT(*) FROM users')->fetchColumn(); $users =  []; $statement = $connection->prepare('SELECT * FROM users WHERE id = ? LIMIT 1');  for  ($i =  1; $i <=  5; $i++)  { $id = rand(1, $numberOfUsers); $users[]  = $statement->execute([$id])->fetch(PDO::FETCH_OBJ);  }
```

当我们第一次调用[准备](http://php.net/manual/en/pdo.prepare.php)函数时，我们告诉 DBMS 解析、编译并缓存我们的查询。稍后在我们的`for`循环中，我们只给它发送占位符的值——仅此而已。这允许查询更快地运行和返回，有效地减少了我们的应用程序从数据库中检索结果所需的时间。

您可能还注意到，我在上面的代码中使用了一个新函数: [fetchColumn](http://php.net/manual/en/pdostatement.fetchcolumn.php) 。正如您可能猜到的那样，它只返回一列的值，并且适合从查询结果中获取标量值，例如`count`、`sum`、`min`、`max`，以及其他只返回一列结果的函数。

## 将值绑定到一个`IN`子句

当很多人第一次开始学习`PDO`时，他们感到困惑的是`IN`条款。例如，假设我们允许用户输入一个逗号分隔的名字列表，并存储在`$names`中。到目前为止，我们的代码是:

```
$names = explode(',', $names);
```

大多数人在这一点上的做法如下:

```
$statement = $connection->prepare('SELECT * FROM users WHERE name IN (:names)'); $statement->execute([':names'  => $names]);
```

这是行不通的——您只能将一个标量值(如整数、字符串等)传递给准备好的语句！方法是——你猜对了——自己构造字符串。

```
$names = explode(',', $names); $placeholder = implode(',', array_fill(0, count($names),  '?')); $statement = $connection->prepare("SELECT * FROM users WHERE name IN ($placeholder)"); $statement->execute([$names]);
```

尽管看起来很吓人，但第 2 行只是创建了一个问号数组，其元素数量与我们的`names`数组一样多。然后它连接数组中的元素，并在它们之间放置一个`,`——有效地创建了类似于`?,?,?,?`的东西。由于我们的`names`数组也是一个数组，将它传递给`execute()`会像预期的那样工作——第一个元素绑定到第一个问号，第二个元素绑定到第二个问号，依此类推。

## 绑定参数时提供数据类型

当您刚开始学习`PDO`时，我们上面展示的将值绑定到参数的技术是很好的，但是指定您绑定的每个参数的类型总是更好。为什么？

*   可读性:对于阅读您代码的人来说，很容易看出变量必须是什么类型才能绑定到参数
*   可维护性:知道查询中的第一个占位符必须是整数，可以让您捕捉到任何遗漏的错误。例如，如果有人将一个包含`test`的变量传递给函数，函数将使用该值搜索一个整数形式的特定 id，拥有一个数据类型可以让您快速找到错误。
*   速度:当您指定变量的数据类型时，您是在告诉您的数据库管理系统没有必要对变量进行强制转换，并且您正在为它提供正确的类型。这样，您就不会有数据类型之间的转换所带来的(小)开销。

要指定每个变量的类型，我个人推荐 [bindValue](http://php.net/manual/en/pdostatement.bindvalue.php) 函数。让我们修改上面的代码来指定占位符的类型:

```
$numberOfUsers = $connection->query('SELECT COUNT(*) FROM users')->fetchColumn(); $users =  []; $statement = $connection->prepare('SELECT * FROM users WHERE id = ? LIMIT 1');  for  ($i =  1; $i <=  5; $i++)  { $id = rand(1, $numberOfUsers); $statement->bindValue(1, $id, PDO::PARAM_INT); $statement->execute(); $users[]  = $statement->fetch(PDO::FETCH_OBJ);  }
```

正如您所看到的，唯一改变的是我们对`execute()`的调用:我们没有直接向它传递值，而是先绑定它，并指定它的类型是整数。

> **注意**:你可能已经注意到我们已经将`bindValue()`的第一个参数指定为`1`。如果我们正在使用一个命名参数(推荐)，我们将传入参数的名称(例如`:id`)。然而，在使用`?`作为占位符的情况下，`bindValue()`的第一个参数是一个数字，指定您所指的是哪个问号。小心——这是一个 1 索引位置，意味着它从`1`开始，而不是`0`！

## 结论

随着 PHP 的进步，使用它的程序员也在进步。PDO 是下一代扩展，允许你写更好的代码。它敏捷、快速、易读，并且令人愉快，所以为什么不在自己的项目中实现它呢？

你已经把 PDO 应用到你现有的项目中了吗？你面临的问题是什么？你高兴你迁移过来吗？你错过了什么功能？请在下面的评论中告诉我们！

## 分享这篇文章