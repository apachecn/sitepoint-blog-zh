# 将您的站点从 MySQL 迁移到 PostgreSQL —第 2 部分

> 原文：<https://www.sitepoint.com/site-mysql-postgresql-2/>

【2001 年 6 月，我将[我网站的](http://www.designharbor.org/)后台数据库从 MySQL 切换到 PostgreSQL。我只花了一天时间。从那时起，Postgres 的工作一直完美无缺，证实了我的选择是正确的。

在本系列的第一篇教程中，我解释了如何将数据从 MySQL 转换成 Postgres，同时避免潜在的陷阱。我也解释了我转到 Postgres 的原因。在本文中，我将指导您完成转换过程的另一半，并解释如何获取现有的 MySQL PHP 代码并使其对 Postgres 友好。

##### 准备

在您开始编写代码之前，您需要做一些准备工作。转换过程会根据你的编码技巧和网站的复杂程度而有所不同，但是如果你试图在一个活跃的网站上改变一些东西，这肯定会很明显。为了避免在网站上工作时出现令人讨厌的错误，请在 Web 服务器的工作区中制作一份网站副本。在我的例子中，我在修改代码时设置了一个特殊的子域进行测试。由于站点根据配置文件中的一些基本设置自动计算 URL，这很容易，但是您可能需要对您的代码进行一些更改，以使它能够工作，无论您选择将正在处理的版本放在子域中、站点的子文件夹中、另一个 Web 主机上还是本地开发机器上。

记住:如果你把正在进行的拷贝放在另一台机器上，要确保 Web 服务器的配置和实时站点一样，否则你可能会遇到麻烦。如果您的 Web 主机只允许来自实时 Web 服务器的 Postgres 连接，您可能还需要将您的 Postgres 数据复制到开发服务器。有关复制 Postgres 数据库的更多信息，请参见 www.postgrsql.org 上`pg_dump`和`pg_restore`的文档。

一旦你复制了你的站点，并测试了它以确保它能正常工作，你就准备好拔出手术刀开始攻击了。

##### PHP 手术:破解代码

首先，如果还没有中央配置文件，请创建一个。这使得在您的代码中更改 Postgres 服务器设置变得容易，并且使这些更改立即影响您的整个站点。确保配置文件是*而不是*在你的 Web 服务器上的 Web 可访问点，这是一个安全风险。PHP 的默认包含目录是`/usr/local/lib/php/`，所以您可能想让您的 Web 主机在那里为您不想从 Web 访问的任何配置文件创建一个目录。还要确保其他使用你的网络主机服务的人看不到它。如果你的站点，比如我的站点，包含一个标准的头文件，你可以从那里包含 Postgres 设置文件。事情是这样的:

```
<?php  

// /usr/local/lib/php/mysite/configfile.php  

$hostname = "localhost";  

$username = "username";  

$database = "mydb";  

$password = "mypasswd";  

?>  

<?php  

//Standard HTML Header  

include("mysite/configfile.php");  

?>  

<html>  

<head>  

<title>Bill's Kazoos</title>  

<head>  

<body>  

header continues...
```

像上一个例子那样的中央信息存储库使得切换数据库系统变得更加容易。说到切换，是时候开始编辑文件了。

##### 连接和查询

PHP 的 MySQL 函数与 Postgres 函数非常相似，这使得代码转换变得很容易，但又非常不同，这使得代码转换变得有点古怪。事实上，您可能希望编写一些特殊的函数来使转换更容易。但在此之前，这里有一个连接和查询差异的概述:

连接到 MySQL 数据库需要两个命令:

```
$connection_id=mysql_connect($hostname, $username, $password);  

mysql_select_db($database, $connection_id);
```

或者，如果您使用持续连接:

```
$connection_id=mysql_pconnect($hostname, $username, $password);  

mysql_select_db($database, $connection_id);
```

但是，PHP 的 Postgres connect 函数采用单个字符串参数，不像 MySQL 函数采用多个字符串参数。Postgres 函数还允许您在最初连接时指定使用哪个数据库。这里有一个例子:

```
$connection_id=pg_connect("host=$hostname dbname=$database user=$username   

password=$password");
```

持久连接功能的工作方式完全相同，只是它需要调用`pg_pconnect()`。

PHP 的 MySQL 和 Postgres 查询函数也有点不同。MySQL 的查询函数是`$result_data = mysql_query("query goes here", $connection_id);`，但是 Postgres 的是反过来的:`$result_data = pg_exec($connection_id, "query goes here")`。

如您所见，PHP 的 MySQL 和 Postgres 在连接和查询方面的支持差异并不大，但是函数参数的差异使得编辑过程更慢。为了加快速度，您可以编写一些包装器函数，它们采用与 MySQL 函数相同的参数，但是连接到 Postgres。如果你有一个实用函数的中央库，你可以把它们放在那里。您也可以将它们放在前面解释的配置文件中，因为它会自动包含在每个页面中。

```
//connect to database  

function postg_connect($hostname, $username, $password, $database)  

{  

  return pg_connect("host=$hostname dbname=$database  

                     user=$username password=$password");  

}  

//alternately, if you use only one database  

//you can make best use of the variables in  

//your configuration file by doing the following,  

//which eliminates the need for any variable   

//passing at all.  

function postg_autoconnect()  

{  

  global $hostname, $username $password $database;  

  return pg_connect("host=$hostname dbname=$database  

                     user=$username password=$password");  

}  

//query-making function  

function postg_query($query, $connection_id)  

{  

  return pg_exec($connection_id, $query);  

}
```

无论是否使用包装函数，转换连接代码和查询调用都非常简单。Postgres 基本上能够支持 MySQL 下使用的旧 SQL 查询，但是您可能需要对您的查询进行一些调整。由于数据模型和代码因网站而异，我在这里就不解释了。然而，转换 SQL 并不难。首先转换代码，然后看看 Postgres 中哪些查询失败。接下来将 MySQL 语言参考资料与 T2 的 PostgreSQL 用户指南进行对比。除了寻找 MySQL 特性的 Postgres 等价物之外，您可能不需要做更多的事情，因为 Postgres 支持所有常见的 MySQL 特性。

既然您已经使连接和查询代码 Postgres 友好，那么是时候动手了。PHP 的 MySQL 和 Postgres 支持之间的差异为处理结果集设置了几个障碍，这需要更多的代码调整。

##### 让我们看看这些结果

PHP 的 Postgres 结果集处理程序可以做 MySQL 处理程序所能做的一切；它们只是略有不同。这些微小的差异可能只需要对代码做一点点修改，但它们也可能需要一些复杂的编程。

首先看看 MySQL 和 Postgres 有什么相似之处。以下是常见的 MySQL 结果处理函数及其 Postgres 对应函数的列表:

****MySQL****

**MySQL _ num _ rows($ result)***返回结果集中的行数。这只对 SELECT 语句*有效

**Mysql _ affected _ rows($ result)**返回插入、更新或删除 SQL 查询中受影响的行数

**MySQL _ fetch _ object($ result)***取一行数据，作为对象返回。可以使用类语法访问字段名。(如* `$field1 = $var->field1;` *)该函数保留一个内部值，以确保每次被调用时都返回下一行。*

**MySQL _ fetch _ row($ result)***该函数以数值数组的形式返回结果集中的一行。可以使用从 0 开始的数组语法来访问这些值。(如* `$field1 = $var[0]` *)。内部计数器跟踪行，并在每次被调用时传递新的行结果。*

**MySQL _ fetch _ array($ result)***这个与其他两个取函数相同，除了它返回一个包含行结果的关联数组(* `$field1 = $var["field1"];` *)。*

****Postgres****

**pg_numrows($result)** *的工作方式和它的 MySQL 对应物*完全一样

**pg _ cmd tuples($ result)***的工作方式就像它的 MySQL 对应物*

**pg_fetch_object($result，$ row)**获取结果集的指定行。必须包含 `$row` *值，没有内部计数器。否则，它就像相应的 MySQL 函数一样工作。*

**pg_fetch_row($result，$row)** *返回指定行结果的数值数组。行号< b >必须包含< /b >。与 MySQL 不同，没有内部计数器跟踪行结果。*

**pg_fetch_array($result，$row)** *除了指定要返回的行的要求和缺少内部计数器之外，与 MySQL 等同。*

关于 PHP 的 MySQL 和 Postgres 函数的更多信息，请参见关于 PHP.Net 的 [PHP 文档。](http://www.php.net/manual/en/)

PHP 的 MySQL 和 Postgres 支持之间最明显的区别在于对结果集的实际读取。MySQL 自动为您确定从哪一行返回结果，Postgres 要求您指定要读取的行。这往往会妨碍许多常见的 MySQL 数据读取算法。这里有几个你可能会遇到的这类问题的简短例子，以及解决它的两种方法。

```
//first common example:   

$rslt=mysql_query("SELECT * FROM blah", $connection_id);   

while($value=mysql_fetch_array($rslt))   

{   

  //do data handling stuff   

}   

//In the Postgres functions, the previous algo won't work,    

//since they require one to specify row number. The fix   

//goes like this (if you're not using the wrapper function   

//explained earlier):   

$rslt=pg_exec($connection_id, "SELECT * from blah");   

$limit=pg_numrows($rslt);   

for($rownum=0;$rownum<$limit;$rownum++)   

{   

  $value=pg_fetch_array($rslt, $rownum);   

  //do stuff here   

}
```

在前面的例子中，注意 Postgres 的代码有点长，因为行号是必需的。然而，如果您编写自己的计数包装函数，并将其与前面显示的包装函数结合使用，事情会变得容易得多。下面是添加了包装函数的实用程序文件。请注意对`postg_query()`的更改以及三个全局变量和结果收集函数的添加。

```
 <?php   

// /usr/local/lib/php/mysite/configfile.php   

$hostname = "localhost";   

$username = "username";   

$database = "mydb";   

$password = "mypasswd";   

//internal counting variables   

$fetch_array_counter=0;   

$fetch_object_counter=0;   

$fetch_row_counter=0;   

//wrapper functions to ease   

//transition to PostgreSQL   

//connect to database   

function postg_connect($hostname, $username, $password, $database)   

{   

  return pg_connect(host=$hostname, dbname=$database user=$username,    

password=$password");   

}   

//connect without having to pass values   

function postg_autoconnect()   

{   

  global $hostname, $username, $password, $database;   

  return pg_connect(host=$hostname, dbname=$database user=$username,    

password=$password");   

}   

//query-making function   

function postg_query($query, $connection_id)   

{   

  //set the globals to 0   

  global $fetch_array_counter, $fetch_row_counter, $fetch_object_counter;   

  $fetch_array_counter=$fetch_row_counter=$fetch_object_counter=0;   

  return pg_exec($connection_id, $query);   

}   

//pg_fetch_array() replacement   

function postg_fetch_array($rslt)   

{   

  global $fetch_array_counter;   

  $fetch_array_counter++; //add one to the counter   

  return pg_fetch_array($rslt, $fetch_array_counter);   

}   

//pg_fetch_row() replacement   

function postg_fetch_row($rslt)   

{   

  global $fetch_row_counter;   

  $fetch_row_counter++; //add one to the counter   

  return pg_fetch_row($rslt, $fetch_row_counter);   

}   

//pg_fetch_object() replacement   

function postg_fetch_object($rslt)   

{   

  global $fetch_object_counter;   

  $fetch_object_counter++; //add one to the counter   

  return pg_fetch_object($rslt, $fetch_object_counter);   

}   

?>
```

当然，如果您想在同一个循环中处理两个结果集*，前面的函数*将不会*起作用，因为每种提取类型只有一个内部计数器。如果出于某种原因，您需要交替读取多个结果集，您将不得不使用传统的 Postgres 方法。*

更改代码时可能遇到的另一个问题是缺少 MySQL 的`mysql_insert_id()`的 Postgres 等价物，它返回最后一个`INSERT`查询的索引值。PHP 文档的语言可能会误导人们认为`pg_getlastoid()`完成了这项工作，但事实并非如此。缺少这样的功能实际上并不是一个缺点，因为这是 Postgres 通过`SEQUENCE`系统允许多个自动递增字段的能力的结果。

幸运的是，获得最后一个 ID 很容易。序列信息可以通过 SQL 访问，所以下面的替换`mysql_insert_id()`是可能的:

```
function postg_insert_id($tablename, $fieldname)   

{   

  global connection_id;   

  $result=pg_exec($connection_id, "SELECT last_value FROM ${tablename}_   

${fieldname}_seq");   

  $seq_array=pg_fetch_row($result, 0);   

  return $seq_array[0];   

}
```

因为 Postgres 使用特殊的命名系统来命名序列，所以我在上面创建的函数需要 tablename 和 fieldname。当被调用时，上面的函数将检索用于表中任何`SERIAL`字段的最后一个序列值，即使有多个字段。

通过使用前面的技术，您应该能够让您的 MySQL 站点成功运行 PostgreSQL。然而，这只是第一步；请继续阅读，查看有用的 PostgreSQL 资源列表。

##### 进一步阅读的资源

首要的 PostgreSQL 资源可以在 [PostgreSQL 非 FAQ 文档站点](http://techdocs.postgresql.org)找到。这个有价值的资源提供了大量的书籍、快速参考、技术指南，甚至工作。它还有一个关于将后端数据库从 MySQL 改为 Postgres 的资源列表，以及关于 PostgreSQL 的故障排除帮助和信息。

PostgreSQL 提供的另一个很好的资源是 [PostgreSQL 交互式文档](http://www.postgresql.org/idocs/index.php)，在这里您可以找到任何关于使用 PostgreSQL 的信息。

Xach Beane 因其在 GIMP T1 上的工作而闻名，他也在 T2 写了一个脚本来将 MySQL 转储转换成 Postgres 转储。他的脚本处理的东西比我的系列文章中涉及的要多，所以请查看一下。当然，它不能完美地处理事情，所以你可能必须小心使用它。

Dobrica Pavlinusic 也写了一个脚本[来处理 MySQL 到 Postgres 的转换。再次注意，源代码声称自己无法处理某些事情，所以您可能不得不求助于手工编辑，正如我在上一篇文章中解释的那样。](http://www.rot13.org/~dpavlin/projects/mysql2pgsql)

一个非常全面的 PostgreSQL & PHP 教程可以在[这里](http://tomcat.users1.50megs.com/postgresql-tutorial/)找到。它从安装开始，并从头开始解释如何使用 PostgreSQL。本教程应该是初学者的必读。

Bruce Momjohan 写了一本关于 PostgreSQL 的书，名为 [PostgreSQL:简介和概念](http://www.ca.postgresql.org/docs/awbook.html)，由 Addison Wesley 出版。你甚至可以在网上[查看](http://www.ca.postgresql.org/docs/aw_pgsql_book/index.html)！

最后， [OpenDocs](http://www.opendocs.org) 正在联合[奥赖利](http://www.oreilly.com)发布[实用 PostgreSQL](http://stage.linuxports.com/projects/postgres/book1.htm) 。这本书预计在 2001 年 10 月初出版，但是你可以在 OpenDocs Linuxports.com 网站上阅读。

将你的网站后端从 MySQL 迁移到 PostgreSQL 是一个明智的选择。转换您的站点需要时间和精力，但是通过遵循适当的步骤，您的站点可以建立在一个更健壮的数据库系统上，为将来的扩展提供更多的选项。和我一样，你不会后悔的！

编码快乐！

## 分享这篇文章