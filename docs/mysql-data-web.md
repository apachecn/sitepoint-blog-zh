# 在 Web 上显示来自 MySQL 的数据:简介

> 原文：<https://www.sitepoint.com/mysql-data-web/>

**以下文章摘自 *[PHP & MySQL:新手到忍者，第 7 版](https://www.sitepoint.com/premium/books/php-mysql-novice-to-ninja-7th-edition)* ，这是一本学习构建专业 web 应用程序所需的所有工具、原则和技术的实用指南。在本系列的最后一篇教程中，您将学习如何获取存储在 MySQL 数据库中的信息，并将其显示在网页上供所有人查看。**

* * *

*   [用 Docker 建立你的 PHP 开发环境](https://www.sitepoint.com/php-development-environment)
*   PHP 初学者指南
*   【MySQL 入门指南
*   **在 Web 上显示来自 MySQL 的数据:简介**

* * *

这就是你注册的东西！在这一章中，你将学习如何获取存储在 MySQL 数据库中的信息，并将其显示在网页上供所有人查看。

到目前为止，您已经编写了第一个 PHP 代码，并学习了关系数据库引擎 MySQL 和服务器端脚本语言 PHP 的基础知识。

现在，您已经准备好学习如何结合使用这些工具来创建一个网站，用户可以在该网站上查看数据库中的数据，甚至可以添加自己的数据。

注意:和第三章一样，我在这里用“MySQL”来指代数据库协议。您的 PHP 脚本也会这样做。在这一章中——以及在您将要编写的 PHP 代码中——有许多对“MySQL”的引用，尽管我们实际上连接的是一个 MariaDB 数据库。

[![PHP & MySQL: Novice to Ninja](img/0c5e27a112706e9dfe4e0f846efcefa0.png)](https://www.sitepoint.com/premium/books/php-mysql-novice-to-ninja-7th-edition/)

## 大局

在我们跃进之前，有必要后退一步，清楚地了解我们的最终目标。我们有两个强大的工具:PHP 脚本语言和 MySQL 数据库引擎。重要的是要理解它们是如何结合在一起的。

在我们的网站中使用 MySQL 的目的是允许从数据库中动态提取内容，以创建在常规浏览器中查看的网页。因此，在系统的一端，有一个访问者使用 web 浏览器请求一个页面。浏览器期望得到一个标准的 HTML 文档作为回报。另一端是站点的内容，它位于 MySQL 数据库的一个或多个表中，只知道如何响应 SQL 查询(命令)。

![Relationships between the web server, browser, PHP and MySQL](img/e7c9174e4608fafec0ab5f83af9b4244.png)

如上图所示，PHP 脚本语言是两种语言的中间人。它处理页面请求，并使用 SQL 查询从 MySQL 数据库获取数据，就像在第 3 章中创建笑话表一样。然后，它动态地将其作为浏览器所期望的格式良好的 HTML 页面输出。

在你的脑海中，这是当有访问者访问你网站上的页面时会发生的事情:

1.  访问者的 web 浏览器向您的 web 服务器请求网页。
2.  web 服务器软件(通常是 Apache 或 NGINX)识别出请求的文件是一个 PHP 脚本，因此服务器启动 PHP 解释器来执行文件中包含的代码。
3.  某些 PHP 命令(这将是本章的重点)连接到 MySQL 数据库并请求属于网页的内容。
4.  MySQL 数据库通过向 PHP 脚本发送请求的内容来做出响应。
5.  PHP 脚本将内容存储到一个或多个 PHP 变量中，然后使用`echo`语句将内容作为网页的一部分输出。
6.  PHP 解释器最后将它创建的 HTML 副本传递给 web 服务器。
7.  web 服务器将 HTML 像普通的 HTML 文件一样发送到 web 浏览器，只是页面不是直接来自 HTML 文件，而是由 PHP 解释器提供的输出。然而，浏览器无法知道这一点。就浏览器而言，它像其他任何网页一样请求和接收网页。

## 创建 MySQL 用户帐户

为了让 PHP 连接到您的 MySQL 数据库服务器，它需要使用用户名和密码。到目前为止，你的笑话数据库包含的都是一些精粹的妙语，但不久之后，它可能会包含敏感信息，如电子邮件地址和你网站用户的其他私人信息。出于这个原因，MySQL 被设计得非常安全，让您可以严格控制它将接受什么连接以及这些连接允许做什么。

Docker 环境已经在第 3 章中包含了一个 MySQL 用户，您已经使用它登录到 MySQL 服务器。

你*可以*使用相同的用户名(`v.je`)和密码(`v.je`)从你的 PHP 脚本连接到数据库，但是创建一个新的帐户是有用的——因为如果你有一个 web 服务器，你可能想用它来托管多个网站。通过为每个网站提供自己的用户帐户，您可以更好地控制谁可以访问任何给定网站的数据。如果您与其他开发人员合作，您可以允许他们访问他们正在开发的网站，但仅此而已。

您应该创建一个新的用户帐户，该帐户只具有在您的网站所依赖的`ijdb`数据库上工作所需的特定权限。让我们现在做那件事。

要创建用户，打开 MySQL Workbench 并连接到您的服务器。然后运行以下查询:

```
 CREATE USER 'ijdbuser'@'%' IDENTIFIED BY 'mypassword';
GRANT ALL PRIVILEGES ON `ijdb`.* TO 'ijdbuser'@'%'; 
```

第一个查询相当简单:它创建了一个名为`ijdbuser`的用户，密码为`mypassword`。用户名后面的`%`符号表示数据库可以从任何位置连接。第二个查询给予用户对`ijdb`模式的完全访问权，因此该用户可以查看和修改`ijdb`模式中的所有表、列和数据，但是不能访问模式之外的任何内容。

现在已经创建了用户`ijdbuser`，我们可以用它来连接数据库。可以在 MySQL Workbench 中与该用户建立连接，但由于权限有限，最好保持 MySQL Workbench 使用`v.je`帐户。相反，当从 PHP 脚本连接时，我们将使用新用户。

## 用 PHP 连接到 MySQL

在从 MySQL 数据库中检索内容以包含在网页中之前，您必须知道如何从 PHP 脚本内部建立到 MySQL 的连接。到目前为止，您已经使用了一个名为 MySQL Workbench 的应用程序来连接数据库。正如 MySQL Workbench 可以直接连接到正在运行的 MySQL 服务器，您自己的 PHP 脚本也可以。

虽然这一章完全讲述了从 PHP 连接到 MySQL，但我们实际上是连接到上一章讨论的 MariaDB 数据库。PHP 看不出 MySQL 和 MariaDB 有什么不同，因为它们是可以互换的。我将始终把数据库称为 MySQL，因为所有使用的命令都可以用来连接 MySQL 或 MariaDB 数据库服务器。

最初的 MySQL 数据库为 MySQL Workbench 和 PHP 等*客户端*提供了与服务器通信的标准化方法。MariaDB 复制了该标准，PHP 中的所有命令都使用名称 *MySQL* ，所以为了简单起见，我将在本章通篇使用术语 *MySQL* 来指代数据库。

从 PHP 连接到 MySQL 服务器有三种方法:

*   MySQL 库
*   MySQLi 库
*   PDO 图书馆

这些基本上都做相同的工作——连接到数据库并向其发送查询——但是它们使用不同的代码来实现。

MySQL 库是最古老的连接数据库的方法，是在 PHP 2.0 中引入的。它包含的功能很少，并且从 PHP 5.0(2004 年发布)开始被 MySQLi 取代。

要使用旧的 MySQL 库连接和查询数据库，需要使用`mysql_connect()`和`mysql_query()`等函数。从 PHP 5.5 开始，这些函数已经被**弃用**——这意味着它们应该被避免——并且从 PHP 7.0 开始已经完全从 PHP 中移除。

尽管大多数开发人员在 PHP 5.0 发布时就看到了这种变化的原因，但在 Web 上仍然有数百篇文章和代码示例使用这些现在已经不存在的`mysql_*`函数——尽管事实上 MySQLi 已经成为 15 年来最受欢迎的库。

如果您遇到包含行`mysql_connect()`的代码示例，请检查文章的日期。它可能来自 21 世纪初，在编程中，你永远不应该相信这么老的东西。事情总是在变化——这就是为什么这本书是第七版！

在 PHP 5.0 中，MySQL 库(代表“MySQL 改进版”)的发布解决了最初 MySQL 库中的一些限制。你可以很容易的识别出 MySQLi 的用法，因为代码会用到`mysqli_connect()`、`mysqli_query()`等函数。

在 PHP 5.0 中的 MySQLi 库发布后不久，PHP 5.1 也发布了，其中有大量的变化帮助塑造了我们今天编写 PHP 的方式(大部分与面向对象编程有关，这在本书后面会有很多介绍)。PHP 5.1 的主要变化之一是它引入了第三个库，PDO (PHP 数据对象)，用于连接 MySQL 数据库。

PDO 和 MySQLi 有几个不同之处，但主要的一点是，你可以使用 PDO 库连接到几乎任何数据库服务器，如 Oracle 服务器或 Microsoft SQL Server。对于开发人员来说，这种通用方法的最大优点是，一旦您学会了如何使用这个库与 MySQL 数据库进行交互，那么与另一个数据库服务器进行交互就非常简单了。

可以说，为 PDO 编写代码更简单，而且有一些细微差别可以使 PDO 代码更可读——准备好的语句中的命名参数是主要的好处。(别担心，我稍后会解释这是什么意思。)

由于这些原因，大多数最新的 PHP 项目都使用 PDO 库，我将在本书中向您展示如何使用这个库。有关这些差异的更多信息，请看 SitePoint 的文章“[重新介绍 PDO——用 PHP 访问数据库的正确方法](https://www.sitepoint.com/re-introducing-pdo-the-right-way-to-access-databases-in-php/)”。

在那堂小小的历史课之后，你可能渴望回去写代码。以下是如何使用 PDO 建立到 MySQL 服务器的连接:

```
new PDO('mysql:host=hostname;dbname=database', 'username',
  'password') 
```

现在，把`new PDO`看作一个内置函数，就像我们在第 2 章中使用的`rand`函数一样。如果你在想“嘿，函数名中不能有*空格*，你比一般的熊聪明，一会儿我会解释到底是怎么回事。在任何情况下，都需要三个参数:

*   指定数据库类型(`mysql:`)、服务器主机名(`host=hostname;`)和数据库名称(`dbname=database`)的字符串
*   您希望 PHP 使用的 MySQL 用户名
*   该用户名的 MySQL 密码

你可能还记得第二章，PHP 函数在被调用时通常会返回值。这个`new PDO`“函数”返回一个名为`PDO`对象的值，用于标识已经建立的连接。因为我们打算利用这个连接，所以我们应该通过将它存储在一个变量中来保存这个值。下面是它的样子，填充了连接到数据库所需的值:

```
$pdo = new PDO('mysql:host=mysql;dbname=ijdb', 'ijdbuser',
  'mypassword'); 
```

你大概可以看到最后两个参数是怎么回事:它们是你在本章前面创建的用户名和密码。

第一个论点有点复杂。`dbname=ijdb`部分告诉 PDO 使用名为`ijdb`的数据库(也称为*模式*)。从 PHP 运行的任何查询都默认为该模式中的表。`SELECT * FROM joke`将从`ijdb`模式的`joke`表中选择记录。

即使你已经熟悉了 PHP、PDO 和 MySQL，`host=mysql`部分看起来很混乱。通常，这将是`host=localhost`(指本地计算机，运行 PHP 的同一台机器)或指向数据库所在的特定域名，如`host=sitepoint.com`。

为什么是`host=mysql`，这里的`mysql`指的是什么？在 Docker 中，每个*服务*都有一个名字。如果您检查配置服务器的`docker-compose.yml`文件，数据库服务被称为`mysql`，在 Docker 中，一个服务可以使用另一个服务的名称连接到另一个服务。

撇开参数不谈，这里重要的是要看到由`new PDO`返回的值存储在一个名为`$pdo`的变量中。

MySQL 服务器是一个完全独立于 web 服务器的软件。因此，我们必须考虑这样的可能性:由于网络中断，或者因为您提供的用户名/密码组合被服务器拒绝，或者因为您忘记启动您的 MySQL 服务器，服务器可能不可用或无法访问！在这种情况下，`new PDO`不会运行，并且会抛出一个 PHP 异常。

注意:至少在默认情况下，PHP 可以被配置成不会抛出异常，也不会连接。这通常不是令人满意的行为，因为这使得找出问题所在变得更加困难。

如果你想知道“抛出 PHP 异常”意味着什么，请做好准备！您将会发现 PHP 语言的更多特性。

PHP **异常**是当你告诉 PHP 执行一项任务，但它无法执行时发生的情况。PHP 会尝试去做它被告知的事情，但是会失败；为了告诉你失败的原因，它会抛出一个异常。异常只不过是 PHP 崩溃并显示一个特定的错误消息。当抛出异常时，PHP 停止。出错后将不会执行任何代码行。

作为一个负责任的开发人员，您的工作就是捕捉异常并做些什么来让程序继续运行。

注意:如果你没有捕捉到一个异常，PHP 将停止运行你的 PHP 脚本并显示一个难看的错误信息。该错误消息甚至会揭示引发错误的脚本代码。在这种情况下，该代码包含您的 MySQL 用户名和密码，因此避免用户看到错误消息尤为重要！

为了捕捉异常，您应该用一个`try … catch`语句将可能抛出异常的代码括起来:

```
try {
  ⋮ do something risky
}
catch (ExceptionType $e) {
  ⋮ handle the exception
} 
```

你可以把`try … catch`语句想象成`if … else`语句，除了第二段代码是第一段代码运行失败时发生的情况。

困惑了吗？我知道我向你抛出了许多新概念(没有双关语的意思)，但是如果我把它们放在一起并向你展示我们所拥有的，那会更有意义:

```
try {
  $pdo = new PDO('mysql:host=mysql;dbname=ijdb', 'ijdbuser',
    ’mypassword’);
  $output = 'Database connection established.';
}
catch (PDOException $e) {
  $output = 'Unable to connect to the database server.';
}

include  __DIR__ . '/../templates/output.html.php'; 
```

如您所见，这段代码是一个`try … catch`语句。在顶部的`try`块中，我们尝试使用`new PDO`连接到数据库。如果成功，我们将生成的 PDO 对象存储在`$pdo`中，这样我们就可以使用新的数据库连接了。如果连接成功，`$output`变量将被设置为一条稍后显示的消息。

重要的是，在一个`try … catch`语句中，任何抛出异常后的代码都不会被执行。在这种情况下，如果连接到数据库抛出异常(可能是密码错误，或者服务器没有响应)，那么`$output`变量将永远不会被设置为“数据库连接已建立”。

如果我们的数据库连接尝试失败，PHP 将抛出一个`PDOException`，这就是`new PDO`抛出的异常类型。因此，我们的`catch`块说它将捕获一个 PDOException(并将它存储在一个名为`$e`的变量中)。在这个块中，我们设置了变量`$output`来包含一个关于出错的消息。

然而，这个错误消息并不是特别有用。它只告诉我们 PDO 无法连接到数据库服务器。最好能有一些关于为什么会这样的信息——例如，因为用户名和密码无效。

`$e`变量包含发生的异常的详细信息，包括描述问题的错误消息。我们可以使用串联将它添加到输出变量中:

```
try {
  $pdo = new PDO('mysql:host=mysql;dbname=ijdb', 'ijdbuser',
    'mypassword');
   $output = 'Database connection established.';
}
catch (PDOException $e) {
  $output = 'Unable to connect to the database server: ' . $e->getMessage();
}

include  __DIR__ . '/../templates/output.html.php'; 
```

*注意:`$e`变量不是一个字符串，而是一个**对象**。我们很快会谈到这意味着什么。但是现在，您只需要知道代码`$e->getMessage()`根据发生的特定异常得到错误消息。*

像一个`if … else`语句一样，`try … catch`语句的两个分支之一保证运行。要么成功执行`try`块中的代码，要么运行`catch`块中的代码。不管数据库连接是否成功，在`$output`变量中都会有一条消息——要么是错误消息，要么是连接成功的消息。

最后，不管`try`块是否成功，或者`catch`块是否运行，模板`output.html.php`都被包含在内。这是一个通用模板，只向页面显示一些文本:

```
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Script Output</title>
  </head>
  <body>
      <?php echo $output; ?>
  </body>
</html> 
```

完整的代码可以在**例子中找到: [MySQL-Connect](https://github.com/spbooks/phpmysql7/tree/MySQL-Connect)** 。

当包含模板时，它将显示错误消息或“数据库连接已建立”消息。

我希望前面提到的代码现在对您有一些意义。如果你迷路了，请随意回到这一节的开头，再读一遍，因为那里有一些棘手的概念。然而，一旦您牢牢掌握了代码，您可能会意识到我仍然留下了一个未解之谜:PDO。究竟什么是`new PDO`，当我说它返回一个“PDO 对象”时，究竟什么是对象？

*注意:所有下载的示例代码都包含一个名为`ijdb_sample`的模式和一个名为`ijdb_sample`的用户，因此无论您如何称呼您的模式和用户，您都可以运行它。包含数据库的文件以`database.sql`的形式提供，您可以导入该文件。*

*如果您使用提供的基于 web 的示例代码查看器，那么`idbj_sample`数据库将在您加载示例时创建，但是当您查看另一个示例时，对此模式的任何更改都将丢失。(您可以把事情弄糟，切换到另一个示例并返回会重置它，但是如果您想保留您所做的任何更改，请在您创建的模式中进行更改。)*

*如果您想使用 MySQL Workbench 将样本数据加载到您的模式中，通过选择**数据导入/恢复**从`project`目录导入`database.sql`。然后选择**从自包含文件**导入，浏览到`database.sql`，并在**默认**目标模式中选择您的模式名。如果您创建了任何同名的表，它们将被覆盖，所有记录都将丢失。*

## 面向对象编程速成班

您可能已经注意到，在前面的章节中,“对象”这个词开始出现在我的词汇表中。PDO 是 PHP 数据*对象*的扩展，`new PDO`返回一个 PDO *对象*。在这一部分，我想解释一下对象是什么。

也许你在自己对 PHP 或一般编程的探索中遇到过术语**面向对象编程(OOP)** 。OOP 是一种高级的编程风格，特别适合构建包含许多部分的非常复杂的程序。当今活跃使用的大多数编程语言都支持 OOP。有些甚至*要求*你以面向对象的方式工作。PHP 在这一点上更容易理解，让开发人员决定是否用 OOP 风格编写他们的脚本。

到目前为止，我们已经用一种叫做**过程化编程**的简单风格编写了我们的 PHP 代码，现在我们将继续这样做，稍后将更详细地研究对象。过程式风格非常适合我们目前正在处理的相对简单的项目。然而，几乎所有你会遇到的复杂项目都使用 OOP，我将在本书后面更详细地介绍它。

也就是说，我们将用来连接和使用 MySQL 数据库的 PDO 扩展是以面向对象的编程风格设计的。这意味着，不是简单地调用一个函数来连接 MySQL，然后调用使用该连接的其他函数，我们必须首先创建一个 PDO *对象*来表示我们的数据库连接，然后使用该对象的特性来处理数据库。

创建一个对象很像调用一个函数。事实上，您已经看到了如何去做:

```
$pdo = new PDO('mysql:host=mysql;dbname=ijdb', 'ijdbuser',
  'mypassword'); 
```

关键字`new`告诉 PHP 你想要创建一个新的对象。然后你留下一个空格并指定一个**类名**，它告诉 PHP 你想要创建什么类型的对象。一个**类**是 PHP 创建一个对象所遵循的一组指令。你可以把一个类想象成一个食谱，比如一个蛋糕，而一个对象是按照这个食谱制作的实际的蛋糕。不同的类可以产生不同的对象，就像不同的食谱可以产生不同的菜肴一样。

就像 PHP 自带了一堆你可以调用的内置函数一样，PHP 自带了一个类库，你可以从中创建对象。因此，`new PDO`告诉 PHP 创建一个新的`PDO`对象——即内置`PDO`类的一个新对象。

在 PHP 中，对象是一个值，就像字符串、数字或数组一样。您可以将一个对象存储在一个变量中，或者将它作为一个参数传递给一个函数——您可以对其他 PHP 值做同样的事情。然而，对象有一些有用的附加功能。

首先，对象的行为很像数组，因为它充当其他值的容器。正如我们在第 2 章中看到的，你可以通过指定数组的索引来访问数组中的值(例如，`$birthdays['Kevin']`)。说到对象，概念是相似的，但是名称和代码是不同的。我们说我们正在访问对象的一个**属性**，而不是访问存储在数组索引中的值。我们没有使用方括号来指定我们想要访问的属性的名称，而是使用了**箭头符号** ( `->` ) —例如，`$myObject->someProperty`:

```
$myObject = new SomeClass();    // create an object
$myObject->someProperty = 123;  // set a property's value
echo $myObject->someProperty;   // get a property's value 
```

数组通常用于存储一系列相似的值(比如生日数组)，而对象则用于存储一系列与*相关的*值(比如数据库连接的属性)。尽管如此，如果这就是所有对象所做的，那么它们就没有什么意义了:我们还不如用一个数组来存储这些值，对吗？当然，对象做得更多。

除了存储属性及其值的集合之外，对象还可以包含一组旨在为我们带来更多有用功能的函数。存储在对象中的函数被称为**方法**(如果你问我，这是编程世界中更容易混淆的名字之一)。方法只是类中的一个函数。更令人困惑的是，当我们开始编写自己的类时，方法是使用`function`关键字定义的！即使是有经验的开发人员也经常错误地互换使用*函数*和*方法*。

为了调用一个方法，我们再次使用箭头符号— `$myObject->someMethod()`:

```
$myObject = new SomeClass();    // create an object
$myObject->someMethod();        // call a method 
```

就像独立的函数一样，方法可以接受参数和返回值。

在这个阶段，这可能听起来有点复杂和没有意义，但是请相信我:将变量(属性)和函数(方法)的集合整合到称为对象的小捆绑包中，会使某些任务的代码更加整洁和易读——使用数据库只是其中之一。有一天，您甚至可能想要开发自定义类，用于创建您自己设计的对象。

然而现在，我们将坚持使用 PHP 附带的类。让我们继续使用我们已经创建的`PDO`对象，看看通过调用它的一个方法能做些什么。

### 配置连接

到目前为止，我已经向您展示了如何创建一个`PDO`对象来建立与 MySQL 数据库的连接，以及如何在出错时显示一条有意义的错误消息:

```
<?php
try {
    $pdo = new PDO('mysql:host=mysql;dbname=ijdb', 'ijdbuser', 'mypassword');
    $output = 'Database connection established.';
} catch (PDOException $e) {
    $output = 'Unable to connect to the database server: ' . $e->getMessage();
}

include  __DIR__ . '/../templates/output.html.php'; 
```

但是，假设连接成功，您需要在使用之前对其进行配置。您可以通过调用新的`PDO`对象的一些方法来配置您的连接。

在向数据库发送查询之前，我们需要配置数据库连接的字符编码。正如我在第二章中简单提到的，你应该在你的网站上使用 UTF-8 编码的文本，当用户在你的网站上填写表单时，最大化用户可以使用的字符范围。默认情况下，当 PHP 连接到 MySQL 时，它使用更简单的 ISO-8859-1(或 Latin-1)编码，而不是 UTF-8。如果我们让它保持原样，我们将不容易插入中文、阿拉伯文或大多数非英文字符。

即使你 100%确定你的网站只会被说英语的人使用，不设置字符集也会导致其他问题。如果你的网页没有设置为 UTF-8，当人们在文本框中输入某些字符如引号`”`时，你会遇到问题，因为它们会以不同的字符出现在数据库中。

因此，我们现在需要设置新的`PDO`对象来使用 UTF-8 编码。

我们可以通过将`;charset=utf8mb4`附加到连接字符串来指示 PHP 在查询数据库时使用 UTF-8。如果您的 PHP 脚本也作为`UTF-8`(这是最近的 PHP 版本中的默认设置)被发送到浏览器，那么这样做没有什么坏处:

```
$pdo = new PDO('mysql:host=mysql;dbname=ijdb;charset=utf8mb4', 'ijdbuser',
  'mypassword'); 
```

*注意:如果你去搜索，你会发现设置字符集的不同方法，这本书的早期版本指导你使用这个代码:*

```
$pdo->exec('SET NAMES "utf8"');
```

这是因为在 PHP 5.3.6 之前，PHP 没有正确应用 charset 选项。因为这在您实际使用的任何 PHP 版本中都是固定的，所以将字符集设置为连接字符串的一部分是首选选项。

因此，我们用来连接 MySQL 并配置该连接的完整代码如下所示。

**举例:[MySQL-Connect-Complete](https://github.com/spbooks/phpmysql7/tree/MySQL-Connect-Complete)**

```
<?php
try {
    $pdo = new PDO('mysql:host=mysql;dbname=ijdb;charset=utf8mb4', 'ijdbuser', 'mypassword');
    $output = 'Database connection established.';
} catch (PDOException $e) {
    $output = 'Unable to connect to the database server: ' . $e->getMessage();
}

include  __DIR__ . '/../templates/output.html.php'; 
```

在你的浏览器中启动这个例子。(如果您已经将数据库代码放在了`public`目录下的`index.php`中，并将`output.html.php`文件放在了`templates`目录下，那么页面的 URL 将是`https://v.je/`。)

如果您的服务器启动并运行，并且一切正常，您应该会看到一条指示成功的消息。

![A successful connection](img/d7deb4654c6af1e52f3e7637534dd3c3.png)

如果 PHP 无法连接到您的 MySQL 服务器，或者如果您提供的用户名和密码不正确，您将会看到如下所示的类似屏幕。为了确保您的错误处理代码正常工作，您可能想要故意拼错密码来测试它。

![A connection failure](img/225eb36bb96e2844803adba29da7542d.png)

多亏了我们的`catch`块，来自数据库的错误消息已经包含在页面上:

```
catch (PDOException $e) {
  $output = 'Unable to connect to the database server: ' . $e->getMessage();
} 
```

方法`getMessage()`返回描述所发生的异常的消息。还有一些其他的方法——包括`getFile()`和`getLine()`——用于返回抛出异常的文件名和行号。您可以生成如下非常详细的错误消息:

```
catch (PDOException $e) {
  $output = 'Unable to connect to the database server: ' . $e->getMessage() . ' in ' .
  $e->getFile() . ':' . $e->getLine();
} 
```

如果你有一个包含几十个文件的大型网站，这是非常有用的。错误消息将告诉您应该查看哪个文件以及错误发生在哪一行。

如果您很好奇，请尝试在数据库连接代码中插入一些其他错误(例如，拼错的数据库名称)，并观察由此产生的详细错误消息。完成后，数据库连接正常工作，返回到简单的错误消息。这样，如果您的数据库服务器出现真正的问题，您的访问者就不会受到技术官样文章的狂轰滥炸。

建立连接并选择数据库后，就可以开始使用存储在数据库中的数据了。

*您可能想知道脚本执行完毕后，与 MySQL 服务器的连接会发生什么。如果你真的想的话，你可以通过丢弃代表你的连接的`PDO`对象来强制 PHP 与服务器断开连接。您可以通过将包含对象的变量设置为`null` :* 来实现这一点

```
$pdo = null;  // disconnect from the database server
```

也就是说，PHP 在运行完脚本后会自动关闭任何打开的数据库连接，所以你通常可以让 PHP 在你之后清理。

## 用 PHP 发送 SQL 查询

在第 3 章中，我们使用 MySQL Workbench 连接到 MySQL 数据库服务器，它允许我们键入 SQL 查询(命令)并立即查看这些查询的结果。`PDO`对象提供了类似的机制 exec 方法:

```
$pdo->exec($query) 
```

这里，`$query`是一个字符串，包含您想要执行的任何 SQL 查询。

如您所知，如果在执行查询时出现问题(例如，如果您在 SQL 查询中犯了一个输入错误)，这个方法将抛出一个`PDOException`让您捕捉。

考虑下面的例子，它试图产生我们在第 3 章中创建的笑话表。

**举例:[MySQL-Create](https://github.com/spbooks/phpmysql7/tree/MySQL-Create)**

```
try {
  $pdo = new PDO('mysql:host=mysql;dbname=ijdb;charset=utf8mb4', 'ijdbuser', 'mypassword');

  $sql = 'CREATE TABLE joke (
    id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    joketext TEXT,
    jokedate DATE NOT NULL
  ) DEFAULT CHARACTER SET utf8 ENGINE=InnoDB';

  $pdo->exec($sql);

  $output = 'Joke table successfully created.';
}
catch (PDOException $e) {
  $output = 'Database error: ' . $e->getMessage() . ' in ' .
  $e->getFile() . ':' . $e->getLine();
}

include  __DIR__ . '/../templates/output.html.php'; 
```

注意，我们使用相同的`try … catch`语句技术来处理查询可能产生的错误。可以使用多个`try … catch`块来显示不同的错误消息——一个用于连接，一个用于查询——但是这会导致大量的额外代码。

相反，我选择使用相同的`try`语句来包含连接和查询。一旦出现错误，`try … catch`块将停止执行代码，因此，如果在数据库连接期间出现错误，`$pdo->exec($run)`行将永远不会运行，从而确保如果向数据库发送查询，连接一定已经建立。

这种方法使我们对显示的错误消息的控制更少，但是省去了为每个数据库操作键入一个`try … catch`语句。在本书的后面，我们将把这些分成不同的块，但是现在，把所有的数据库操作放在同一个`try`块中。

这个例子还使用了`getMessage`方法从 MySQL 服务器中检索详细的错误消息。下图显示了当笑话表已经存在时显示的错误。

![The CREATE TABLE query fails because the table already exists](img/4fa9becef58b32d0da62857d300fe20c.png)

对于`DELETE`、`INSERT`和`UPDATE`查询(用于修改存储的数据)，`exec`方法返回受查询影响的表行数(条目数)。考虑下面的 SQL 命令，我们在第 3 章中使用它来设置所有包含单词“程序员”的笑话的日期。

**举例:[MySQL-更新](https://github.com/spbooks/phpmysql7/tree/MySQL-Update)**

```
try {
  $pdo = new PDO('mysql:host=mysql;dbname=ijdb;charset=utf8mb4', 'ijdbuser', 'mypassword');

  $sql = 'UPDATE joke SET jokedate="2021-04-01"
      WHERE joketext LIKE "%programmer%"';

  $affectedRows = $pdo->exec($sql);

  $output = 'Updated ' . $affectedRows .' rows.';
}
catch (PDOException $e) {
  $output = 'Database error: ' . $e->getMessage() . ' in ' .
  $e->getFile() . ':' . $e->getLine();
}

include  __DIR__ . '/../templates/output.html.php'; 
```

通过将 exec 方法返回的值存储在`$affectedRows`中，我们可以使用`$output`变量中的变量在模板中进行打印。

下图显示了这个例子的输出，假设你的数据库中只有一个“程序员”的笑话。

![The number of database records updated is displayed](img/c9031a9d28a6fe8e7b7c46ad5feed928.png)

如果刷新页面以再次运行相同的查询，应该会看到消息发生变化，如下图所示。它表示没有行被更新，因为应用于笑话的新日期与现有日期相同。

![MySQL lets you know when you’re wasting its time](img/96bdcf0bfe6a224d9fb0e79b26e4ceed.png)

查询的处理略有不同，因为它们可以检索大量数据，PHP 提供了处理这些信息的方法。

## 处理`SELECT`结果集

对于大多数 SQL 查询来说，`exec`方法工作得很好。该查询对您的数据库做了一些事情，您可以从该方法的返回值中获得受影响的行数(如果有的话)。`SELECT`然而，查询需要比`exec`更奇特的东西。您还记得`SELECT`查询用于查看数据库中存储的数据。不仅仅影响数据库，`SELECT`查询有结果——我们需要一个方法来返回它们。

查询方法看起来就像`exec`，因为它接受一个 SQL 查询作为要发送到数据库服务器的参数。然而，它返回的是一个`PDOStatement`对象，表示一个**结果集**，包含查询返回的所有行(条目)的列表:

```
<?php
try {
    $pdo = new PDO('mysql:host=mysql;dbname=ijdb;charset=utf8mb4', 'ijdbuser', 'mypassword');

    $sql = 'SELECT `joketext` FROM `joke`';
    $result = $pdo->query($sql);
} catch (PDOException $e) {
    $error = 'Unable to connect to the database server: ' . $e->getMessage() . ' in ' .
  $e->getFile() . ':' . $e->getLine();
} 
```

假设在处理查询时没有遇到错误，这段代码会将结果集(以`PDOStatement`对象的形式)存储到变量`$result`中。这个结果集包含存储在`joke`表中的所有笑话的文本。由于对数据库中笑话的数量没有实际限制，结果集可能会很大。

我在第 2 章提到过，当我们需要循环但不知道循环多少次时,`while`循环是一个有用的控制结构。我们不能使用`for`循环，因为我们不知道查询返回了多少条记录。实际上，您可以在这里使用一个`while`循环，一次处理结果集中的一行:

```
while ($row = $result->fetch()) {
  ⋮ process the row
} 
```

`while`循环的条件可能与你习惯的条件不同，所以让我解释一下它是如何工作的。将条件视为一个单独的语句:

```
$row = $result->fetch(); 
```

`PDOStatement`对象的`fetch`方法将结果集中的下一行作为数组返回(我们在第 2 章讨论了数组)。当结果集中没有更多的行时，`fetch`返回`false`。(在这种情况下，要求一个 PDO 对象做它不能做的事情——因为当结果集中没有剩余的行时,`fetch`不能返回下一行——会导致 *not* 抛出`PDOException`。如果是这样，我们就不能像这里一样在`while`循环条件中使用`fetch`方法。)

现在，上面的语句给变量`$row`赋值，但是，同时，整个语句取相同的值。这就是让您在`while`循环中使用语句作为条件的原因。由于一个`while`循环将一直循环，直到它的条件评估为`false`，所以这个循环将会出现与结果集中的行数一样多的次数，每次循环执行时`$row`都将采用下一行的值。剩下要解决的就是每次循环运行时如何从`$row`变量中检索值。

由`fetch`返回的结果集的行被表示为关联数组，其索引以结果集中的表列命名。如果`$row`是结果集中的一行，`$row['joketext']`是该行的`joketext`列中的值。

我们在这段代码中的目标是存储所有笑话的文本，以便我们可以在 PHP 模板中显示它们。最好的方法是将每个笑话作为一个新条目存储在一个数组中，`$jokes`:

```
while ($row = $result->fetch()) {
  $jokes[] = $row['joketext'];
} 
```

随着笑话从数据库中提取出来，我们现在可以将它们传递给 PHP 模板`jokes.html.php`。

总结一下，到目前为止，这个例子的控制器代码如下:

```
<?php

try {
    $pdo = new PDO('mysql:host=mysql;dbname=ijdb;charset=utf8mb4', 'ijdbuser', 'mypassword');

    $sql = 'SELECT `joketext` FROM `joke`';
    $result = $pdo->query($sql);

    while ($row = $result->fetch()) {
        $jokes[] = $row['joketext'];
    }
} catch (PDOException $e) {
    $error = 'Unable to connect to the database server: ' . $e->getMessage() . ' in ' .
  $e->getFile() . ':' . $e->getLine();
}

include  __DIR__ . '/../templates/jokes.html.php'; 
```

`$jokes`变量是一个存储笑话列表的数组。如果你用 PHP 写出数组的内容，它看起来会像这样:

```
$jokes = [];
$jokes[0] = 'A programmer was found dead in the shower. The instructions read: lather, rinse, repeat.';
$jokes[1] = '!false - it\'s funny because it\'s true';
$jokes[2] = 'A programmer\'s wife tells him to go to the store and "get a gallon of milk, and if they have eggs, get a dozen." He returns with 13 gallons of milk.'; 
```

但是，数据是从数据库中检索出来的，而不是在代码中手工键入的。

您会注意到，根据`try`块是否成功执行，设置了两个不同的变量— `$jokes`和`$error`。

在`jokes.html.php`模板中，我们需要显示`$jokes`数组的内容或者包含在`$error`变量中的错误信息。

为了检查变量是否被赋值，我们可以使用前面用来检查表单是否被提交的`isset`函数。模板可以包含一个`if`语句来决定是显示错误还是笑话列表:

```
if (isset($error)) {
  ?>
  <p>
  <?php
  echo $error;
  ?>
  </p>
}
else {
  : display the jokes
} 
```

这里没有什么新东西，但是为了显示笑话，我们需要显示`$jokes`数组的内容。与我们到目前为止使用的其他变量不同，`$jokes`数组不仅仅包含一个值。

在 PHP 中处理数组最常见的方法是使用循环。我们已经看到了`while`循环和`for`循环。`foreach`循环对于处理数组特别有用:

```
foreach (array as $item) {
  ⋮ process each $item
} 
```

一个`foreach`循环顶部的括号包含一个数组，后跟关键字`as`，然后是一个新变量的名称，该变量将用于依次存储数组中的每一项，而不是一个条件。然后对数组中的每一项执行一次循环体。每次该项都存储在指定的变量中，以便代码可以直接访问它。

在 PHP 模板中使用`foreach`循环来依次显示数组中的每一项是很常见的。下面是我们的`$jokes`数组可能的样子:

```
<?php
foreach ($jokes as $joke) {
    ?>
  ⋮ HTML code to output each $joke
<?php
}
?> 
```

混合使用 PHP 代码来描述循环和 HTML 代码来显示循环，代码看起来相当混乱。因此，当在模板中使用`foreach`循环时，通常使用另一种方式来编写它:

```
foreach (array as $item):
  ⋮ process each $item
endforeach; 
```

这两段代码在功能上是相同的，但是后者在与 HTML 代码混合时看起来更友好。下面是这种形式的代码在模板中的样子:

```
<?php foreach ($jokes as $joke): ?>
  ⋮ HTML code to output each $joke
<?php endforeach; ?> 
```

同样的事情也可以用`if`语句来完成，通过避免使用大括号来更好地查看 HTML 模板内部:

```
<?php if (isset($error)): ?>
  <p>
  <?php echo $error; ?>
  </p>
<?php else: ?>
  : display the jokes
<?php endif; ?> 
```

有了这些新工具，我们可以编写模板来显示笑话列表。

**举例:[MySQL-list 笑话](https://github.com/spbooks/phpmysql7/tree/MySQL-ListJokes)**

```
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <title>List of jokes</title>
  </head>
  <body>
  <?php if (isset($error)): ?>
  <p>
    <?php echo $error; ?>
  </p>
  <?php else: ?>
  <?php foreach ($jokes as $joke): ?>
  <blockquote>
    <p>
    <?php echo htmlspecialchars($joke, ENT_QUOTES, 'UTF-8') ?>
    </p>
  </blockquote>
  <?php endforeach; ?>
  <?php endif; ?>
  </body>
</html> 
```

要么是文本显示在页面上，要么是每个笑话显示在一个段落(`<p>`)中，该段落包含在一个引号(`<blockquote>`)内，因为我们实际上是在引用页面中每个笑话的作者。

因为笑话可能包含可以被解释为 HTML 代码的字符(例如，`<`、`>`或`&`)，所以我们必须使用`htmlspecialchars`来确保它们被翻译成 HTML 字符实体(即，`&lt;`、`&gt;`和`&amp;`)，以便正确显示。

下图显示了在数据库中添加了几个笑话后，该页面的外观。

![A list of jokes from the database](img/d4eb1d3c921146437d6aae21144fa488.png)

*还记得我们如何在控制器中使用`while`循环从`PDOStatement`结果集中一次取出一行吗？*

```
while ($row = $result->fetch()) {
  $jokes[] = $row['joketext'];
}
```

*原来`PDOStatement`对象被设计成当你把它们传递给`foreach`循环时，它们的行为就像数组一样。因此，您可以使用`foreach`循环而不是`while`循环:*来稍微简化您的数据库处理代码

```
foreach ($result as $row) {
  $jokes[] = $row['joketext'];
}
```

在本书的其余部分，我将使用这种更整洁的形式。

PHP 提供的另一个简洁的工具是调用`echo`命令的快捷方式——正如您已经看到的，我们需要经常使用它。我们的`echo`语句看起来像这样:

```
<?php echo $variable; ?> 
```

相反，我们可以用这个:

```
<?=$variable?> 
```

这是完全一样的。`<?=`的意思是`echo`,给你一个稍微短一点的打印变量的方法。不过，这有一个限制:如果你使用`<?=`，你只能打印。您不能包含`if`语句、`for`语句等等，尽管您可以使用串联，并且它后面可以跟一个函数调用。

这里有一个使用简写 echo 的更新模板。

**举例:[MySQL-list 笑话-速记](https://github.com/spbooks/phpmysql7/tree/MySQL-ListJokes-Shorthand)**

```
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <title>List of jokes</title>
  </head>
  <body>
  <?php if (isset($error)): ?>
  <p>
    <?=$error?>
  </p>
  <?php else: ?>
  <?php foreach ($jokes as $joke): ?>
  <blockquote>
    <p>
    <?=htmlspecialchars($joke, ENT_QUOTES, 'UTF-8')?>
    </p>
  </blockquote>
  <?php endforeach; ?>
  <?php endif; ?>
  </body>
</html> 
```

从现在开始，我将使用适用的简写符号。

*注意:在 PHP 5.4 之前的版本中，这种简写符号需要启用一个相当不常见的 PHP 设置，因此出于兼容性原因，不鼓励使用。使用简写符号可能会导致您的代码在从启用了简写符号的服务器转移到未启用简写符号的服务器时停止工作。*

从 PHP 5.4 开始(所以这些天你实际上会遇到的任何版本)，无论 PHP 设置如何，速记 echo 都可以工作，所以你可以安全地使用它，不用担心它可能无法在所有服务器上工作。

## 提前思考

在我们刚刚看到的例子中，我们创建了一个模板`jokes.html.php`，它包含了显示页面所需的所有 HTML。然而，随着我们网站的发展，我们会添加更多的页面。我们当然希望有一个页面，让人们能够在网站上添加笑话，我们还需要一个带有一些介绍性文字的主页，一个带有所有者联系方式的页面，随着网站的发展，甚至可能需要一个人们可以登录 T2 网站的页面。

我在这里有点超前了，但是考虑一个项目如何成长总是值得的。如果我们将刚刚用于`jokes.html.php`的方法应用于其余的模板——`addjoke.html.php`、`home.html.php`、`contact.html.php`、`login.html.php`等等——我们最终会得到许多重复的代码。

网站上的每个页面都需要一个类似这样的模板:

```
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <title>IJDB - Internet Joke Database</title>
  </head>
  <body>
  <?php if (isset($error)): ?>
  <p>
    <?=$error?>
  </p>
  <?php else: ?>
    : do whatever is required for this page: show text,
    : show a form, list records from the database, etc.
  <?php endif; ?>
  </body>
</html> 
```

作为程序员，重复代码是你能做的最糟糕的事情之一。事实上，程序员经常提到的**干**原则，代表着“不要重复自己”。如果您发现自己在重复代码段，几乎肯定有更好的解决方案。

所有最好的程序员都很懒，重复代码就意味着重复工作。使用这种复制/粘贴模板的方法使得网站很难维护。让我们想象一下，有一个页脚和一个导航部分，我们希望出现在每一页上。我们的模板现在看起来像这样:

```
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <title>IJDB - Internet Joke Database</title>
  </head>
  <body>
  <nav>
    <ul>
      <li><a href="index.php">Home</a></li>
      <li><a href="jokes.php">Jokes List</a></li>
    </ul>
  </nav>

  <main>
  <?php if (isset($error)): ?>
  <p>
    <?=$error?>
  </p>
  <?php else: ?>
    : do whatever is required for this page: show text,
    : show a form, list jokes, etc.
  <?php endif; ?>
  </main>

  <footer>
  &copy; IJDB 2021
  </footer>
  </body>
</html> 
```

2022 年我们会遇到问题！如果网站上所有页面的模板(例如，`jokes.html.php``addjoke.html.php``home.html.php``contact.html.php`和`login.html.php`)包含上述结构中的代码，要将版权声明中的年份更新为“2022”，您需要打开每个模板并更改日期。

如果你好奇的话，我们可以聪明地从服务器的时钟中动态读取日期。)来避免这个问题，但是如果我们想要添加一个包含在每个页面上的`<script>`标签呢？或者给菜单添加一个新的链接？我们仍然需要打开每个模板文件并修改它！

更改五六个模板可能有点烦人，但不会造成太大的问题。但是，如果网站增长到几十页或者几百页呢？每次你想添加一个链接到菜单，你必须打开每一个模板并改变它。

这个问题*可以用一系列的`include`语句*来解决。例如:

```
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <title>IJDB - Internet Joke Database</title>
  </head>
  <body>
  <nav>
    <?php include 'nav.html.php'; ?>
  </nav>

  <main>
  <?php if (isset($error)): ?>
  <p>
    <?=$error?>
  </p>
  <?php else: ?>
    : do whatever is required for this page: show text,
    : show a form, list jokes, etc.
  <?php endif; ?>

  </main>

  <footer>
  <?php include 'footer.html.php'; ?>
  </footer>
  </body>
</html> 
```

但是这种方法需要先知先觉:我们需要准确预测未来可能需要做出什么样的改变，并在我们预见将会发生改变的地方使用相关的`include`语句。

例如，在上面的例子中，通过将菜单项添加到`nav.html.php`很容易添加新的菜单项，但是向每个页面添加一个`<script>`标签，或者甚至像向`nav`元素添加一个 CSS 类这样琐碎的事情，仍然意味着打开每个模板进行更改。

没有办法准确预测网站生命周期中可能需要的所有变化，所以我在本章开始向你展示的方法实际上更好:

```
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <link rel="stylesheet" href="jokes.css">
    <title><?=$title?></title>
  </head>
  <body>

  <header>
    <h1>Internet Joke Database</h1>
  </header>
  <nav>
    <ul>
      <li><a href="index.php">Home</a></li>
      <li><a href="jokes.php">Jokes List</a></li>
    </ul>
  </nav>

  <main>
  <?=$output?>
  </main>

  <footer>
  &copy; IJDB 2021
  </footer>
  </body>
</html> 
```

如果我们总是包含这个模板，我们称之为`layout.html.php`，可以将`$output`变量设置为某个 *HTML 代码*，并让它出现在带有导航和页脚的页面上。这样做的好处是，要更改网站每个页面上的日期，我们只需要在一个位置更改它。

我还偷偷放入了一个`$title`变量，这样每个控制器都可以定义一个出现在`<title>`和`</title>`标签之间的值，以及一些 CSS(在示例代码中作为`jokes.css`提供)来使页面更漂亮一些。

![IJDB — now with CSS styles](img/51af3eeffc580ae46b7f0f1bebe5530b.png)

任何控制器现在都可以使用`include __DIR__ . '/../templates/layout.html.php';`并为`$output`和`$title`提供值。

我们使用`layout.html.php`的`jokes.php`编码如下。

**举例:[MySQL-list 笑话-Layout-1](https://github.com/spbooks/phpmysql7/tree/MySQL-ListJokes-Layout-1)**

```
<?php

try {
    $pdo = new PDO('mysql:host=mysql;dbname=ijdb;charset=utf8mb4', 'ijdbuser', 'mypassword');

    $sql = 'SELECT `joketext` FROM `joke`';
    $result = $pdo->query($sql);

    while ($row = $result->fetch()) {
        $jokes[] = $row['joketext'];
    }

    $title = 'Joke list';

    $output = '';

    foreach ($jokes as $joke) {
        $output .= '<blockquote>';
        $output .= '<p>';
        $output .= $joke;
        $output .= '</p>';
        $output .= '</blockquote>';
    }
} catch (PDOException $e) {
    $title = 'An error has occurred';

    $output = 'Database error: ' . $e->getMessage() . ' in ' .
  $e->getFile() . ':' . $e->getLine();
}

include  __DIR__ . '/../templates/layout.html.php'; 
```

但是等等！`try`块里的`$output`是怎么回事？`$output`变量实际上包含了一些 HTML 代码:循环构建了一个包含笑话列表 HTML 代码的字符串。

原则上，这是我们希望发生的事情:`$output`变量包含 HTML 代码，该代码将被插入到`layout.html.php`的导航和页脚之间，但是我想你会同意该代码非常难看。

我已经向您展示了如何通过`include`语句避免混合 HTML 和 PHP 代码。就像我们之前做的那样，将用于显示笑话的 HTML 移动到它自己的文件中是很好的——但是这一次，只有笑话列表页面特有的 HTML 代码。

`templates`目录中的`jokes.html.php`应包含此代码:

```
<?php foreach ($jokes as $joke): ?>
<blockquote>
  <p>
  <?=htmlspecialchars($joke, ENT_QUOTES, 'UTF-8')?>
  </p>
</blockquote>
<?php endforeach; ?> 
```

重要的是，这只是显示笑话的代码。它不包含导航、页脚、`<head>`标签或任何我们希望在每一页上重复的内容；这只是笑话列表页面特有的 HTML 代码。

要使用此模板，您可以尝试以下方法:

```
 while ($row = $result->fetch()) {
    $jokes[] = $row['joketext'];
  }

  $title = 'Joke list';

  include 'jokes.html.php';
} 
```

或者如果你很聪明:

```
 while ($row = $result->fetch()) {
    $jokes[] = $row['joketext'];
  }

  $title = 'Joke list';

  $output = include 'jokes.html.php';
} 
```

用这种方法，你的逻辑将是完全合理的。我们需要包括`jokes.html.php`。不幸的是，`include`语句只是在被调用时执行包含文件中的代码。如果您运行上面的代码，输出实际上会是这样的:

```
<blockquote>
  <p>
  A programmer was found dead in the shower. The instructions read: lather, rinse, repeat.
  </p>
</blockquote>
<blockquote>
  <p>
  !false - it's funny because it's true
  </p>
</blockquote>
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Joke List</title>
  </head>
  <body>
  … 
```

因为首先包含了`jokes.html.php`，所以它首先被发送到浏览器。我们需要做的是加载`jokes.html.php`，但不是将输出直接发送到浏览器，我们需要捕获它并将其存储在`$output`变量中，以便稍后由`layout.html.php`使用。

`include`语句不返回值，所以`$output = include 'jokes.html.php';`没有预期的效果，PHP 也没有替代语句来做这件事。然而，这并不意味着这是不可能的。

PHP 确实有一个有用的特性叫做“输出缓冲”。这听起来可能很复杂，但是这个概念实际上非常简单:当你使用`echo`来打印一些东西，或者使用`include`来包含一个包含 HTML 的文件时，通常它会被直接发送到浏览器。通过利用输出缓冲，而不是将输出直接发送到浏览器，HTML 代码存储在服务器上的一个“缓冲区”中，这个缓冲区基本上只是一个字符串，包含到目前为止打印的所有内容。

更好的是，PHP 允许您随时打开缓冲区并读取其内容。

我们需要两个函数:

*   `ob_start()`，启动输出缓冲器。调用这个函数后，通过`echo`打印的任何东西或者通过`include`打印的 HTML 都将存储在一个缓冲区中，而不是发送到浏览器。
*   `ob_get_clean()`，返回缓冲区内容并清零。

您可能已经猜到，函数名中的“ob”代表“输出缓冲区”。

要捕获包含文件的内容，我们只需要利用这两个函数:

```
while ($row = $result->fetch()) {
   $jokes[] = $row['joketext'];
}

$title = 'Joke list';

// Start the buffer

ob_start();

// Include the template. The PHP code will be executed,
// but the resulting HTML will be stored in the buffer
// rather than sent to the browser.

include  __DIR__ . '/../templates/jokes.html.php';

// Read the contents of the output buffer and store them
// in the $output variable for use in layout.html.php

$output = ob_get_clean();
} 
```

当这段代码运行时，`$output`变量将包含在`jokes.html.php`模板中生成的 HTML。

从现在开始，我们将使用这种方法。每个页面将由两个模板组成:

*   `layout.html.php`，包含了每个页面需要的所有常用 HTML
*   一个唯一的模板，只包含特定页面的 HTML 代码

完整的`jokes.php`看起来是这样的:

```
try {
  $pdo = new PDO('mysql:host=mysql;dbname=ijdb;charset=utf8mb4', 'ijdbuser', 'mypassword');

  $sql = 'SELECT `joketext` FROM `joke`';
  $result = $pdo->query($sql);

  while ($row = $result->fetch()) {
     $jokes[] = $row['joketext'];
  }

  $title = 'Joke list';

  ob_start();

  include  __DIR__ . '/../templates/jokes.html.php';

  $output = ob_get_clean();
}
catch (PDOException $e) {
  $title = 'An error has occurred';

  $output = 'Database error: ' . $e->getMessage() . ' in ' .
  $e->getFile() . ':' . $e->getLine();
}

include  __DIR__ . '/../templates/layout.html.php'; 
```

让我们通过添加一个`index.php`文件来使“Home”链接工作。我们可以在这个页面上放任何东西:最新的笑话，本月最好的笑话，或者任何我们喜欢的东西。不过现在，我们将保持简单，只显示一条信息“欢迎来到互联网笑话数据库”。

在`templates`文件夹中创建一个名为`home.html.php`的文件:

```
<h2>Internet Joke Database</h2>

<p>Welcome to the Internet Joke Database</p> 
```

我们的`index.php`比`jokes.html.php`简单多了。它不从数据库中获取任何信息，所以它不需要数据库连接，我们也不需要`try … catch`语句，所以我们只需加载两个模板并设置`$title`和`$output`变量。

**举例:[MySQL-list 笑话-布局-3](https://github.com/spbooks/phpmysql7/tree/MySQL-ListJokes-Layout-3)**

```
<?php

$title = 'Internet Joke Database';

ob_start();

include  __DIR__ . '/../templates/home.html.php';

$output = ob_get_clean();

include  __DIR__ . '/../templates/layout.html.php'; 
```

注意:如果需要的话，最好只连接到数据库。数据库是许多网站上最常见的性能瓶颈，因此最好尽可能少地建立连接。

测试这两个页面在您的浏览器中是否正常工作。当你访问`https://v.je/jokes.php`时，你应该有一个可见的笑话列表和`https://v.je/jokes.php`上的欢迎信息。两个页面都应该包含导航和页脚。

尝试修改`layout.html.php`。您所做的更改将出现在两个页面上。如果网站有几十个页面，布局的改变会影响到每个页面。

## 将数据插入数据库

在这一节中，我将演示如何使用您所掌握的工具，使站点访问者能够将自己的笑话添加到数据库中。

如果你想让你的站点访问者输入新的笑话，你显然需要一个表单。这里有一个符合要求的表单模板:

```
<form action="" method="post">
  <label for="joketext">Type your joke here:</label>
  <textarea id="joketext" name="joketext" rows="3" cols="40">
  </textarea>
  <input type="submit" name="submit" value="Add">
</form> 
```

将此作为`addjoke.html.php`保存在`templates`目录中。

`<form>`元素最重要的部分是`action`属性。`action`属性告诉浏览器表单提交后将数据发送到哪里。这可以是文件名，比如`"addjoke.php"`。

但是，如果您通过将属性设置为`""`而将其留空，用户提供的数据将被发送回您当前正在查看的页面。如果浏览器的 URL 显示页面为`addjoke.php`，当用户按下**添加**按钮时，数据将被发送到该页面。

让我们将这个表单与前面的例子联系起来，前面的例子显示了数据库中的笑话列表。打开`layout.html.php`，添加一个链接到`addjoke.php`的“添加新笑话”链接的 URL:

```
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <link rel="stylesheet" href="jokes.css">
    <title><?=$title?></title>
  </head>
  <body>
  <nav>
    <header>
      <h1>Internet Joke Database</h1>
    </header>
    <ul>
      <li><a href="index.php">Home</a></li>
      <li><a href="jokes.php">Jokes List</a></li>
      <li><a href="addjoke.php">Add a new Joke</a></li>
    </ul>
  </nav>

  <main>
  <?=$output?>
  </main>

  <footer>
  &copy; IJDB 2021
  </footer>
  </body>
</html> 
```

当您打开`layout.html.php`时，包含第 2 章中的`form.css`样式表，就像我上面做的那样。现在，布局中显示的任何表单都将具有我们以前使用的样式。

提交该表单时，请求将包含一个变量— `joketext` —它包含键入文本区域的笑话文本。这个变量将出现在 PHP 创建的`$_POST`数组中。

让我们在`public`目录中创建`addjoke.php`。该控制器的基本逻辑是:

*   如果没有设置`joketext` POST 变量，显示一个表格。
*   否则，将提供的笑话插入数据库。

创建这个骨架`addjoke.php`:

```
<?php
if (isset($_POST['joketext'])) {
    try {
        $pdo = new PDO('mysql:host=mysql;dbname=ijdb;charset=utf8mb4', 'ijdbuser', 'mypassword');
    } catch (PDOException $e) {
        $title = 'An error has occurred';

        $output = 'Database error: ' . $e->getMessage() . ' in ' .
    $e->getFile() . ':' . $e->getLine();
    }
} else {
    $title = 'Add a new joke';

    ob_start();

    include  __DIR__ . '/../templates/addjoke.html.php';

    $output = ob_get_clean();
}
include  __DIR__ . '/../templates/layout.html.php'; 
```

这个开始的`if`语句检查`$_POST`数组是否包含一个名为`joketext`的变量。如果设置好了，表单就已经提交了。否则，来自`addjoke.html.php`的表单被加载到`$output`变量中，以便在浏览器中显示。

如果此时你确实在浏览器中打开了`addjoke.php`，你会看到这个表单，但是输入一个笑话并按下**添加**不起作用，因为我们还没有对包含在`$_POST['joketext']`中的数据做任何事情。

要将提交的笑话插入数据库，我们必须使用存储在`$_POST['joketext']`中的值执行一个`INSERT`查询，以填充`joke`表的`joketext`列。这可能会导致您编写如下代码:

```
$sql = 'INSERT INTO `joke` SET
  `joketext` ="' . $_POST['joketext'] . '",
  `jokedate` ="2021-02-04"';

$pdo->exec($sql); 
```

然而，这段代码有一个严重的问题:`$_POST['joketext']`的内容完全由提交表单的用户控制。如果一个恶意用户在表单中输入一些讨厌的 SQL 代码，这个脚本会毫无疑问地把它输入到您的 MySQL 服务器。这种类型的攻击被称为 **SQL 注入攻击**，在 PHP 的早期，这是黑客在基于 PHP 的网站中发现和利用的最常见的安全漏洞之一。(在许多编程领域，SQL 注入攻击仍然惊人地有效，因为开发人员没有预料到它们。考虑一下这个让交通摄像头放弃数据库的非凡尝试:“ [SQL 注入车牌希望挫败欧洲交通摄像头](https://gizmodo.com/sql-injection-license-plate-hopes-to-foil-euro-traffic-5498412)”。)

用户可以在文本框中键入以下内容:

```
How many programmers does it take to screw in a lightbulb? None. It's a hardware problem. 
```

发送到数据库的查询如下:

```
INSERT INTO `joke` SET
  `joketext` ="How many programmers does it take to screw in a lightbulb? None. It's a hardware problem.",
  `jokedate` ="2021-02-04 
```

但是，如果用户键入以下内容会怎么样呢:

```
A programmer's wife tells him to go to the store and "get a gallon of milk, and if they have eggs, get a dozen." He returns with 13 gallons of milk. 
```

在这种情况下，发送到数据库的查询将是:

```
INSERT INTO `joke` SET
  `joketext`="A programmer's wife tells him to go to the store and "get a gallon of milk,
and if they have eggs, get a dozen." He returns with 13 gallons of milk.",
  `jokedate`="2021-02-04 
```

因为笑话包含引号字符，MySQL 将返回一个错误，因为它会将“get”前的引号视为字符串的结尾。

为了使这个查询有效，我们需要对文本中的所有引号进行转义，以便发送到数据库的查询变成这样:

```
INSERT INTO `joke` SET
  `joketext`="A programmer's wife tells him to go to the store and \"get a gallon of milk, and if they have eggs, get a dozen.\" He returns with 13 gallons of milk.",
  `jokedate`="2021-02-04 
```

对于用户来说，如果数据包含报价，则不会被插入是一个令人烦恼的问题。他们会丢失他们输入的任何东西。但是恶意用户能够滥用这一点。在 PHP 的旧版本中，可以通过用分号(`;`)分隔来运行多个查询。

想象一下，如果用户在框中键入以下内容:

```
"; DELETE FROM `joke`; -- 
```

这将向数据库发送以下查询:

```
INSERT INTO `joke` SET
        `joketext`="";

DELETE FROM `joke`;

--`jokedate`="2021-02-04 
```

在 MySQL 中,`--`是单行注释，所以最后一行将被忽略，然后运行`INSERT`查询，接着运行用户在框中输入的`DELETE`查询。事实上，用户可以在框中键入任何他们喜欢的查询，它就会在数据库上运行！

## 神奇语录

在 PHP 的早期，SQL 注入攻击是如此可怕，以至于 PHP 背后的团队在语言中添加了一些针对 SQL 注入的内置保护。首先，他们禁用了一次发送多个查询的功能。其次，他们添加了一种叫做**的神奇语录**。PHP 的这一保护性特性会自动分析浏览器提交的所有值，并在任何“危险”字符(如撇号)前插入反斜杠(`\`)——如果不小心将它们包含在 SQL 查询中，就会导致问题。

神奇引号的问题在于，它造成的问题和防止的问题一样多。首先，它检测到的字符，以及它用来净化它们的方法(在它们前面加一个反斜杠)，只在某些情况下有效。根据您站点的字符编码和您使用的数据库服务器，这些措施可能完全无效。

第二，当提交的值用于除创建 SQL 查询之外的其他目的时，这些反斜杠可能会非常麻烦。如果用户的姓中包含撇号，magic quotes 特性会在其中插入一个伪反斜杠。

简而言之，神奇的引号功能是个坏主意——以至于它从 PHP 5.4 版本中被移除了。然而，由于 PHP 的历史和代码量，您可能会遇到一些对它的引用，所以有必要对 magic quotes 特性的作用有一个基本的了解。

一旦 magic quotes 被认为是一个坏主意，PHP 开发人员的建议是关闭它。然而，这意味着有些 web 服务器是关闭的，而其他的是打开的。这对开发人员来说是一个头疼的问题:他们要么必须指示每个将要使用他们代码的人关闭它——这在一些共享服务器上是不可能的——要么编写额外的代码来解决这个问题。

大多数开发人员选择了后者，您可能会遇到这样的代码:

```
if (get_magic_quotes_gpc()) {
  // code here
} 
```

如果您在已有的代码中看到类似这样的`if`语句，您可以安全地删除整个代码块，因为在最近的 PHP 版本中，不会执行`if`语句中的任何代码。

如果您确实看到了这样的代码，这意味着最初的开发人员理解了魔术引号的问题，并且正在尽最大努力防止它。从 PHP 5.4 开始(你应该不会碰到它，因为它不再被支持)，`get_magic_quotes_gpc()`将总是返回`false`并且代码永远不会被执行。

你真正需要知道的是，对于手头的问题来说，这是一个糟糕的解决方案。当然，没有神奇的报价，你需要找到一个不同的解决问题的方法。幸运的是，PDO 类可以通过使用一种叫做“预准备语句”的东西来为您完成所有的艰苦工作。

## 准备好的声明

一个**准备好的语句**是一种特殊的 SQL 查询，你已经提前发送到你的数据库服务器，给服务器一个机会准备执行它——但不是实际执行它。把它想象成写一个`.php`剧本。代码就在那里，但是直到您在 web 浏览器中访问该页面时，它才真正运行。准备好的语句中的 SQL 代码可以包含占位符，当查询*是要执行的*时，您将为这些占位符提供值。在填写这些占位符时，PDO 足够聪明，能够自动防范“危险”字符。

下面是如何准备一个`INSERT`查询，然后使用`$_POST['joketext']`作为笑话的文本安全地执行它:

```
$sql = 'INSERT INTO `joke` SET
  `joketext` = :joketext,
  `jokedate` = "today's date"';

$stmt = $pdo->prepare($sql);

$stmt->bindValue(':joketext', $_POST['joketext']);
$stmt->execute(); 
```

让我们来分解一下，一次一个陈述。首先，我们像往常一样将 SQL 查询写成一个 PHP 字符串并存储在一个变量(`$sql`)中。然而，这个`INSERT`查询的不同寻常之处在于没有为`joketext`列指定值。相反，它包含该值的占位符(`:joketext`)。别管刚才那个`jokedate`场了；我们一会儿再回到这个话题。

接下来，我们调用 PDO 对象的 prepare 方法(`$pdo`)，将我们的 SQL 查询作为参数传递给它。这将查询发送到 MySQL 服务器，要求它*准备*运行查询。MySQL 还不能运行它，因为`joketext`列没有值。`prepare`方法返回一个`PDOStatement`对象(没错，就是从`SELECT`查询中给我们结果的那种对象)，我们将它存储在`$stmt`中。

既然 MySQL 已经准备好了要执行的语句，我们就可以通过调用我们的`PDOStatement`对象(`$stmt`)的`bindValue`方法来发送缺失的值。对于要提供的每个值，我们调用这个方法一次(在这种情况下，我们只需要提供一个值—笑话文本)，作为参数传递我们要填充的占位符(`':joketext'`)和我们要填充的值(`$_POST['joketext']`)。因为 MySQL 知道我们发送给它的是一个离散值，而不是需要解析的 SQL 代码，所以值中的字符没有被解释为 SQL 代码的风险。当使用准备好的语句时，SQL 注入漏洞根本不可能出现！

最后，我们调用`PDOStatement`对象的`execute`方法来告诉 MySQL 使用我们提供的值执行查询。(没错，这个`PDOStatement`的方法叫`execute`，不像`PDO`对象的类似方法叫`exec`。PHP 有很多优点，但是一致性不是其中之一！)

关于这段代码，您会注意到一件有趣的事情，那就是我们从未在笑话文本周围加引号。`:joketext`存在于查询中，没有任何引号，当我们调用`bindValue`时，我们从`$_POST`数组中传递给它简单的笑话文本。当使用准备好的语句时，您不需要引号，因为数据库(在我们的例子中是 MySQL)足够智能，知道文本是一个字符串，并且在执行查询时会被当作字符串处理。

这段代码中挥之不去的问题是如何将今天的日期分配给`jokedate`字段。我们*可以*编写一些奇特的 PHP 代码，以 MySQL 要求的`YYYY-MM-DD`格式生成今天的日期，但结果是 MySQL 本身有一个函数可以做到这一点——`CURDATE`:

```
$sql = 'INSERT INTO `joke` SET
  `joketext` = :joketext,
  `jokedate` = CURDATE()';

$stmt = $pdo->prepare($sql);
$stmt->bindValue(':joketext', $_POST['joketext']);
$stmt->execute(); 
```

这里使用 MySQL `CURDATE`函数将当前日期指定为`jokedate`列的值。MySQL 实际上有几十个这样的函数，但是我只在需要的时候介绍它们。

现在我们有了查询，我们可以完成前面开始的`if`语句来处理“添加笑话”表单的提交:

```
if (isset($_POST['joketext'])) {
  try {
    $pdo = new PDO('mysql:host=mysql;dbname=ijdb;charset=utf8mb4', 'ijdbuser', 'mypassword');

    $sql = 'INSERT INTO `joke` SET
      `joketext` = :joketext,
      `jokedate` = CURDATE()';

    $stmt = $pdo->prepare($sql);

    $stmt->bindValue(':joketext', $_POST['joketext']);

    $stmt->execute();
  }
  catch (PDOException $e) {
    $title = 'An error has occurred';

    $output = 'Database error: ' . $e->getMessage() . ' in ' .
    $e->getFile() . ':' . $e->getLine();
  }
} 
```

但是等等！这个`if`声明还有一个锦囊妙计。一旦我们将新笑话添加到数据库中，而不是像以前那样显示 PHP 模板，我们希望将用户的浏览器重定向回笑话列表。这样，用户就可以看到新添加的笑话。上面的`if`语句末尾的两行就是这么做的。

为了获得想要的结果，您的第一反应可能是允许控制器在添加新笑话并像往常一样使用`jokes.html.php`模板显示列表后，简单地从数据库中获取笑话列表。这样做的问题是，从浏览器的角度来看，笑话列表是提交了“添加笑话”表单的结果。如果用户刷新页面，浏览器将重新提交表单，导致新笑话的另一个副本被添加到数据库中！这很少是想要的行为。

相反，我们希望浏览器将更新后的笑话列表视为普通网页，无需重新提交表单即可重新加载。做到这一点的方法是用 HTTP 重定向来回答浏览器的表单提交，这是一种特殊的响应，告诉浏览器导航到不同的页面。(HTTP 代表超文本传输协议，是描述访问者的 web 浏览器和您的 web 服务器之间交换的请求/响应通信的语言。)

PHP `header`函数提供了发送类似这样的特殊服务器响应的方法，它允许您在发送给浏览器的响应中插入特定的头。为了发出重定向信号，您必须发送一个带有您希望浏览器指向的页面 URL 的`Location`头:

```
header('Location: URL'); 
```

在这种情况下，我们希望将浏览器发送给`jokes.php`。下面是在将新笑话添加到数据库后，将浏览器重定向回控制器的两行代码:

```
header('Location: jokes.php'); 
```

下面是`addjoke.php`控制器的完整代码。

**举例:[MySQL-add joke](https://github.com/spbooks/phpmysql7/tree/MySQL-AddJoke)**

```
<?php
if (isset($_POST['joketext'])) {
    try {
        $pdo = new PDO('mysql:host=mysql;dbname=ijdb;charset=utf8mb4', 'ijdbuser', 'mypassword');

        $sql = 'INSERT INTO `joke` SET
      `joketext` = :joketext,
      `jokedate` = CURDATE()';

        $stmt = $pdo->prepare($sql);

        $stmt->bindValue(':joketext', $_POST['joketext']);

        $stmt->execute();

        header('location: jokes.php');
    } catch (PDOException $e) {
        $title = 'An error has occurred';

        $output = 'Database error: ' . $e->getMessage() . ' in ' .
    $e->getFile() . ':' . $e->getLine();
    }
} else {
    $title = 'Add a new joke';

    ob_start();

    include  __DIR__ . '/../templates/addjoke.html.php';

    $output = ob_get_clean();
}
include  __DIR__ . '/../templates/layout.html.php'; 
```

当您回顾这些以确保对您有意义时，请注意通过创建一个`new PDO`对象连接到数据库的代码必须出现在任何运行数据库查询的代码之前。但是显示“添加笑话”表单并不需要数据库连接。只有当表单提交后，才会建立连接。

加载这个，通过你的浏览器给数据库添加一两个新笑话。

现在，你可以在 MySQL 数据库中查看现有的笑话，并添加新的笑话。

## 从数据库中删除数据

在这一节中，我们将对笑话数据库站点进行最后一次增强。在笑话页面(`jokes.php`)的每个笑话旁边，我们将放置一个标记为**删除**的按钮。当点击时，它将从数据库中删除笑话，并显示更新的笑话列表。

如果你喜欢挑战，在阅读我的解决方案之前，你可能想尝试自己写这篇文章。尽管我们正在实现一个全新的特性，但我们将主要使用本章前面例子中使用的相同工具。以下是一些让你开始的提示:

*   你需要一个新的控制器。
*   SQL `DELETE`命令是必需的，我在第 3 章中介绍过。
*   要删除控制器中的某个笑话，您需要唯一地标识它。`joke`表中的`id`列就是为此目的而创建的。在请求删除笑话时，您必须传递要删除的笑话的 ID。最简单的方法是使用隐藏的表单域。

至少，花点时间想想你会如何处理这件事。当你准备好看到解决方案时，请继续阅读！

首先，我们需要修改从数据库获取笑话列表的`SELECT`查询。除了`joketext`列，我们还必须获取`id`列，这样我们就可以唯一地标识每个笑话:

```
try {
  $pdo = new PDO('mysql:host=mysql;dbname=ijdb;charset=utf8mb4', 'ijdbuser', 'mypassword');

  $sql = 'SELECT `id`, `joketext` FROM `joke`';
  $result = $pdo->query($sql);
  // … 
```

我们还必须修改将数据库结果存储到`$jokes`数组中的`while`循环。我们存储每个笑话的 ID 和文本，而不是简单地将每个笑话的文本存储为数组中的一项。一种方法是让`$jokes`数组中的每一项都成为一个独立的数组:

```
while ($row = $result->fetch()) {
  $jokes[] = ['id' => $row['id'], 'joketext' => $row['joketext']];
} 
```

*注意:如果你已经切换到使用一个`foreach`循环来处理你的数据库结果行，那也很好:*

```
foreach ($result as $row) {
  $jokes[] = array('id' => $row['id'], 'joketext' =>
    $row['joketext']);
}
```

一旦这个循环运行完毕，我们将得到`$jokes`数组，其中的每一项都是一个包含两项的关联数组:笑话的 ID 和它的文本。因此，对于每个笑话(`$jokes[n]`)，我们可以检索其 ID ( `$jokes[n]['id']`)和文本(`$jokes[n]['text']`)。

我们的下一步是更新`jokes.html.php`模板，从这个新的数组结构中检索每个笑话的文本，并为每个笑话提供一个**删除**按钮:

```
<?php foreach ($jokes as $joke): ?>
<blockquote>
  <p>
  <?=htmlspecialchars($joke['joketext'], ENT_QUOTES, 'UTF-8')?>
  <form action="deletejoke.php" method="post">
    <input type="hidden" name="id" value="<?=$joke['id']?>">
    <input type="submit" value="Delete">
  </form>
  </p>
</blockquote>
<?php endforeach; ?> 
```

以下是更新代码的亮点:

*   每个笑话都将显示一个表单，如果提交，将删除该笑话。我们使用表单的`action`属性向新的控制器`deletejoke.php`发出信号。
*   由于`$jokes`数组中的每个笑话现在由一个两项数组表示，而不是一个简单的字符串，我们必须更新这一行来检索笑话的文本。我们使用`$joke['text']`而不仅仅是`$joke`来做到这一点。
*   当我们提交删除这个笑话的表单时，我们会发送要删除的笑话的 ID。为此，我们需要一个包含笑话 ID 的表单字段，但是我们希望对用户隐藏这个字段；这就是为什么我们使用一个隐藏的表单字段(`input type="hidden"`)。这个字段的名字是`id`，它的值是要删除的笑话的 ID(`$joke['id']`)。与笑话的文本不同，ID 不是用户提交的值，所以不需要担心用`htmlspecialchars`使它 HTML 安全。我们可以放心这将是一个数字，因为当笑话被添加到数据库时，它是由 MySQL 为`id`列自动生成的。
*   点击提交按钮(`input type="submit"`)提交表单。它的值属性给了它一个标签**删除**。
*   最后，我们关闭这个笑话的表单。

注意:如果你了解你的 HTML，你可能会认为表单和输入标签不属于 blockquote 元素，因为它们不是引用文本的一部分(笑话)。

严格地说，这是真的:表单及其输入应该在块引号之前或之后。不幸的是，显示标签结构显然需要一点 CSS(层叠样式表)代码，这超出了本书的范围。

与其在一本关于 PHP 和 MySQL 的书中教你 CSS 布局技术，我决定用这个不完美的标记。如果你打算在现实世界中使用这些代码，你应该花一些时间学习 CSS(或者至少获得 CSS 专家的服务)。这样，您可以完全控制您的 HTML 标记，而不用担心需要 CSS 来使它看起来更好。如果你想了解更多关于 CSS 布局的知识，看看蒂芙尼·布朗的《CSS 大师，第三版 》。

将以下 CSS 添加到`jokes.css`中，使按钮出现在笑话的右侧，并在它们之间画一条线:

```
blockquote {display: table; margin-bottom: 1em; border-bottom: 1px solid #ccc; padding: 0.5em;}
blockquote p {display: table-cell; width: 90%; vertical-align: top;}
blockquote form {display: table-cell; width: 10%;} 
```

下图显示了添加了**删除**按钮的笑话列表。

![Each button can delete its respective joke](img/c9856420d9a93acf9a60774f61dd25b6.png)

但是等等！在我们继续让**删除**按钮工作之前，让我们简单地后退一步，仔细看看这一行:

```
$jokes[] = ['id' => $row['id'], 'joketext' => $row['joketext']]; 
```

在这里，我们对`PDOStatement`对象进行循环，这给了我们一个包含键`id`和`joketext`以及相应值的`$row`变量，我们用它来构建另一个具有相同键和值的数组。

你可能已经意识到这是非常低效的。我们可以用这段代码实现同样的事情:

```
while ($row = $result->fetch()) {
  $jokes[] = $row;
} 
```

但是我们知道，这也可以通过一个`foreach`循环来实现:

```
foreach ($result as $row) {
 $jokes[] = $row;
} 
```

在这个实例中，我们使用`foreach`来遍历数据库中的记录并构建一个数组。然后我们用模板中的另一个`foreach`循环遍历数组。我们可以这样写:

```
$jokes = $result; 
```

现在，当在模板中迭代`$jokes`时，它不是一个数组，而是一个`PDOStatement`对象。然而，这对输出没有影响，并为我们节省了一些代码。事实上，我们可以完全省略`$result`变量，将`PDOStatement`对象直接加载到`$jokes`变量中。完整的`jokes.php`控制器现在看起来像这样:

```
try {
  $pdo = new PDO('mysql:host=mysql;dbname=ijdb;charset=utf8mb4', 'ijdbuser', 'mypassword');

  $sql = 'SELECT `joketext`, `id` FROM joke';

  $jokes = $pdo->query($sql);

  $title = 'Joke list';

  ob_start();

  include  __DIR__ . '/../templates/jokes.html.php';

  $output = ob_get_clean();
}
catch (PDOException $e) {
  $title = 'An error has occurred';

  $output = 'Database error: ' . $e->getMessage() . ' in ' .
  $e->getFile() . ':' . $e->getLine();
}

include  __DIR__ . '/../templates/layout.html.php'; 
```

现在，我们甚至没有在控制器中迭代记录的`while`循环，而是直接在模板中迭代记录，节省了一些代码并使页面执行得稍微快一些，因为它现在只在记录上循环一次。

回到我们新的**删除**按钮:要让这个新特性工作，剩下的就是添加一个相关的`deletejoke.php`来对数据库发出一个`DELETE`查询:

```
try {
  $pdo = new PDO('mysql:host=mysql;dbname=ijdb;charset=utf8mb4', 'ijdbuser', 'mypassword');

  $sql = 'DELETE FROM `joke` WHERE `id` = :id';

  $stmt = $pdo->prepare($sql);

  $stmt->bindValue(':id', $_POST['id']);
  $stmt->execute();

  header('location: jokes.php');
}
catch (PDOException $e) {
  $title = 'An error has occurred';

  $output = 'Unable to connect to the database server: ' . $e->getMessage() . ' in ' .
  $e->getFile() . ':' . $e->getLine();
}

include  __DIR__ . '/../templates/layout.html.php'; 
```

更新后的`jokes.php`和`deletejoke.php`的完整代码可作为**示例: [MySQL-DeleteJoke](https://github.com/spbooks/phpmysql7/tree/MySQL-DeleteJoke)** 。

这段代码与我们在本章前面添加的处理“添加笑话”代码的代码完全一样。我们首先准备一个带有占位符的`DELETE`查询，该占位符用于我们想要删除的笑话 ID。

提示:您可能认为在这种情况下不需要准备好的语句来保护我们的数据库免受 SQL 注入攻击，因为玩笑 ID 是由用户看不见的隐藏表单字段提供的。事实上，**所有的**表单域——甚至是隐藏的——最终都在用户的控制之下。例如，有广泛分布的浏览器插件，它们将使隐藏的表单域可见并可供用户编辑。记住:当涉及到保护你的站点安全时，浏览器提交的任何值最终都是可疑的。

然后，我们将提交的值`$_POST['id']`绑定到占位符并执行查询。一旦查询完成，我们使用 PHP `header`函数请求浏览器发送一个新的请求来查看更新后的笑话列表。

*注意:如果您自己处理这个例子，您的第一反应可能是为每个笑话提供一个**删除**超链接，而不是为页面上的每个笑话编写一个包含**删除**按钮的完整 HTML 表单。事实上，这样一个链接的代码会简单得多:*

```
<?php foreach ($jokes as $joke): ?>
  <blockquote>
    <p>
      <?=htmlspecialchars($joke['joketext'], ENT_QUOTES, 'UTF-8')?>
      <a href="deletejoke.php&amp;id=<?=$joke['id']?>">Delete</a>
    </p>
  </blockquote>
<?php endforeach; ?>
```

*简而言之，永远不要用超链接来执行**动作**(比如删除一个笑话)。它们只能用于提供一些相关内容的链接。带有`method="get"`的表单也是如此，它应该只用于执行现有数据的查询。只有在提交了带有`method="post"`的表单后，才能执行操作。*

*这是因为浏览器和相关软件对带有`method="post"`的表单有不同的处理方式。例如，如果您提交一个带有`method="post"`的表单，然后单击浏览器中的刷新按钮，浏览器会询问您是否确定要重新提交表单。当涉及到带有`method="get"`的链接和表单时，浏览器没有类似的保护来防止重新提交。*

搜索引擎和其他网络爬虫也会跟踪你网站上的所有链接，以便确定何时在搜索结果中显示你网站的页面。

如果你的网站因为超链接被点击而删除了一个笑话，你会发现只要搜索引擎找到你的网站，你的笑话就会被删除。

## 任务已完成

在这一章中，你学习了所有关于 PHP 数据对象(PDO)的知识，这是一个内置 PHP 类(`PDO`、`PDOException`和`PDOStatement`)的集合，允许你通过创建对象然后调用它们提供的方法来与 MySQL 数据库服务器交互。在学习的过程中，您还学到了面向对象编程(OOP)的基础知识——这对 PHP 初学者来说是一个不小的成就！

使用 PDO 对象，你建立了你的第一个数据库驱动的网站，它在线发布了`ijdb`数据库，并允许访问者添加和删除笑话。

在某种程度上，你可以说这一章实现了本书的既定使命:教你如何建立一个数据库驱动的网站。当然，本章中的例子只包含了最基本的内容。在本书的其余部分，我将向你展示如何充实你在这一章中学到的骨架。

在第五章，我们将回到 MySQL Workbench 中的 SQL 查询窗口。我们将学习如何使用关系数据库原理和高级 SQL 查询来表示更复杂类型的信息，并为我们的访问者添加的笑话加分！

我们希望你喜欢这个节选自 *[PHP & MySQL:新手到忍者，第七版](https://www.sitepoint.com/premium/books/php-mysql-novice-to-ninja-7th-edition)*。整本书可以在 SitePoint Premium 上买到，也可以从你最喜欢的书籍和电子书零售商那里买到。

## 分享这篇文章