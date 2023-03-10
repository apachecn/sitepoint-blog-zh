# 用 Perl 访问 MySQL 数据库

> 原文：<https://www.sitepoint.com/access-mysql-database-perl/>

在 [SitePoint 论坛](http://www.sitepointforums.com)上，最常见的 Perl 相关问题之一是，“我如何用 Perl 访问我的数据库？”Perl 可以与市场上几乎所有类型的数据库一起工作，但是今天我将使用 MySQL 作为例子。

 **出于演示的目的，假设我们有一个名为“shoes”的数据库和一个名为“subscribers”的表。这是表格的布局:** 

**`create table subscribers (
uid int not null primary key auto_increment,
username varchar(32) not null,
emailaddr varchar(32) not null
);`
我们将用 DBI 模块和 DBD::mysql 数据库驱动程序连接到 MySQL。这些模块不是标准的 Perl 发行版附带的，所以您必须自己获得它们。如果您维护自己的服务器，请遵循以下说明，但是如果您有一个 Web 主机，如果他们还没有安装模块，他们应该能够为您负责安装。** 

##### 安装模块

要在作为 Cygwin 的一部分安装的类似 Unix 的操作系统或 Windows 上的 Perl 上安装 DBI 和 DBD::mysql 模块，请运行以下命令。

注意，你必须已经安装了 MySQL，否则你将无法安装 DBD::mysql 模块。此外，如果您没有以这种方式安装任何其他模块，您将被询问配置选项。幸运的是，这些是不言自明的。输入完配置值后，模块安装将开始。

你会看到几个屏幕的文本飞逝而过，但最终它会让你回到友好的命令提示符下。

很少会有任何错误，因为这些模块已经过数千人的测试。然而，如果你不幸遇到错误，在谷歌上快速搜索可能会找到解决方法。

##### 使用数据库

现在模块和数据库驱动程序已经安装好了，我们可以进入实际的 Perl 代码来处理数据库了。现在，我们将只连接到数据库。

`# set the data source name
# format: dbi:db type:db name:host:port
# mysql's default port is 3306
# if you are running mysql on another host or port,
#    you must change it
my $dsn = 'dbi:mysql:shoes:localhost:3306';

# set the user and password
my $user = 'user';
my $pass = 'pass';

# now connect and get a database handle  
my $dbh = DBI->connect($dsn, $user, $pass)
 or die "Can't connect to the DB: $DBI::errstrn";`
好的，那部代码里有很多新东西。首先，我们定义了 DSN，即数据源名称。这告诉 DBI 在哪里可以找到您的数据库服务器，以及您将在什么数据库上操作。然后，我们将用户名和密码设置为。最后，我们调用`DBI->connect`来实际连接数据库(别忘了输入自己的用户名和密码！)，如果连接不成功，我们会打印出一个错误。从这个命令中，我们获得了一个数据库句柄，我们将使用它在数据库上运行查询。

查询的执行需要两个步骤:准备和执行。首先，您必须像这样准备查询:

`my $sth = $dbh->prepare('insert into subscribers(username, emailaddr)
values "jim", "jim@microsoft.com")');`

然后您可以执行它:

`$sth->execute();`
因为我们运行了一个`INSERT`查询，所以没有任何要检索的结果。但是如果我们使用`SELECT`呢？我们如何获得数据？像这样:

`my $sth = $dbh->prepare("select username, email from subscribers");
$sth->execute;

while(@row = $sth->fetchrow_array()) {
 print "$row[0]: $row[1]<br>";
}`
如你所见，`$sth->fetchrow_array`返回一个数组的结果。如果您愿意，您可以像这样编写循环，使它更具可读性:

不是将值分配给一个普通的数组，而是将每一行的值分配给不同的变量。

##### 构建 Web 应用程序

现在让我们使用 DBI 创建一个(有点)有用的 Web 应用程序来管理一个虚拟鞋店的目录订阅者。将有两页，`view.pl`和`add.pl`，数据库模式将与本指南顶部的模式相同。同样，请记住，您必须在这些脚本中输入自己的用户名和密码，而不是默认的“user”和“pass”。

下面是 add.pl:

```
use CGI;  

use DBI;  

print CGI::header();  

my $username = dbquote(CGI::param('name'));  

my $email    = dbquote(CGI::param('email'));  

unless($username) {  

  print <<PAGE;  

<h1>Add a Subscriber</h1>  

<form action=add.pl method=post>  

Name: <input type=text name=name><br>  

Email: <input type=text name=email><br>  

<input type=submit value="Add Subscriber">  

</form>  

PAGE  

exit;  

}  

# connect  

my $dbh = DBI->connect("dbi:mysql:shoes:localhost:3306", "user", "pass");  

# prepare the query  

my $sth = $dbh->prepare("insert into subscribers(username, emailaddr)  

 values('$username', '$email')");  

# execute the query  

$sth->execute();  

print <<PAGE;  

<h1>User Added</h1>  

The user $username was just added.  Want to   

<a href=add.pl>add another</a>? PAGE  

sub dbquote {  

  my($str) = @_;  

$str =~ s/"/\"/g;  

  $str =~ s/\/\\/g;  

  $str =~ s/'/\'/g;  

  return $str;  

} 

And here is view.pl:

use DBI;  

use CGI;  

print CGI::header();  

print "<h1>Subscribers</h1>";  

# connect  

my $dbh = DBI->connect("dbi:mysql:shoes:localhost:3306", "user", "pass");  

# prepare the query  

my $sth = $dbh->prepare("select username, emailaddr from subscribers");  

# execute the query  

$sth->execute();  

while(my ($username, $email) = $sth->fetchrow_array()) {  

  print "$username: $email<br>n";  

} 

That's it!  If you're after more information, try these resources:

```

*   [编程 Perl DBI](http://www.amazon.com/exec/obidos/ASIN/1565926994/webmasterresou05) (奥赖利)

*   [Perl 一言以蔽之](http://www.amazon.com/exec/obidos/ASIN/1565922867/webmasterresou05)(奥赖利)

*   DBI 文档(DBI 佩尔多克)

## 分享这篇文章