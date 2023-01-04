# 避免最初的 MySQL 扩展，第 2 部分

> 原文：<https://www.sitepoint.com/avoid-the-original-mysql-extension-2/>

[在本系列的第一部分](https://www.sitepoint.com/avoid-the-original-mysql-extension-1/ "Avoid the Original MySQL Extension, Part 1")中，我们看到了最初的 MySQL API 有什么问题，以及我们如何迁移到更新的、功能丰富的 MySQL API。在第二部分中，我们将探索 PDO 扩展，揭示它所提供的一些特性。

## 选择# 2:PDO-PHP 数据对象

PDO 扩展支持 12 个驱动程序，使它能够连接到各种关系数据库，而开发人员不必学习许多不同的 API。它通过抽象公共接口背后的数据库交互调用来实现这一点，使开发人员能够为不同的数据库使用一致的接口。这当然给了它比 MySQL 和 MySQLi 扩展更大的优势，MySQL 和 MySQLi 扩展只限于一个数据库。

### PDO 基础知识

通过 PDO 连接到数据库是一个快速简单的过程，所需的细节作为参数传递给 PDO 的构造函数。然后，我们访问对象的公共方法和属性来发送查询和检索数据。

```
<?php
$dsn = 'mysql:host=localhost;dbname=database_name';
$username = 'user';
$password = 'password';

$db = new PDO($dsn, $username, $password);

$numRows = $db->exec("INSERT INTO table VALUES ('val1', 'val2', 'val3')");

$result = $db->query('SELECT col1, col2, col3 FROM table', PDO::FETCH_ASSOC);
foreach ($result as $row) {
    echo "{$row['col1']} - {$row['col2']} - {$row['col3']}n";
}

$result->closeCursor();

unset($db);
```

首先连接到数据库，DSN、用户名和密码作为参数传递给`PDO`类。DSN 是一个连接字符串，因数据库而异，但通常包含数据库驱动程序的名称、主机、数据库名称，有时还包含端口号。

然后在示例中执行两个基本查询，第一个使用`exec()`方法，第二个使用`query()`方法。这两种方法的重要区别是成功的返回值；`exec()`应该用于非选择语句(插入、更新或删除)并返回受影响的行数，而`query()`应该用于选择语句并在成功时返回结果集对象。如果失败，它们都返回 false。

我们可以通过向`query()`传递第二个参数来操纵数据返回的方式。默认值是`PDO::FETCH_BOTH`，它为每一行返回一个数组中的重复数据；一个是关联数据，其中列名是键，列值是值(`PDO::FETCH_ASSOC`)，另一个是整数索引数组(`PDO::FETCH_NUM`)。因为通常不需要这样做，所以建议您指定一个适当的获取模式来节省资源。

然后，我们调用`closeCursor()`方法来清理存储在`$result`对象中不再需要的已用资源。此外，当我们知道脚本不再需要与数据库交互时，我们会取消 PDO 对象的设置，以释放所使用的任何资源。

### PDO 的特色

与 MySQLi API 一样，PDO 扩展也引入了许多开发人员可以利用的特性。既然我们已经介绍了使用 PDO 的绝对基础，我们可以继续看一些特性，包括准备好的语句、事务、改变 PDO 的默认行为，以及抽象的衍生。

**准备好的报表**

像 MySQLi 一样，PDO 也支持预准备语句，通过预准备语句，我们将参数绑定到查询，以防止对数据库的注入攻击。准备好的语句还支持客户端和服务器端缓存，当同一个准备好的查询需要绑定和执行不同的值时，这会加快执行时间。然而，相对于 MySQLi 的参数化查询，PDO 确实有几个优点值得研究。

第一个优点是，PDO 支持使用命名参数，通过给占位符起一个有意义的名字，让我们能够识别查询中的占位符。这有助于我们在涉及大量参数时跟踪需要绑定到查询的参数，而不是使用问号的未命名(或位置)占位符序列，这取决于绑定的顺序。

第二个优点是，通过使用`bindValue()`方法，我们可以更加灵活地将值绑定到我们准备好的查询。这使我们能够绕过只能绑定变量然后在调用`execute()`方法时被求值的限制，就像 MySQLi 的`bind_param()`方法一样。

让我们通过一个例子来仔细看看如何将变量和值显式绑定到我们准备好的查询。

```
<?php
$insQuery = $db->prepare('INSERT INTO table VALUES (:col1, :col2, :col3)');
$insQuery->bindParam('col1', $val1, PDO::PARAM_INT);
$insQuery->bindParam('col2', $val2, PDO::PARAM_STR);
$insQuery->bindParam('col3', $val3, PDO::PARAM_INT);
$insQuery->execute();

$selQuery = $db->prepare('SELECT col2, col3 FROM table WHERE col1 LIKE :val');
$selQuery->bindValue('val', "%{$val}%", PDO::PARAM_STR);
$result = $selQuery->execute();

while ($row = $result->fetch(PDO::FETCH_ASSOC)) {
    echo "{$row['col2']} - {$row['col3']}n";
}
```

命名占位符以冒号开头，然后使用与 PHP 中变量相同的命名约定。当将我们的参数绑定到准备好的查询时，我们必须为`bindParam()`和`bindValue()`方法提供至少两个参数，以及可选的第三个参数。

第一个参数是占位符的名称(区分大小写)，第二个参数是我们希望绑定到查询的变量或值，可选的第三个参数是绑定变量/值的类型(默认为`PDO::PARAM_STR`，但是为了清楚起见，我总是指定类型)。`bindParam()`方法还使我们能够分别指定可选的第四个和第五个参数、数据类型长度和任何附加的驱动程序选项。

`bindParam()`和`bindValue()`方法是正交的，因此在将值绑定到查询时可以使用其中之一或两者。然而，命名和未命名占位符的情况并非如此，在一个准备好的查询中只能使用其中的一个。

隐式绑定是我们放弃使用`bindParam()`和`bindValue()`方法的地方，只是将参数以数组格式传递给`execute()`方法进行绑定。如果使用了命名占位符，那么需要将一个关联数组传递给`execute()`；如果使用了位置占位符，那么可以将一个索引数组传递给`execute()`。

下面是我们如何在参数化查询的简写格式中使用未命名占位符:

```
<?php
$updQuery = $db->prepare('UPDATE table SET col1 = ?, col2 = ? WHERE col3 = ?');
$updQuery->execute(array('val1', 'val2', 'val3'));

if ($updQuery->rowCount() !== 0) {
    echo 'Success';
}
```

我们首先准备我们的查询并将未命名的占位符放置到位，然后用一个数组调用`execute()`方法，该数组包含要绑定到我们准备好的查询的值。这个被传递的数组可以包含变量和字符串中的一个(或两个)。这种简便方法的缺点是，我们无法指定绑定到准备好的查询的参数类型。接下来，我们询问是否有任何行通过使用来自`rowCount()`方法的返回值被更新，该返回值将包含受上一个操作影响的行数。如果行数不等于零，那么我们认为它是成功的。

我们在准备好的查询中看到的最后一种绑定类型不是为了安全目的，而是为了获取数据。在这里，我们使用`bindColumn()`方法将列名绑定到变量。`bindColumn()`和`bindParam()` / `bindValue()`方法都可以用在一个准备好的查询上，通过将结果直接分配给所有变量，为我们提供了获取数据的灵活性，同时不受注入攻击。

```
<?php
$preQuery = $db->prepare('SELECT col2, col3 FROM table WHERE col1 = :val');
$preQuery->bindParam('val', $value, PDO::PARAM_STR);
$preQuery->execute();

$preQuery->bindColumn('col2', $OUTcol2);
$preQuery->bindColumn('col3', $OUTcol3);

while ($result = $preQuery->fetch(PDO::FETCH_BOUND)) {
    echo "{$OUTcol2} - {$OUTcol3}n";
}
```

我们首先准备一个值并将其绑定到我们的查询。然后执行它，我们调用`bindColumn()`方法；第一个参数是列名(可以用数字指定)，第二个参数是绑定列值的变量。为此，我使用了自己的命名约定来帮助区分我创建的变量(具有已知的安全值)和那些包含数据库中的值的变量。这有助于我知道哪些可能包含被污染的数据，因此需要在输出时进行转义，以防止 XSS 攻击。

然后，我们逐行遍历获取的数据，其中获取方法(`PDO::FETCH_BOUND`)将结果集中的列值赋给它们绑定到的变量(`$OUTcol2`和`$OUTcol3`)。当`$result`的值保持为真时(仍然有要循环的行)，循环将继续执行。

MySQLi API 也使用`bind_result()`方法提供了相同的功能(语法与上面类似)。

**交易**

PDO 也支持事务，但是它们的创建方式与 MySQLi API 略有不同。如前一篇文章所述，事务处理 ACID 属性，用于维护关系数据库中多个表之间的数据完整性(A **C** ID)。如果其中一个语句在执行过程中失败，我们能够在永久提交更改之前回滚所有语句的影响(ACI**D**)；这是由于每个事务在被认为成功之前都是孤立的。当语句依赖于彼此的成功时，这一点尤其重要，因此它们要么全部成功，要么全部失败。

通过对一个表执行重复插入操作，并在其中一列上设置唯一约束键，我们可以再次在表单中看到事务:

```
<?php
$db->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

try
{
    $db->beginTransaction();

    $db->exec("INSERT INTO table VALUES (NULL, 'col1', 'col2')");
    $db->exec("INSERT INTO table VALUES (NULL, 'col1', 'col2')");

    $db->commit();
}
catch(PDOException $e) {
    $db->rollBack();
}
```

我们必须首先设置错误处理行为，以便在引发异常时抛出异常。这是因为 PDO 的默认错误处理设置不会触发异常，这意味着我们的 catch 块永远不会执行。

为了启动一个事务，我们调用`beginTransaction()`方法，然后尝试执行两个破坏性查询(其中我们数据库中的数据将使用 INSERT/UPDATE/DELETE 语句被永久修改)。然后我们调用`commit()`方法尝试提交事务，然后将查询处理返回到自动提交。上述内容当然会违反表上设置的数据完整性规则，导致 catch 块执行，事务回滚。这意味着不会向数据库中插入新数据。

**操纵默认行为**

更改 PDO 的默认行为可以在类实例化期间通过构造函数方法完成，也可以在类实例化后创建的预先存在的对象上完成。通过使用 PDO 的构造函数方法，我们可以更改任意数量的设置，方法是将它们以数组格式作为第四个(可选)参数传递。当想要操纵一个预先存在的对象的行为时，我们可以使用`setAttribute()`方法。

```
<?php
$dsn = 'mysql:host=localhost;dbname=database_name';
$username = 'user';
$password = 'password';
$options = array(PDO::ATTR_PERSISTENT => TRUE,
                 PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC);
$pdo = new PDO($dsn, $username, $password, $options);
```

以上将启用持久连接，并将默认获取模式从`FETCH_BOTH`更改为`FETCH_ASSOC`。但是，连接持久性设置是少数设置之一，在创建对象之前必须更改这些设置才能生效。另一方面，缺省的获取行为在预先存在的对象上是可变的，使得它的设置能够在脚本执行期间随时改变。

使用`setAttribute()`方法对对象进行可改变的设置，其中对象的默认行为在它的剩余使用中被更新。我们也能够在一些操作的方法调用期间临时改变一些设置；一个这样的例子是使用`prepare()`方法，它使我们能够在准备查询时指定额外的驱动程序选项(作为可选的第二个参数)。这些为我们在整个脚本中自由调整 PDO 对象的行为提供了额外的灵活性。

为了让我们的 PDO 对象在脚本执行期间对错误做出反应，更改错误处理设置是经常发生的事情。这可以通过构造函数或`setAttribute()`方法来完成，有三种模式:`SILENT`(默认)、`WARNING`和`EXCEPTION`。虽然我们总是能够使用`errorCode()`和`errorInfo()`方法查看错误信息，但是错误报告设置使我们能够选择遇到的错误是完全沉默(`PDO::ERRMODE_SILENT`)、如果是警告则引发(`PDO::ERRMODE_WARNING`)还是总是抛出(`PDO::ERRMODE_EXCEPTION`)。

### 抽象的分支

然而，PDO 提供的抽象层也有一些缺点。其中之一是每个数据库之间的兼容性问题，PDO 必须尝试只使用所有数据库都可用的特性。这方面的一个例子是 PDO_MYSQL 驱动程序，其中查询的默认设置被设置为无缓冲，因为它不受其他驱动程序的支持。这意味着，为了优化这个抽象层的使用，当从一个数据库切换到另一个数据库时，可能需要更改单独的设置。

在使用 PDO 时，我们还必须小心选择要实现的方法。虽然 PDO 通过其方法提供的许多功能已经标准化，可以在所有受支持的数据库上工作，但仍有一些方法不能完全移植到所有数据库，因为它们根本不受支持。其中之一是`quote()`方法，它不适用于 PDO ODBC 驱动程序。为了实现可移植性，应该总是使用泛型方法。

另一个需要注意的地方是，它会编写不兼容的 SQL 代码。这是因为尽管 PDO 能够处理多个数据库，但它不是查询抽象层。编写依赖于数据库的 SQL 代码的一个经典例子是在使用反勾号字符时，它可能受您的 MySQL 数据库支持，但不受 PDO 可以与之交互的其他数据库的支持。对于转义无效的表名和列名，其他数据库有自己的定义，例如 PostgreSQL 和 Oracle 使用双引号，或者 Microsoft SQL Server 使用方括号。

## 结束语

在本文中，我们介绍了 PDO 的基础知识和对其默认行为的操作，并探索了净化输入的预准备语句(使用显式和隐式绑定)，同时演示了它们的另一种用法。我们还查看了事务的创建及其各自的行为(通过 ACID 属性描述)。然而，PDO 还有很多额外的功能，我们没有机会在本文中讨论，所以请务必阅读 PHP.net 手册，了解关于 PDO 的更多信息。

这就结束了我们的两部分系列，为什么我们应该避免 MySQL API，以及我们如何通过引入两种替代方法来避免它。我希望我们现在可以更好地摆脱原来的 MySQL API，转而支持 MySQL 或更突出的 PDO 扩展！

<small>图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")</small>

## 分享这篇文章