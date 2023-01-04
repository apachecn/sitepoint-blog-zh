# 从 MySQL 扩展迁移到 PDO

> 原文：<https://www.sitepoint.com/migrate-from-the-mysql-extension-to-pdo/>

本指南是一个实用的概述，帮助您开始将现有的 PHP 代码从使用旧的 MySQL 扩展迁移到新的 PDO 数据库抽象扩展。我不会详细介绍 PDO 为您提供的所有功能，但我会与您分享您需要了解的基本方法，并提供一些提示，这样您的迁移将会尽可能地快速和轻松。

首先，您应该了解 PDO 在 PHP 生态系统中的位置。实际上有三种方法可以从 PHP 与 MySQL 数据库交互:第一种是使用 MySQL 扩展，第二种是使用 MySQL 扩展，第三种是使用 PDO。

MySQL 扩展是三个扩展中最古老的，也是开发人员用来与 MySQL 通信的最初选项。由于 PHP 和 MySQL 新版本中的改进，[扩展现在被弃用，取而代之的是另外两种选择。](http://news.php.net/php.internals/53799 "php.internals: deprecating ext/mysql")

MySQL 是使用 MySQL 数据库的“改进”扩展。它利用了 MySQL 服务器新版本中可用的特性，向开发人员公开了面向函数和面向对象的接口，还做了一些其他漂亮的事情。如果 PDO 不适合你的项目，那么这是你应该使用的扩展。

然后最近的扩展是 PDO，代表“PHP 数据对象”PDO 提供了一个 API，它整合了以前分布在主要数据库访问扩展中的大部分功能，如 MySQL、PostgreSQL、SQLite、MSSQL 等。该接口为程序员展示高级对象以处理数据库连接、查询和结果集，而低级驱动程序执行与数据库服务器的通信和资源处理。许多讨论和工作正在进入 PDO，它被认为是用现代、专业的代码处理数据库的合适方法。

如果可能的话，我建议人们跳过中间人，从 MySQL 扩展迁移到 PDO，而不是 MySQL。PDO 提供了一个一致的 API 来处理各种数据库，所以如果你必须使用另一个数据库，你不必修改你的 PHP 代码。此外，虽然 MySQLi 扩展目前得到了维护，但它总有可能在未来被弃用。现在选择迁移到 PDO 意味着你不必以后再迁移到那里并更新你的代码两次。我用 PDO，你也应该用！

## 安装和配置 PDO

一旦你决定要更新你的代码，你需要确保 PDO 被正确的设置和配置。正如我之前提到的，PDO 由两部分组成:公开接口的扩展本身和一个底层的数据库特定驱动程序。包括 MySQL 在内的越来越多的数据库都有驱动程序。

从版本 5.1.0 开始，PDO 和 SQLite 特定的驱动程序应该是默认 PHP 安装的一部分，MySQL 特定的驱动程序仍然需要安装，但是大多数 Linux 发行版通常对 PHP 应该如何编译和打包有不同的想法。事实上，现在许多主流发行版都将 MySQL 扩展、MySQL 扩展和 MySQL PDO 驱动程序打包在同一个包中。如果你在这样的系统上使用 MySQL，那么 PDO 可能已经为你设置好了。

*   Ubuntu 用 PDO 构建 PHP(但默认情况下不打包 SQLite 驱动程序)，并将 MySQL 扩展和驱动程序捆绑在一起。您可以通过运行`sudo apt-get install php5-mysql`来安装扩展和驱动程序。
*   Fedora 也将扩展和驱动捆绑在一起，但是将 PDO 打包为可加载模块。运行`sudo yum install php-mysql`会安装 MySQL 扩展和驱动程序，而`php-pdo`包会自动作为依赖项包含进来。
*   SUSE 是三个发行版中唯一一个按照 PHP 团队推荐的 PDO 方式构建 PHP 的发行版；他们用 PDO *和*构建 PHP，包括 SQLite 驱动程序。`yast2 --install php5-mysql`将安装您需要的 MySQL 扩展和驱动程序。

在 Windows 上，所有的 PDO 驱动程序都包含在 ext 文件夹中，该文件夹是在您从预编译的二进制文件中安装 PHP 时创建的。您只需要通过添加或取消注释以下行来更新您的`php.ini`:

```
extension=php_pdo_mysql.dll
```

在最坏的情况下，自己编译驱动程序总是可能的。确保 MySQL 库和其他开发文件已安装并可在您的构建环境中访问，因为人们在尝试编译时遇到的大多数问题都是由于编译器找不到各种头文件或库来链接造成的。

## 基本查询

使用 MySQL 扩展的函数来处理数据库的基本工作流程可以被认为是一个 5 步的过程:

1.  建立与数据库服务器的连接，并选择您将使用的数据库
2.  构造一个查询发送给服务器
3.  发送查询
4.  迭代返回的结果行
5.  释放结果和可能的数据库连接所使用的资源

```
<?php
// Step 1: Establish a connection
$db = mysql_connect("localhost", "testusr", "secretpass");
mysql_select_db("testdb", $db);

// Step 2: Construct a query
$query = "SELECT * FROM foo WHERE bar = '" . mysql_real_escape_string($zip) . "'";

// Step 3: Send the query
$result = mysql_query($query, $db);

// Step 4: Iterate over the results
while($row = myql_fetch_assoc($result)) {
    print_r($row);
}

// Step 5: Free used resources
mysql_free_result($result);
mysql_close($db);
```

对于 PDO，可以遵循相同的过程，如下所示:

```
<?php
// Step 1: Establish a connection
$db = new PDO("mysql:host=localhost;dbname=testdb", "testusr", "secretpass");

// Step 2: Construct a query
$query = "SELECT * FROM foo WHERE bar = " . $db->quote($zip);

// Step 3: Send the query
$result = $db->query($query);

// Step 4: Iterate over the results
while($row = $result->fetch(PDO::FETCH_ASSOC)) {
    print_r($row);
}

// Step 5: Free used resources
$result->closeCursor();
$db = null;
```

使用 PDO 连接到服务器和选择数据库的过程是使用 DSN 而不是`myql_connect()`和`mysql_select_db()`来执行的。DSN 是“数据源名称”的缩写，它是一个字符串，标识使用哪个 PDO 驱动程序与数据库进行通信，并传递任何重要的连接信息，如主机服务器、连接端口和数据库名称。根据您的数据库，您提供的 DSN 的格式可能会有所不同。每个驱动程序所需的主要组件都记录在 php.net 上，但不幸的是，它们有时很难找到。我建议使用该网站的搜索功能在在线文档中查找术语“DSN”[。](http://www.php.net/results.php?q=DSN&p=manual&l=en "PHP: Search results - DSN")

还要注意的是，`PDO::quote()`都转义了*和*引用了一个值，而`mysql_real_escape()`只转义了该值。您应该记住这一点，这样您就不会在最终的查询中出现多余的引号，并绞尽脑汁想知道它们是从哪里来的。

虽然有时候你可以不用释放结果资源就可以使用`mysql_free_result()`，但是我建议你在使用 PDO 时不要养成这种懒惰的习惯。如果您仍然有一个来自尚未调用`closeCursor()`的`SELECT`查询的结果集，并且您试图在表上执行`INSERT`或`UPDATE`，那么更改可能会悄无声息地失败，您将再次感到困惑。最好养成正确做事的习惯，以避免以后遇到令人沮丧的情况。

## (稍微)深入挖掘

到目前为止，已经介绍的内容足以让您开始尽可能快速、轻松地将 PHP 应用程序从 MySQL 迁移到 PDO，但是对于 PDO，还有比我向您展示的更多的内容。我还想强调一下 PDO 的几个其他特性，您可能会发现它们很有用:预准备语句和错误处理。

### 准备好的声明

预处理语句是查询作为对象的表示。将查询表示为对象的好处是，可以将额外的逻辑与它相关联，否则对于平面字符串表示来说，很难管理这些逻辑。例如，您可以在查询中指定占位符，然后将其绑定到脚本中的变量。如果您使用不同的参数多次运行相同的查询来满足请求，这将非常有用。该查询只需要准备一次，但是可以随着变量值的变化而用不同的值运行多次。

```
<?php
// collection of user information to import into the database
$users = ...

// specify the query "template"
$query = $db->prepare("INSERT INTO users (first_name, last_name, email) VALUES (:fname, :lname, :email)");

// bind the placeholder names to specific script variables
$query->bindParam(":fname", $firstName);
$query->bindParam(":lname", $lastName);
$query->bindParam(":email", $email);

// assign values to the specific variables and execute the query
foreach ($users as $u) {
    $firstName = $u["first_name"];
    $lastName = $u["last_name"];
    $email = $u["email"];
    $query->execute();
}
```

这些参数不必用预处理语句来引用或转义，因为底层驱动程序会自动为您引用和转义任何绑定的参数。因此，许多人喜欢使用预准备语句来防范 SQL 注入漏洞，即使查询只执行一次。

### 错误处理

通过使用`PDO::setAttribute()`设置错误处理模式，您可以使用 PDO 指定三种不同的错误处理范例之一。

```
<?php
$db = new PDO($dsn, $user, $passwd);
$db->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
```

这些模式是:

*   静默模式(`PDO::ERRMODE_SILENT`)–设置内部错误代码，但不中断脚本的执行(这是默认设置)
*   警告模式(`PDO::ERRMODE_WARNING`)–设置错误代码并触发 E_WARNING 消息
*   异常模式(`PDO::ERRMODE_EXCEPTION`)–设置错误代码并抛出一个 PDOException 对象

无论设置了哪种模式，都会设置一个内部错误代码，您可以使用`PDO`和`PDOStatement`对象的`errorCode()`和`errorInfo()`方法进行检查。这些类似于`mysql_errno()`和`mysql_error()`。`errorCode()`返回 ANSI SQL-92 中定义的 [5 个字符的字符串。`errorInfo()`通常更有用，因为它返回的数组除了包含 5 个字符的代码外，还包含一条错误消息。](http://docstore.mik.ua/orelly/java-ent/jenut/ch08_06.htm "Return Codes (Java Enterprise in a Nutshell)")

```
<?php
$query = "SELECT * FROM foo WHERE MALFORMED QUERY";
$result = $db->query($query);
$error = $db->errorInfo();
if (!is_null($error[2])) {
    echo "Query failed! " . $error[2];
}
...
```

如果您正在使用异常模式，那么可以通过异常对象的`getMessage()`方法获得出错原因的描述。

```
<?php
$query = "SELECT * FROM foo WHERE MALFORMED QUERY";
try {
    $result = $db->query($query);
    ...
}
catch (PDOException $e) {
    echo $e->getMessage();
}
```

## 摘要

现在，您应该已经具备了从旧的 MySQL 扩展迁移到新的 PDO 扩展的基本知识。您已经看到，虽然大部分时间 PDO 已经可用，但如果由于某种原因它不可用，也很容易设置。您还看到了连接到 MySQL 数据库服务器、发出查询和接收结果是多么容易。当然，这只是一个实用的概述；为了更多地了解 PDO 能提供什么以及你如何利用它，我鼓励你阅读 php.net 大学的可用文档。

<small>图片 via [德克·埃尔肯](http://www.shutterstock.com/gallery-348535p1.html "Stock Photos | Shutterstock: Royalty-Free Subscription Stock Photography & Vector Art")/[Shutterstock](http://www.shutterstock.com "Stock Photos and Royalty-Free Images by Subscription")</small>

## 分享这篇文章