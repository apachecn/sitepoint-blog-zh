# PHP 和 Microsoft SQL Server 上的存储过程

> 原文：<https://www.sitepoint.com/php-microsoft-sql-server/>

虽然 PHP 和 MySQL 的结合不像 PHP 和 MySQL 那样常见，但 PHP 和 Microsoft SQL Server 可以是一个强大的团队。您可以使用 PEAR 数据库抽象层轻松有效地查询 SQL Server 数据库，就像查询 MySQL 数据库一样。但是一旦您开始尝试使用 SQL Server 优于 MySQL 的主要优势之一——即存储过程——一些问题很快就变得明显起来:

首先，您的 PHP 代码通常就像动态构建 SQL 语句来执行一样混乱。以下面的存储过程定义为例:

```
GetCustomerList @StoreId int, 

@CustomerType varchar(50)
```

考虑构建 SQL 语句所需的 PHP 代码，该语句将从一些页面提交中执行该过程:

```
$sql = "EXEC GetCustomerList @StoreId="; 

$sql .= intval($_GET['StoreId']); 

$sql .= ', @CustomerType='; 

if ($_GET['CustomerType'] == '') { 

  $sql .= 'NULL'; 

} 

else { 

  $sql .= "'" . $_GET['CustomerType'] . "'" ; 

} 

// Assume you have an open PEAR database connection ($pearDB) 

$arrResults = $pearDB->getAll($sql);
```

不完全是最可读或最美观的代码块，是吗？

其次，当您想做一些比调用存储过程更高级的事情，而存储过程只是查询结果列表时，该怎么办呢？比方说，您想在存储过程中检索返回值或使用输出参数。PEAR 数据库库中没有直接内置的东西允许这样做。

最后，也是最重要的，考虑安全性。上面列出的代码产生了调用`GetCustomerList`过程所必需的 SQL 字符串，这些代码一点也不安全。因为假定来自用户输入的值`$_GET['CustomerType']`被直接用在 SQL 字符串中，没有检查不安全的内容或引号的转义，所以生成的 SQL 字符串很容易产生意想不到的结果。我们中的大多数人都读过关于 [SQL 注入攻击](https://www.sitepoint.com/blog/)的文章，以至于不认为它们是理所当然的(如果不是，我强烈建议你现在就阅读它们)。

令人高兴的是，PHP 内置了一些特性，可以帮助最小化这些攻击发生的可能性，例如“神奇引号”和相关的“stripslashes”函数。PHP 的这一功能可以用来“自动地”转义通过`GET`或`POST`值输入的所有字符串中的单引号，这使得这些字符串可以安全地用于数据库查询。如果你和我一样，过一段时间后，你会发现魔术报价选项有点麻烦。此外，我个人认为我依赖的全局设置越少越好——我已经将我的代码转移到新机器上太多次了，以至于不能依赖完全相同的服务器配置，除非这是一个例外。

##### 输入:SqlCommand 类

`SqlCommand`类是一个对象，旨在尽量减少这些问题，并帮助您生成更可读(可调试)、更强大、更安全的代码。基本用法相当简单，只包含 6 个常用的公共方法(可选参数显示在方括号中):

```
SqlCommand([$sCommandText], [$bGetReturnValue])
```

类实例化，通常用于定义存储过程名。

```
addParam($sParamName, [$oValue], [$sType], [$iLength], [$bForOutput])
```

配置一个必须传递给存储过程的参数。此处显示的$sType 选项是变量类型的确切 SQL Server 名称。目前支持的值包括:bit、int、smallint、tinyint、real、float、money、text、char、varchar、datetime 和 smalldatetime。

```
execute($oDB)
```

在不获取结果集的情况下执行(例如对于插入/更新/删除)。

```
getAll($oDB, [$mode])
```

执行并获得一个结果集(比如对于 select 语句)。

```
getReturnValue()
```

检索存储过程的返回值。

```
getOutputValue($sParamName)
```

检索存储过程中定义的任何输出参数的值。

要实际使用`SqlCommand`类，您必须首先实例化一个新的`SqlCommand`类型的对象，用您想要执行的存储过程的名称配置该对象，并设置任何需要的参数。然后，您可以选择是否返回结果集来执行您的存储过程(`getAll()`与`execute()`)。在此过程中，`SqlCommand`对象将验证参数值以确保它们可以安全使用(包括字符串值中的转义单引号)，并为您提供从过程中轻松检索返回和输出参数值的方法。

例如，要调用上面定义的示例存储过程，可以使用下面几行代码:

```
$oQuery = new SqlCommand('GetCustomerList');  

$oQuery->addParam('StoreId', $_GET['StoreId'], 'int');  

$oQuery->addParam('CustomerType', $_GET['CustomerType'], 'varchar', 50);  

// Assume you have an open PEAR database connection ($pearDB)  

$arrResults = $oQuery->getAll($pearDB);
```

这似乎比自己动态构建 SQL 字符串来调用这个过程更具可读性，不是吗？而且，因为您用实际的数据库字段类型定义了存储过程的每个参数，所以为了安全起见，`SqlCommand`类能够执行变量“类型化”——这是一种防止 SQL 注入攻击的强大方法。例如:

*   `int`使用内置的 PHP 函数`intval()`将值强制为整数
*   `datetime`使用正则表达式过滤器将值验证为日期
*   `varchar`如有必要，对值进行修整以满足最大大小要求，并过滤掉单引号

##### 返回值和输出参数呢？

对，我提到过，不是吗？默认情况下，检索返回值的能力是通过`SqlCommand`类实现的(如上所述)。所以，在我们的例子中，如果你想要得到`GetCustomerList`过程的实际返回值，你需要做的就是调用`$oQuery->getReturnValue()`。

输出参数几乎同样容易检索，但是在配置您的`SqlCommand`对象时需要一行额外的代码。假设我们有一个不同的存储过程，它实际上使用了一些输出参数，如下所示:

```
GetStoreCustomerCount @StoreId int, @CustomerCount int OUTPUT
```

在这种情况下，您可以修改您的`SqlCommand`类的配置，并轻松获得这个新输出参数的值，如下所示:

```
$oQuery = new SqlCommand('GetStoreCustomerCount');  

$oQuery->addParam('StoreId', $_GET['StoreId'], 'int');  

$oQuery->addParam('CustomerCount', null, 'int');  

// One extra method call is required to configure a parameter for output  

$oQuery->setParamDirection('CustomerCount', true);  

// Assume you have an open PEAR database connection ($pearDB)  

$arrResults = $oQuery->getAll($pearDB);  

// Now retrieve the value of your output parameter  

$count = $oQuery->getOutputValue('CustomerCount');
```

这就是全部了。您可以配置任意数量的输出参数，并通过`SELECT`语句检索它们的值(以及存储过程的返回值)，以及存储过程碰巧产生的任何查询结果。

##### 幕后到底发生了什么？

如果您曾经直接通过 Microsoft Query Analyzer 执行过存储过程，您会知道返回值和输出参数也不会自动返回，您必须做一些额外的工作。例如，为了找到一个存储过程的返回值，您需要按照下面的方式写一些东西:

```
DECLARE @res int  

EXEC @res = GetCustomerList 1, 'SomeType'  

SELECT @res
```

`SELECT @res`的查询结果包含您的存储过程调用的返回值。检索输出参数的值有些类似，通常如下所示:

```
DECLARE @out_value int  

EXEC GetStoreCustomerCount 1, @out_value OUTPUT  

SELECT @out_value
```

要跟踪的每个返回值或输出参数必须首先进行适当的声明(正确的数据类型)，然后包含在实际的存储过程调用中。然后可以在执行之后使用`SELECT`语句返回值本身。`SqlCommand`类自动用这些类型的语句包装您的数据库查询，以提供每种类型的功能。

##### 结论

存储过程是一种强大的工具，在开发与 SQL Server 交互的软件时应该广泛使用。虽然您可以在 PHP 中直接使用它们——类似于动态构造的查询语句——但您的代码可能会从使用更结构化的方法中受益。`SqlCommand`类应该提供这种结构，并在这个过程中帮助提高项目的简单性、可维护性和安全性。

别忘了从本文[这里](https://www.sitepoint.com/examples/sqlcommand/SqlCommand.zip)下载代码。

## 分享这篇文章