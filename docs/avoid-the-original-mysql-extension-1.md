# 避免最初的 MySQL 扩展，第 1 部分

> 原文：<https://www.sitepoint.com/avoid-the-original-mysql-extension-1/>

有经验的开发人员会避开最初的 MySQL 扩展，因为它在 PHP 中处于被抛弃的状态。然而，新生的网络开发者可能完全忘记了它休眠的过去和垂死的未来。

从 php 连接到 mysql 的入门教程当然非常丰富，从一个简单的“PHP 和 MySQL 教程”谷歌搜索就可以看出这一点。只是不幸的是，他们中的许多人只教如何使用原来的 MySQL 扩展。这使得希望学习如何与数据库交互的新开发人员只能使用三种潜在的扩展中的一种，这种技能在即将到来的 PHP 主要版本中很快就会变得无用。

反过来，这也增加了依赖该扩展的人数，在某些情况下，他们会将它作为唯一的数据库访问方法。这意味着需要一个“软弃用”来慢慢淘汰扩展的使用，对已经切换到新扩展的用户产生尽可能小的影响。

因此，这篇由两部分组成的文章旨在提高仍在使用 MySQL 扩展的开发人员的意识，告知他们 MySQL 扩展的问题，并帮助他们转向另一种扩展。毕竟，谁会真的想写一个在不久的将来的 PHP 版本中无法运行的脚本呢？在这一部分，我们将关注 MySQLi，在第二部分，我们将看看 PDO。

## MySQL 扩展有什么问题？

除了它目前正在被弃用的事实之外，最初的 MySQL 扩展似乎没有什么问题。深入挖掘 PHP 的历史，很容易明白为什么许多开发人员避免使用它。从 PHP 4.3.1 开始，这个扩展只被维护。这意味着 MySQL 扩展上一次添加新功能是在 2002 年末。这是你错过的 10 年的功能！

如果这听起来还不够糟糕的话，集成到 PHP 中的 MySQL API 的最后一个版本来自 MySQL 4.0.0。这意味着该扩展不处理 MySQL 多年来添加到 API 中的任何新特性。

## 选择 1:MySQL——MySQL 得到了改进

与 MySQL 数据库交互有两种主要的替代方法。第一个是 MySQLi，或 MySQL 改进版，它公开了面向对象和过程 API。

与 MySQL 扩展相比，过程 API 之间的差异很小，但收益是显著的。MySQLi 提供了超过 100 个函数，让只有 48 个函数的老扩展相形见绌。这些新函数带来了新的特性，在我们熟悉了基础知识之后，我们将快速浏览其中的一些。

### MySQLi 基础知识

首先，让我们创建一个到 MySQL 数据库的连接，然后通过执行一些基本的查询与它进行交互。

```
<?php
// Procedural API connection method #1
$db = mysqli_connect('host', 'username', 'password');
mysqli_select_db($db, 'database');

// Procedural API connection method #2
$db = mysqli_connect('host', 'username', 'password', 'database');

if (mysqli_connect_errno()) {
     die(mysqli_connect_error());
}

// Object-oriented API connection
$db = new MySQLi('host', 'username', 'password', 'database');

if ($db->connect_errno) {
    die($db->connect_error);
}
```

当使用过程实现时，我们有两种连接数据库的方法。第一种更类似于最初的 MySQL 扩展，其中主机连接与数据库连接分离，需要更多的代码。第二种方法更像 MySQLi API 的面向对象接口，其中数据库名称作为参数传递。然后，我们可以通过询问`mysqli_connect_errno()`的返回来检查在尝试连接到数据库时是否有错误。使用`mysqli_connect_error()`检索错误信息。

使用面向对象的实现，我们实例化了`MySQLi`类，并将所有连接细节作为构造函数参数传递。然后可以对`connect_errno`属性进行检查，查看是否有错误，并且可以从`connect_error`属性中检索相应的错误消息。

现在让我们对数据库进行一些基本的交互。

```
<?php
$name = "O'Reilly";
$email = "In'valid@email.com";
$clean = array();

// Procedural API
$clean['name'] = mysqli_real_escape_string($db, $name);
$clean['email'] = mysqli_real_escape_string($db, $email);

mysqli_query($db, "INSERT INTO table_name
     VALUES (NULL, '{$clean['name']}', '{$clean['email']}')");

mysqli_query($db, "UPDATE table_name
     SET name = 'Thomas' WHERE email = '{$clean['email']}'");

$result = mysqli_query($db, "SELECT name FROM table_name
     WHERE email = '{$clean['email']}'");

if ($row = mysqli_fetch_assoc($result)) {
     echo $row['name'];
} else {
     echo 'No results found.';
}

// Object-oriented API
$clean['name'] = $db->real_escape_string($name);
$clean['email'] = $db->real_escape_string($email);

$db->query("INSERT INTO table_name
    VALUES (NULL, '{$clean['name']}', '{$clean['email']}')");

$db->query("UPDATE table_name
    SET name = 'Thomas' WHERE email = '{$clean['email']}'");

$result = $db->query("SELECT name FROM table_name
    WHERE email = '{$clean['email']}'");

if ($row = $result->fetch_assoc()) {
    echo $row['name'];
} else {
    echo 'No results found.';
}
```

与最初的 MySQL 扩展和 MySQLi 的过程 API 的主要区别是强制传递连接链接作为`mysqli_*`函数中的第一个参数(所有函数和方法/属性的综合列表可以在 PHP 手册中找到[)。这当然不同于 MySQL 扩展，在 MySQL 扩展中，可以选择将连接链接作为最后一个参数传递。](http://php.net/manual/en/mysqli.summary.php "PHP: The MySQLi Extension Function Summary - Manual")

在面向对象的方法中，我们不必将连接链接作为参数传递给对象的方法，因为它是作为`MySQLi`实例状态的一部分来维护的。此外，`MySQLi`类公开的所有方法和属性也省略了`mysqli_`前缀，`MySQLi_stmt`类的属性和方法中也省略了`mysqli_stmt_`。

### MySQLi 有什么新功能？

现在我们已经熟悉了这两个 API 的基础知识，让我们看看它引入的一些新特性:预准备语句、多查询和事务。

**准备好的报表**

预处理语句，也称为参数化查询，是该 API 引入的主要特性之一。预处理语句被认为比 MySQL 的`mysql_real_escape_string()`函数更安全，因为它们不容易出现人为错误。如果 MySQL 的本机转义功能应用不当，它仍然会在 web 应用程序中留下巨大的安全漏洞。

参数化查询可以这样应用:

```
<?php
$name = "O'Reilly";
$email = "In'valid@email.com";

// Procedural API
$insQuery = mysqli_prepare($db, 'INSERT INTO table VALUES (NULL, ?, ?)');
mysqli_stmt_bind_param($insQuery, 'ss', $name, $email);
mysqli_stmt_execute($insQuery);

// Object-oriented API
$insQuery = $db->prepare('INSERT INTO table VALUES (NULL, ?, ?)');
$insQuery->bind_param('ss', $name, $email);
$insQuery->execute();
```

在创建准备好的语句时，我们插入问号(没有用引号括起来，否则它们将被视为字符串值)来标识值在查询中的绑定位置。这些被称为占位符，在这个例子中是未命名的，因为 MySQLi 不支持命名占位符(不像 PDO)。一旦我们准备好了查询，我们就可以绑定参数。

关注面向对象的 API，`bind_param()`方法的第一个参数指定了我们在绑定值时希望将值转换成的参数类型。有四种类型:s (string)、i (integer)、d (double)和 b (blob)。All 必须仅以小写形式使用，并且指示参数类型的字母数量必须与需要绑定的值的数量相匹配(即使所有的值都将被转换为相同的值)。

然后在`bind_param()`方法中为每个需要绑定到准备好的查询的值传递一个新参数。绑定值将分别转换为第一个参数中的类型顺序。然后，我们可以执行准备好的语句，将所有值安全地插入到查询中。

**多重查询**

MySQLi 引入的下一个主要特性是支持多条语句。这是我们能够在一个查询中堆叠 SQL 语句的地方，方法是用分号分隔它们，然后一次性执行它们。这是一个特别有效的特性，但是如果查询中的外包值在使用前没有正确转义，也可能是破坏性的。

对于多查询，我们不能使用参数化查询，因为它们不能一次准备多个语句。语句必须单独准备，这将需要更多的代码，但是代价是可读性更好，人为错误的可能性更小。

**交易**

我们要看的最后一个特性是对事务的支持，前提是您使用的是 InnoDB 存储引擎。事务通常被描述为具有 ACID 属性:

*   **原子性**表示一个动作要么完全发生，要么根本不发生。
*   **一致性**是进入数据库的新数据将符合当前设置的规则。如果传入的数据没有通过验证，那么它将被全部返回。
*   **隔离**指的是正在进行的事务的状态，它必须独立于其他事务，直到它完全完成。
*   **耐久性**是指数据库在发生故障(例如断电)后能够承受更新数据丢失的能力。它说一旦一个交易被提交，它将保持不变。

当我们有一组相互依赖的 SQL 语句时，通常会使用事务。这使得确保它们要么全部成功实现并得到承诺，要么全部失败变得更加重要。

我们可以通过在一个表中执行重复插入来测试事务的理论，该表中的一个字段设置了唯一的键约束:

```
<?php
mysqli_autoCommit($db, false);

$resultA = mysqli_query($db, "INSERT INTO table_name
    VALUES (NULL, 'Tom', 'email@address.com')");
if ($resultA === false) {
    mysqli_rollback($db);
}

$resultB = mysqli_query($db, "INSERT INTO table_name
    VALUES (NULL, 'Tom', 'email@address.com')");
if ($resultB === false) {
    mysqli_rollback($db);
}

mysqli_commit($db);
```

当创建事务时，我们首先调用`mysqli_autoCommit()`函数。它的第二个参数(如果使用面向对象的 API，则为第一个参数)应该设置为 false，不允许数据库在发出每个查询时保存您的更改。这是为了防止在您确定事务已经完全成功之前保存任何更改。一旦保存了更改，就不能回滚。

然后我们执行查询，检查它们是否失败；如果有任何失败，那么更改不会影响数据库。然而，如果在事务期间没有回滚，那么我们可以用`mysqli_commit()`成功地提交所有的更改。在上面的例子中，我们的第二个 insert 语句将会失败，因为提交了一个重复的条目，这意味着不会向数据库中插入任何内容。

## 摘要

本文展示了切换到更新的、更熟悉的 MySQLi API 的容易性，并给出了两个放弃使用 MySQL 扩展的很好的理由:它目前已被弃用(从 PHP 5.5.0 起将被正式弃用)，并且该扩展已经非常过时。我希望这能让迁移到较新的 MySQLi API 变得更容易一些，这样我们就能最终看到 MySQL 扩展的全部。

不要忘了查看 PHP 手册以获得关于 MySQLi 及其函数库和内置类的更多信息，当我们关注 PDO 时，请继续关注[第二部分](https://www.sitepoint.com/avoid-the-original-mysql-extension-2/ "Avoid the Original MySQL Extension, Part 2")。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章