# 从 PHP 访问 PostgreSQL

> 原文：<https://www.sitepoint.com/accessing-postgresql-php/>

 **<q>*本文是《用 PostgreSQL 开始数据库*[(Wrox 出版社，2001) *一书的节选，经允许在此转载。*](http://www.wrox.com/Books/Book_Details.asp?section=1_1&isbn=1861005156&subject=&subject_id=)</q>

最近，向在线数据库提供基于网络的接口已经成为一种强烈的趋势。支持这一运动的原因有很多，包括:

*   Web 浏览器是浏览数据的常见和熟悉的界面
*   基于 Web 的应用程序可以很容易地集成到现有的网站中
*   Web (HTML)界面很容易创建和修改

在本章中，我们将探索从 PHP 访问 PostgreSQL 的各种方法。PHP 是一种用于编写基于 web 的应用程序的服务器端、跨平台脚本语言。它允许您在 HTML 页面中嵌入程序逻辑，这使您能够提供动态网页。PHP 允许我们创建与 PostgreSQL 交互的基于 web 的用户界面。

在本章中，我们将假设至少对 PHP 语言有一个基本的了解。如果您对 PHP 完全不熟悉，您可能想先浏览以下一些资源:

*   PHP 的主页[http://www.php.net](http://www.php.net)
*   《开始 PHP 4》，Wankyu Choi，Allan Kent，Ganesh Prasad 和 Chris Ullman，Jon Blank 和 Sean Cazzell，Wrox 出版社(ISBN 1-861003-73-0)

关于 PHP 开发方法有许多不同的思想流派。讨论它们不在本书的范围之内。相反，我们将专注于设计有效利用 PHP 的 PostgreSQL 接口的 PHP 脚本。

请注意，我们将重点关注 PHP 版本 4。虽然下面的大部分代码示例和描述将适用于 PHP 的早期版本，但在功能上可能会有一些差异。此外，假设所有代码片段都在有效 PHP 范围的上下文中(通常是指带有`<?php ?>`标签)，除非另有说明。

#### 向 PHP 添加 PostgreSQL 支持

在开始开发与 PostgreSQL 数据库交互的 PHP 脚本之前，您需要在 PHP 安装中包含 PostgreSQL 支持。

如果您不确定您现有的 PHP 安装是否已经支持 PostgreSQL，那么创建一个名为`phpinfo.php`的简单脚本(它应该放在您的 web 服务器的文档根目录中)，包含以下行:

```
<?php  

phpinfo();  

?>
```

在 web 浏览器中检查该脚本的输出。如果已经包含 PostgreSQL 支持，则输出将包含如下所示的部分:

如果您的 PHP 安装已经支持 PostgreSQL，您可以继续下一节。

如果您有 PHP 源代码，添加 PostgreSQL 支持是相当容易的。只需将`--with-pgsql`选项传递给`configure`脚本:

```
$ ./configure --with-pgsql
```

如果`configure` 脚本无法自己找到 PostgreSQL 安装目录，您可以选择指定该目录:

```
$ ./configure --with-pgsql=/var/lib/pgsql
```

记住，根据您的构建需求，您可能需要向`configure` 脚本传递额外的选项。例如，要构建支持 PostgreSQL、LDAP 和 XML 的 PHP，您可以使用以下命令行:

```
$ ./configure --with-pgsql --with-imap --enable-xml
```

参考 PHP 文档(特别是 PHP 发行版中包含的`INSTALL`文档),获得额外的编译选项和安装说明。你也可以在 http://www.php.net/manual/en/html/installation.html 找到它们。

#### 为 PostgreSQL 使用 PHP API

与 PostgreSQL 数据库的所有交互都是通过 PostgreSQL 扩展执行的，它是一组全面的 PHP 函数。有关功能的完整列表和更多信息，请参考[http://www.php.net/manual/ref.pgsql.php](http://www.php.net/manual/ref.pgsql.php)。

一个简单的 PHP 脚本打开到 PostgreSQL 数据库的连接，选择一些行，打印结果集中的行数，然后关闭连接，如下所示:

```
<?php   

$db_handle = pg_connect("dbname=bpsimple");   

$query = "SELECT * FROM item";   

$result = pg_exec($db_handle, $query);   

echo "Number of rows: " . pg_numrows($result);   

pg_freeresult($result);   

pg_close($db_handle);   

?>
```

如您所见，从 PHP 内部与数据库交互相当简单。我们现在将更深入地讨论 PHP PostgreSQL 扩展的各个方面。

##### 数据库连接

在与数据库进行交互之前，必须先打开一个与数据库的连接。每个连接由一个变量表示(我们将这个变量称为**连接句柄**)。PHP 允许一次打开多个连接，每个连接都有自己的连接句柄。

*T2`pg_connect()`*

使用`pg_connect()`功能打开数据库连接。该函数将一个连接字符串作为唯一的参数，并返回一个数据库连接句柄。这里有一个例子:

```
$db_handle = pg_connect("dbname=bpsimple user=jon");
```

您可以创建自己的用户名，并用它作为`user=<username>`连接到数据库。

如果您想使用 PHP 变量，请记住用双引号而不是单引号将连接字符串括起来:

```
$db_handle = pg_connect("dbname=$dbname user=$dbuser");
```

所有标准 PostgreSQL 连接参数都可以在连接字符串中找到。下面列出了最常用的选项及其含义:

*   `Dbname`:要连接的数据库(默认:`$PGDATABASE`)
*   `User`:连接时使用的用户名(默认:`$PGUSER`)
*   `password`:指定用户的密码(默认:`$PGPASSWORD`或无)
*   `Host`:要连接的服务器名称(默认:`$PGHOST`或`localhost`)
*   `hostaddr`:要连接的服务器的 IP 地址(默认:`$PGHOSTADDR`)
*   `Port`:服务器上要连接的 TCP/IP 端口(默认:`$PGPORT`或`5432`)

如果连接尝试失败，`pg_connect()`函数将返回 false。因此，可以通过测试返回值来检测失败的连接尝试:

```
<?php   

$db_handle = pg_connect("dbname=bpsimple");   

if ($db_handle) {   

    echo 'Connection attempt succeeded.';   

} else {   

    echo 'Connection attempt failed.';   

}   

pg_close($db_handle);   

?>
```

如上所述，PHP 支持多个并发数据库连接:

```
$db_handle1 = pg_connect("dbname=database1");   

$db_handle2 = pg_connect("dbname=database2");
```

**持续连接**

PHP 还支持**持久的**数据库连接。持久连接在页面请求的生存期内保持打开，而普通连接在页面请求结束时关闭。PHP 维护一个当前打开的连接列表，如果请求一个新的持久性数据库连接，该连接具有与列表中打开的连接相同的连接参数，则返回一个已经打开的连接的句柄。这样做的好处是，当连接池中已经存在合适的数据库连接时，可以为脚本节省创建新数据库连接的额外开销。

*T2`pg_pconnect()`*

要打开到 PostgreSQL 的持久连接，请使用`pg_pconnect()`函数。这个函数的行为与上面描述的`pg_connect()`函数完全一样，除了它请求一个持久连接(如果有可用的话)。

但是，建议您小心使用持久连接。过度使用持久连接可能会导致到数据库的大量空闲数据库连接。持久连接的理想用途是在多个页面也将请求相同类型的数据库连接(意味着包含相同连接参数的连接)的情况下。在这种情况下，持久连接可以大幅提升性能。

**关闭连接**

*T2`pg_close()`*

可以使用`pg_close()` 函数显式关闭数据库连接:

```
pg_close($db_handle);
```

然而，这里需要指出几件事。首先，在持久连接的情况下，这个函数实际上不会关闭连接。相反，连接将被返回到数据库连接池。其次，PHP 将在脚本执行结束时自动关闭任何打开的非持久数据库连接。这两点都使得调用`pg_close()`变得非常不必要，但是为了完整性和那些真正需要立即关闭连接的情况，我们包含了这个函数。

如果提供的连接句柄无效，`pg_close()`将返回 false。否则，`pg_close()`将在成功时返回 true。

**连接信息**

PHP 提供了许多简单的函数，用于根据提供的连接句柄检索当前数据库连接的信息。这些功能包括:

*   `pg_dbname()`返回当前数据库的名称
*   `pg_host()`返回与当前连接相关的主机名
*   `pg_options()`返回与当前连接相关的选项
*   `pg_port()`返回当前连接的端口号
*   `pg_tty()`返回与当前连接相关的 TTY 名称

所有这些函数都需要一个连接句柄作为唯一的参数，并在成功时返回一个字符串或一个数字。否则，它们将返回 false:

```
<?php   

$db_handle = pg_connect("dbname=bpsimple");   

echo "<h1>Connection Information</h1>";   

echo "Database name: ' . pg_dbname($db_handle) . "<br>n";   

echo "Hostname: " . pg_host($db_handle) . "<br>n";   

echo "Options: " . pg_options($db_handle) . "<br>n";   

echo "Port: " . pg_port($db_handle) . "<br>n";   

echo "TTY name: " . pg_tty($db_handle) . "<br>n";   

pg_close($db_handle);   

?>
```

##### 构建查询

我们已经看到了一个从 PHP 执行查询的简单例子。在这一节中，我们将更深入地讨论查询构建和执行的主题。

SQL 查询仅仅是字符串，因此可以使用 PHP 的任何字符串函数来构建。下面是 PHP 中查询字符串构造的三个例子:

```
$lastname = strtolower($lastname);    

$query = "SELECT * FROM customer WHERE lname = '$lastname'";
```

本例首先执行`$lastname`的小写转换。然后，它使用 PHP 的标准字符串语法构建查询字符串。

请注意，`$lastname`的值在这几行之后将保持小写。

```
$query = "SELECT * FROM customer WHERE lname = '" .    

         strtolower($lastname) . "'";
```

这个例子使用了对`strtolower()`的内联调用。不能从字符串文字内部(换句话说，在引号之间)调用函数，所以我们需要将查询字符串分成两部分，并用中间的函数调用将它们连接起来(使用“点”操作符)。

与前面的例子不同，PHP 执行完这一行后，`strtolower()`函数的结果不会影响`$lastname`的值。

```
$query = sprintf("SELECT * FROM customer WHERE lname = '%s'",    

                 strtolower($lastname));
```

最后一个例子使用了`sprintf()`函数来生成查询字符串。`sprintf()`函数使用特殊的字符组合(例如上面一行中的`%s`)来格式化字符串。关于`sprintf()`功能的更多信息可在 http://www.php.net/manual/en/function.sprintf.php[获得](http://www.php.net/manual/en/function.sprintf.php)。

这些方法中的每一种都会产生完全相同的查询字符串。像大多数事情一样，最好的方法取决于具体情况。对于简单的查询，直接的字符串赋值可能效果最好，但是当情况需要对大量变量进行插值或转换时，您可能希望探索不同的方法。在某些情况下，您可能会遇到执行速度和代码可读性之间的权衡。大多数编程任务都是如此，因此您必须运用自己的最佳判断。

下面是一个以长赋值字符串形式编写的复杂查询示例:

```
$query = "UPDATE table $tablename SET " . strtolower($column) . " = '" .    

         strtoupper($value) . "'";
```

这可以用 PHP `sprintf()`函数重写为:

```
$query = sprintf("UPDATE table %s SET %s = '%s'", $tablename,    

                 strtolower($column), strtoupper($value));
```

第二个表达式显然比第一个更易读，尽管基准测试将显示这种可读性是以轻微的性能代价为代价的，因为程序员的时间比机器的时间要昂贵得多。在这种情况下，可读性与执行速度之间的权衡可能是值得的，除非您在每个页面请求中要做数百个这种类型的字符串构造。

**复杂查询**

在理想的情况下，我们所有的查询应该和前面例子中使用的一样简单，但是我们都知道这很少是真的。在需要构建更复杂查询的情况下，我们发现 PHP 提供了许多方便的函数来帮助我们完成任务。

例如，考虑需要执行大量表删除的情况。在原始 SQL 中，查询可能如下所示:

```
DELETE FROM items WHERE item_id = 4 OR item_id = 6
```

现在，这个查询本身看起来并不复杂，但是如果这个查询需要删除十几行，并在`WHERE`子句中指定每一行的`item_id`呢？此时查询字符串变得相当长，因为 where 子句中表达式的数量可能需要变化，所以我们需要在代码中考虑这些细节。

我们可能会通过某种 HTML 表单输入方法从用户那里收到要删除的商品 id 列表，所以我们可以假设它们将以某种数组格式存储(至少，这是存储列表最方便的方式)。我们假设这个物品 id 数组被命名为`$item_ids`。基于这一假设，上述查询可以构造如下:

```
<?php     

$query = "DELETE FROM items WHERE ";     

$query .= "item_id = " . $item_ids[0];     

if (count($item_ids) > 1) {     

    array_shift($item_ids);     

    $query .= " or item_id = " .     

              implode(" or item_id =", $item_ids);     

}     

?>
```

这将产生一个带有任意数量的条目 id 的 SQL 查询。基于这段代码，我们可以编写一个通用函数来执行删除操作:

```
<?php     

function sqlDelete($tablename, $column, $ids) {     

    $query = '';     

    if (is_array($ids)) {     

        $query = "DELETE FROM $tablename WHERE ";     

        $query .= "$column = " . $ids[0];     

        if (count($ids) > 1) {     

            array_shift($ids);     

            $query .= " or $column = " .     

                      implode(" or $column =", $ids);     

        }     

    }     

    return $query;     

}     

?>
```

**执行查询**

一旦构建了查询字符串，下一步就是执行它。使用`pg_exec()`函数执行查询。

*T2`pg_exec()`*

`pg_exec()`函数负责将查询字符串发送到 PostgreSQL 服务器并返回结果集。

这里有一个简单的例子来说明`pg_exec()`的用法:

```
<?php      

$db_handle = pg_connect("dbname=bpsimple");      

$query = 'SELECT * FROM customer'      

$result = pg_exec($db_handle, $query);      

pg_close($db_handle);      

?>
```

如您所见，`pg_exec()`需要两个参数:一个活动的连接句柄和一个查询字符串。您应该已经熟悉了前面几节中的每一项。`pg_exec()`将在成功执行查询后返回结果集。我们将在下一节使用结果集。

如果查询失败，或者连接句柄无效，`pg_exec()`将返回 false。因此，谨慎的做法是测试`pg_exec()`的返回值，这样就可以检测到这样的故障。

以下示例包括一些结果检查:

```
<?php      

$db_handle = pg_connect("dbname=bpsimple");      

$query = "SELECT * FROM customer";      

$result = pg_exec($db_handle, $query);      

if ($result) {      

    echo "The query executed successfully.<br>n";      

} else {      

    echo "The query failed with the following error:<br>n";      

    echo pg_errormessage($db_handle);      

}      

pg_close($db_handle);      

?>
```

在这个例子中，我们测试了`pg_exec()`的返回值。如果它不是 false(换句话说，它有一个值)，`$result`表示一个结果集。否则，如果`$result`为假，我们知道发生了错误。然后，我们可以使用`pg_errormessage()`函数打印该错误的描述性消息。我们将在本章后面更详细地介绍错误消息。

##### 使用结果集

成功执行查询后，pg_exec()将返回一个结果集标识符，通过它我们可以访问结果集。结果集存储数据库返回的查询结果。例如，如果执行选择查询，结果集将包含结果行。

PHP 为处理结果集提供了许多有用的函数。它们都将结果集标识符作为参数，因此它们只能在查询成功执行后使用。在前一节中，我们学习了如何测试成功执行。

*`pg_numrows()`和`pg_numfields()`*

现在我们从两个最简单的结果函数开始:`pg_numrows()`和`pg_numfields()`。这两个函数分别返回结果集中的行数和字段数。例如:

```
<?php       

$db_handle = pg_connect("dbname=bpsimple");       

$query = "SELECT * FROM customer";       

$result = pg_exec($db_handle, $query);       

if ($result) {       

    echo "The query executed successfully.<br>n";       

    echo "Number of rows in result: " . pg_numrows($result) . "<br>n";       

    echo "Number of fields in result: " . pg_numfields($result);       

} else {       

    echo "The query failed with the following error:<br>n";       

    echo pg_errormessage($db_handle);       

}       

pg_close($db_handle);       

?>
```

如果有错误，这些函数将返回-1。

*T2`pg_cmdtuples()`*

还有一个`pg_cmdtuples()`函数，它将返回受查询影响的行数**。例如，如果我们在查询中执行插入或删除操作，我们实际上不会从数据库中检索任何行，因此结果集中的行数或字段数不会指示查询的结果。相反，更改发生在数据库内部。`pg_cmdtuples()`将返回受这些类型的查询影响的行数(即插入、删除或更新的行数):**

```
<?php       

$db_handle = pg_connect("dbname=bpsimple");       

$query = "DELETE FROM item WHERE cost_price > 10.00";       

$result = pg_exec($db_handle, $query);       

if ($result) {       

    echo "The query executed successfully.<br>n";       

    echo "Number of rows deleted: " . pg_cmdtuples($result);       

} else {       

    echo "The query failed with the following error:<br>n";       

    echo pg_errormessage($db_handle);       

}       

pg_close($db_handle);       

?>
```

如果数据库中没有行受到查询的影响，那么`pg_cmdtuples()`函数将返回 0，就像选择查询一样。

**从结果集中提取值**

有许多方法可以从结果集中提取值。我们将从`pg_result()`函数开始。

*T2`pg_result()`*

当您想要从结果集中检索单个值时，可以使用`pg_result()`函数。除了结果集标识符之外，还必须指定要从结果中检索的行和字段。行用数字指定，而字段可以用名称或数字索引指定。编号总是从零开始。

这里有一个使用`pg_result()`的例子:

```
<?php        

$db_handle = pg_connect("dbname=bpsimple");        

$query = "SELECT title, fname, lname FROM customer";        

$result = pg_exec($db_handle, $query);        

if ($result) {        

    echo "The query executed successfully.<br>n";        

    for ($row = 0; $row < pg_numrows($result); $row++) {        

        $fullname = pg_result($result, $row, 'title') . " ";        

        $fullname .= pg_result($result, $row, 'fname') . " ";        

        $fullname .= pg_result($result, $row, 'lname');        

        echo "Customer: $fullname<br>n";        

    }        

} else {        

    echo "The query failed with the following error:<br>n";        

    echo pg_errormessage($db_handle);        

}        

pg_close($db_handle);        

?>
```

使用数字索引，同样的代码块也可以写成这样:

```
<?php        

$db_handle = pg_connect("dbname=bpsimple");        

$query = "SELECT title, fname, lname FROM customer";        

$result = pg_exec($db_handle, $query);        

if ($result) {        

    echo "The query executed successfully.<br>n";        

    for ($row = 0; $row < pg_numrows($result); $row++) {        

        $fullname = "";        

        for ($col = 0; $col < pg_numfields($result); $col++) {        

            $fullname .= pg_result($result, $row, $col) . " ";        

        }        

        echo "Customer: $fullname<br>n";        

    }        

} else {        

    echo "The query failed with the following error:<br>n";        

    echo pg_errormessage($db_handle);        

}        

pg_close($db_handle);        

?>
```

然而，第一个例子可读性更强，并且不依赖于结果集中字段的顺序。PHP 还提供了更高级的从结果集中检索值的方法，因为遍历结果行不是特别有效。

*T2`pg_fetch_row()`*

PHP 提供了两个函数，`pg_fetch_row()`和`pg_fetch_array()`，可以一次返回多个结果值。这些函数都返回一个数组。

```
pg_fetch_row() returns an array that corresponds to a single row in the resultset. The array is indexed numerically, starting from zero. Here is the previous example rewritten to use pg_fetch_row():

```

```
<?php        

$db_handle = pg_connect("dbname=bpsimple");        

$query = "SELECT title, fname, lname FROM customer";        

$result = pg_exec($db_handle, $query);        

if ($result) {        

    echo "The query executed successfully.<br>n";        

    for ($row = 0; $row < pg_numrows($result); $row++) {        

        $values = pg_fetch_row($result, $row);        

        $fullname = "";        

        for ($col = 0; $col < count($values); $col++) {        

            $fullname .= $values[$col] . " ";        

        }        

        echo "Customer: $fullname<br>n";        

    }        

} else {        

    echo "The query failed with the following error:<br>n";        

    echo pg_errormessage($db_handle);        

}        

pg_close($db_handle);        

?>
```

如您所见，使用`pg_fetch_row()`消除了对`pg_result()`的多次调用。它还将结果值放在一个数组中，使用 PHP 的原生数组函数可以很容易地操作这个数组。

然而，在这个例子中，我们仍然通过数字索引来访问字段。理想情况下，我们还应该能够通过相关的名称来访问每个字段。为此，我们可以使用`pg_fetch_array()`函数。

*T2`pg_fetch_array()`*

`pg_fetch_array()`函数也返回一个数组，但是它允许我们指定是用数字索引还是关联索引(使用字段名作为键)。通过将以下参数之一作为第三个参数传递给`pg_fetch_array()`来指定该首选项:

*   `PGSQL_ASSOC` 通过字段名索引结果数组
*   `PGSQL_NUM` 用数字索引结果数组
*   `PGSQL_BOTH` 用数字和字段名索引结果数组

如果不指定上述索引方法之一，将默认使用`PGSQL_BOTH`。请注意，这将使结果集的大小加倍，因此您最好显式地指定上述内容之一。还要注意，字段名总是以小写字母返回，不管它们在数据库中是如何表示的。

下面是再次重写的例子，现在使用`pg_fetch_array()`:

```
<?php        

$db_handle = pg_connect("dbname=bpsimple");        

$query = "SELECT title, fname, lname FROM customer";        

$result = pg_exec($db_handle, $query);        

if ($result) {        

    echo "The query executed successfully.<br>n";        

    for ($row = 0; $row < pg_numrows($result); $row++) {        

        $values = pg_fetch_array($result, $row, PGSQL_ASSOC);        

        $fullname = $values['title'] . " ";        

        $fullname .= $values['fname'] . " ";        

        $fullname .= $values['lname'];        

        echo "Customer: $fullname<br>n";        

    }        

} else {        

    echo "The query failed with the following error:<br>n";        

    echo pg_errormessage($db_handle);        

}        

pg_close($db_handle);        

?>
```

*T2`pg_fetch_object()`*

PHP 还允许您用`pg_fetch_object()`函数获取结果值。每个字段名将被表示为这个对象的一个属性。因此，不能以数字方式访问字段。使用`pg_fetch_object()`编写，我们的示例如下所示:

```
<?php        

$db_handle = pg_connect("dbname=bpsimple");        

$query = "SELECT title, fname, lname FROM customer";        

$result = pg_exec($db_handle, $query);        

if ($result) {        

    echo "The query executed successfully.<br>n";        

    for ($row = 0; $row < pg_numrows($result); $row++) {        

        $values = pg_fetch_object($result, $row, PGSQL_ASSOC);        

        $fullname = $values->title . " ";        

        $fullname .= $values->fname . " ";        

        $fullname .= $values->lname;        

        echo "Customer: $fullname<br>n";        

    }        

} else {        

    echo "The query failed with the following error:<br>n";        

    echo pg_errormessage($db_handle);        

}        

pg_close($db_handle);        

?>
```

**字段信息**

PHP 允许您在结果集中收集一些关于字段值的信息。这些函数在某些情况下可能是有用的，所以我们将在这里简要介绍它们。

*T2`pg_fieldisnull()`*

PostgreSQL 支持`NULL` 字段值的概念。然而，PHP 不一定像 PostgreSQL 那样定义`NULL` 。考虑到这一点，PHP 提供了`pg_fieldisnull()`函数，以便您可以根据 PostgreSQL 对`NULL`的定义来确定字段值是否为`NULL` :

```
if (pg_fieldisnull($result, $row, $field)) {         

    echo "$field is NULL.";         

} else {         

    echo "$field is " . pg_result($result, $row, $field);         

}
```

*`pg_fieldname()`和`pg_fieldnum()`*

这些函数返回给定字段的名称或编号。字段以数字索引，从零开始:

```
echo "Field 1 is named: " . pg_fieldname($result, 1);         

echo "Field item_id is number: " . pg_fieldnum($result, "item_id");
```

请注意，`pg_fieldname()`将返回在`SELECT`语句中指定的字段名称。

*`pg_fieldsize()``pg_fieldprtlen()``pg_fieldtype()`*

可以确定字段的大小、打印(字符)长度和类型:

```
echo "Size of field 2:" . pg_fieldsize($result, 2);         

echo "Length of field 2: " . pg_fieldprtlen($result, $row, 2);         

echo "Type of field 2: " . pg_fieldtype($result, 2);
```

通常，数字字段索引从零开始。字段索引也可以指定为表示字段名的字符串。

此外，如果字段的大小是可变的，`pg_fieldsize()`将返回-1，或者返回 false。`pg_fieldprtlen()`出错时将返回-1。

**释放结果集**

*T2`pg_freeresult()`*

可以通过使用`pg_freeresult()`函数来释放结果集使用的内存:

```
pg_freeresult($result);
```

PHP 会在脚本执行结束时自动释放所有结果内存，所以只有当您特别担心脚本中的内存消耗，并且您知道以后在脚本执行中不会再使用这个 resultset 时，才需要调用这个函数。

**结果值的类型转换**

PHP 不提供您在其他语言中可能找到的多样化数据类型支持，因此结果集中的值有时会从原始数据类型转换为 PHP 原生数据类型。在大多数情况下，这种转换对您的应用程序影响很小或没有影响，但重要的是要知道可能会发生一些类型转换:

*   所有整数、布尔和 OID 类型都被转换为整数
*   所有形式的浮点数都被转换成双精度数
*   所有其他类型(数组等。)表示为字符串

##### 错误处理

我们在前面的章节中非常简要地谈到了错误处理。我们现在将更详细地介绍它。

几乎所有与 PostgreSQL 相关的函数在出错时都会返回某种可预测的值(通常为 false 或-1)。这使得检测错误情况变得相当容易，这样您的脚本就可以优雅地失败。例如:

```
$db_handle = pg_connect('dbname=bpsimple');          

if (!$db_handle) {          

    header("Location: http://www.example.com/error.php");          

    exit;          

}
```

在上面的例子中，如果数据库连接尝试失败，用户将被重定向到错误页面。

*T2`pg_errormessage()`*

```
pg_errormessage() can be used to retrieve the text of the actual error message as returned by the database server. pg_errormessage() will always return the text of the last error message generated by the server. Be sure to take that into consideration when designing your error handling and display logic.

您会发现，根据您的错误报告级别，当错误发生时，PHP 可能会相当冗长，通常会输出几行错误和警告。在生产环境中，通常不希望向最终用户显示这种类型的消息。

**`@`符号**

最直接的解决方案是降低 PHP 中的错误报告级别(通过`php.ini`中的`error_reporting`配置变量控制)。第二种选择是在每个函数调用的基础上直接从 PHP 代码中隐藏这些错误消息。PHP 使用`@`符号来表示错误抑制。例如，以下代码不会输出任何错误:

```

```
$db_handle = pg_connect("host=nonexistent_host");          

$result = @pg_exec($db_handle, "SELECT * FROM item");
```

如果没有`@`符号，上面的第二行将生成一个错误，抱怨缺少有效的数据库连接(当然，假设您的错误报告级别足够高，会导致显示该错误)。

注意，上面的错误仍然可以通过测试`$result`的值来检测，所以抑制错误消息输出并不排除我们以编程方式处理错误情况。此外，我们可以使用`pg_errormessage()`函数在方便的时候显示错误消息。

##### 字符编码

如果 PostgreSQL 中启用了字符编码支持，PHP 将提供获取和设置当前客户端编码的函数。默认情况下，编码设置为`SQL ASCII`。

支持的字符集有:`SQL_ASCII`，`EUC_JP`，`EUC_CN`，`EUC_KR`，`EUC_TW`，`UNICODE`，`MULE_INTERNAL`，`LATINX (X=1...9)`，`KOI8`，`WIN`，`ALT`，`SJIS`，`BIG5`，`WIN1250`。

*T2`pg_client_encoding()`*

`pg_client_encoding()`函数将返回当前的客户端编码:

```
$encoding = pg_client_encoding($db_handle);
```

*T2`pg_set_client_encoding()`*

您可以使用`pg_set_client_encoding()`功能设置当前客户端编码:

```
pg_set_client_encoding($db_handle, 'UNICODE');
```

##### 梨树

PEAR(PHP 扩展和应用程序库)试图在 PHP 社区中复制 Perl 的 CPAN 的功能。引用 PEAR 的官方目标:

*   为库代码作者与其他开发人员共享代码提供一致的方法
*   给 PHP 社区一个共享代码的基础设施
*   定义帮助开发人员编写可移植和可重用代码的标准
*   提供代码维护和分发的工具

PEAR 主要是一个庞大的 PHP 类集合，它利用了 PHP 面向对象的编程能力。因此，您需要熟悉 PHP 处理类的语法。PHP 的面向对象扩展在这里有记载:[http://www.php.net/manual/en/language.oop.php](http://www.php.net/manual/en/language.oop.php)。

有关 PEAR 的更多信息，请访问:

*   [http://pear.php.net](http://pear.php.net)
*   [http://php.weblogs.com/php_pear_tutorials/](http://php.weblogs.com/php_pear_tutorials/)

**PEAR 的数据库抽象接口**

PEAR 包括一个数据库(DB)抽象接口，它包含在标准 PHP 发行版中。使用数据库抽象接口而不是直接调用数据库的本机函数的优点是代码独立性。如果您需要将您的项目移动到不同的数据库，这可能涉及到主要的代码重写。然而，如果您使用了数据库抽象接口，那么这个任务将是微不足道的。

PEAR 的 DB 接口还增加了一些增值特性，比如方便地访问多个结果集和集成错误处理。所有的数据库交互都是通过 DB 类和对象来处理的。这在概念上类似于 Perl 的 DBI 接口。

数据库抽象接口的主要缺点是在应用程序执行时会导致性能开销。同样，这是一种在代码灵活性和性能之间进行权衡的情况。

*使用数据库接口*

下面的例子说明了 DB 接口的使用:注意，这个例子假设已经安装了 PEAR DB 接口，并且可以通过当前的`include_path`设置找到它。这两个选项都是较新的 PHP4 安装的默认选项:

```
<?php           

/* Import the PEAR DB interface. */           

require_once "DB.php";           

/* Database connection parameters. */           

$username = "jon";           

$password = "secret";           

$hostname = "localhost";           

$dbname = "bpsimple";           

/* Construct the DSN -- Data Source Name. */           

$dsn = "pgsql://$username:$password@$hostname/$dbname";           

/* Attempt to connect to the database. */           

$db = DB::connect($dsn);           

/* Check for any connection errors. */           

if (DB::isError($db)) {           

    die ($db->getMessage());           

}           

/* Execute a selection query. */           

$query = "SELECT title, fname, lname FROM customer";           

$result = $db->query($query);           

/* Check for any query execution errors. */           

if (DB::isError($result)) {           

    die ($result->getMessage());           

}           

/* Fetch and display the query results. */           

while ($row = $result->fetchRow(DB_FETCHMODE_ASSOC)) {           

    $fullname = $row['title'] . " ";           

    $fullname .= $row['fname'] . " ";           

    $fullname .= $row['lname'];           

    echo "Customer: $fullname<br>n";           

}           

/* Disconnect from the database. */           

$db->disconnect();           

?>
```

正如您所看到的，这段代码虽然没有直接使用任何 PostgreSQL 函数，但仍然遵循我们前面示例的相同编程逻辑。不难看出，上面的例子可以很容易地适用于另一种类型的数据库(例如 Oracle 或 MySQL)。

**PEAR 的错误处理**

使用 PEAR DB 接口为开发人员提供了许多额外的优势。例如，PEAR 包含一个集成的错误处理系统。下面是一些演示错误处理的代码:

```
<?php           

/* Import the PEAR DB interface. */           

require_once 'DB.php';           

/* Construct the DSN -- Data Source Name. */           

$dsn = "pgsql://jon:secret@localhost/bpsimple";           

/* Attempt to connect to the database. */           

$db = DB::connect($dsn);           

/* Check for any connection errors. */           

if (DB::isError($db)) {           

    die ($db->getMessage());           

}
```

上面，我们看到了 PEAR 错误处理能力的第一个实例:`DB::isError()`。如果对`DB::connect()`的调用由于某种原因失败，它将返回一个`PEAR_Error`实例，而不是一个数据库连接对象。我们可以使用`DB::isError()`函数测试这种情况，如上所示。

知道发生了错误很重要，但找出错误发生的原因更重要。我们可以使用`PEAR_Error` 对象的`getMessage()`方法检索错误消息的文本(在本例中，是由 PostgreSQL 生成的连接错误)。上面的例子也证明了这一点。

我们的示例继续一些查询:

```
/* Make errors fatal. */           

$db->setErrorHandling(PEAR_ERROR_DIE);           

/* Build and execute the query. */           

$query = "SELECT title, fname, lname FROM customer";           

$result = $db->query($query);           

/* Check for any query execution errors. */           

if (DB::isError($result)) {           

    die ($result->getMessage());           

}           

while ($row = $result->fetchRow(DB_FETCHMODE_ASSOC)) {           

    $fullname = $row['title'] . " ";           

    $fullname .= $row['fname'] . " ";           

    $fullname .= $row['lname'];           

    echo "Customer: $fullname<br>n";           

}           

/* Disconnect from the database. */           

$db->disconnect();           

?>
```

注意，我们通过调用`setErrorHandling()`方法改变了 PEAR 的错误处理行为。如果错误发生，将错误处理行为设置为`PEAR_ERROR_DIE`将导致 PHP 致命退出。

以下是其他错误处理行为的列表:

*   简单地返回一个错误对象(默认)
*   `PEAR_ERROR_PRINT`打印错误信息并继续执行
*   使用 PHP 的`trigger_error()`函数引发一个内部错误
*   `PEAR_ERROR_DIE`打印错误信息并中止执行
*   `PEAR_ERROR_CALLBACK`在中止执行之前，使用回调函数处理错误

关于`PEAR_Error`类和 PEAR 错误处理的更多信息可以在这里获得:[http://php.net/manual/en/class.pear-error.php](http://php.net/manual/en/class.pear-error.php)。

**查询准备和执行**

PEAR 还包括一个准备和执行查询的句柄方法。下面是一个简短的例子，演示了 DB 接口的`prepare()`和`execute()`方法。这个例子假设我们已经有了一个有效的数据库连接(来自一个`DB::connect()`):

```
/* Set up the $items array. */           

$items = array(           

    '6241527836190' => 20,           

    '7241427238373' => 21,           

    '7093454306788' => 22           

);           

/* Prepare our template SQL statement. */           

$statement = $db->prepare("INSERT INTO barcode VALUES(?,?)");           

/* Execute the statement for each entry in the $items array. */           

while (list($barcode, $item_id) = each($items)) {           

    $db->execute($statement, array($barcode, $item_id));           

}
```

对于不熟悉预准备 SQL 语句的人来说，这个示例可能需要一些解释。

对`prepare()`方法的调用创建了一个可以重复执行的 SQL 模板。请注意语句中使用问号指定的两个通配符点。稍后当我们调用`execute()`方法时，这些占位符将被替换为实际值。

假设我们有一个包含条形码和商品 id 的数组`$items`,我们将希望对每个商品执行一次数据库插入。为此，我们构建了一个循环来迭代`$items`数组中的每个条目，提取条形码和商品 ID，然后执行准备好的 SQL 语句。

如上所述，`execute()`方法将把准备好的语句中的占位符值替换为以数组形式传递给它的第二个参数中的值。在上面的例子中，这将是`array($barcode, $item_id)`参数。占位符值是按照指定这些新值的顺序替换的，因此正确使用它们很重要。

希望您会发现 PEAR DB 接口的这个特性在您自己的项目中非常有用。

在本章中，我们研究了从 PHP 脚本语言访问 PostgreSQL 数据库的各种方法。

我们讨论了数据库连接、查询构建和执行、结果集操作和错误处理的各个方面。我们还介绍了 PEAR 数据库抽象接口。

在此基础上，您现在应该有足够的基本工具来开始开发自己的基于 web 的数据库应用程序。

## 分享这篇文章****