# 体验 PHP5 附带的项目

> 原文：<https://www.sitepoint.com/a-taste-of-projects-to-come-with-php5/>

只是想引起人们对一些专门为 PHP5 编写的有趣项目的注意。

首先是 [cowiki](http://www.develnet.org/) ，尽管它只有 0.3.x 版本，但已经证明是成功的，并为 [phpCommunity](http://www.phpcommunity.org) 网站提供了动力。代码*非常*有条理，充满模式，很明显作者懂 Java。似乎没有任何对其他 PHP 库的依赖，因此 wheels 正在被重新发明，但同样地，它为任何寻找如何使用 PHP5 新特性的技巧的人提供了一个有趣的研究。对我来说，这有点太受 Java 的启发了，例如，有一个基类“Object”——关于这些行的想法，请阅读 [PHP 应用程序设计关注点](http://wact.sourceforge.net/index.php/PHP%20Application%20Design%20Concerns)。当然每个都有自己的特点，也许内置一些额外的抽象会有所帮助，而 PHP5 本身仍然是一个移动的目标。

另外两个有趣的项目可以在 http://www.phpdb.org 找到。

首先是 [Creole](http://creole.phpdb.org/) ，一个专门为 PHP5 编写的数据库抽象库，模仿 Java 的 JDBC。这肯定会吸引大量的 Java 用户，而且代码也组织得非常好。下面是一个连接执行查询的示例；

 `$dsn = "mysql://dbuser:dbpass@localhost/testdb";`

 `$ conn = Creole::getConnection($ DSN)；

$ RS = $ conn--> execute query(" SELECT id，name FROM users ")；` 

`while($rs->next()) {
echo $rs->getString("name") . " (" . $rs->getInt("id") . ")n"
}`

我对 PHP 中的 getInt()之类的方法有点紧张。我也想知道 JDBC 之后的建模，它(我的观点)过于强调“例外”的情况(毕竟 Java 是一种通用语言)，而不是专注于使常见情况尽可能简单(将选择转储到 HTML 表中是 PHP 中的常见情况)。

相比之下，WACT 的 [DBC](http://wact.sourceforge.net/api/WACT_DB/DBC.html) (数据库连接管理器)API 使得获取一个结果集(包括连接到数据库，连接设置存储在一个 ini 文件中)减少到；

 `$DataSet = & DBC::NewRecordSet('SELECT id, name FROM users');`

`while ( $DataSet->next() ) {
echo $DataSet->get('name') . " (" . $DataSet->get('id') . ")n"
}`

得到一个分页的结果集看起来像:

 `$PagedDataSet = & DBC::NewPagedRecordSet('SELECT id, name FROM users', $Pager);`

其中$Pager 是“分页组件”的一个实例。

无论如何，抛开这些担忧不谈，[http://www.phpdb.org](http://www.phpdb.org)上的另一个项目是 [Propel](http://propel.phpdb.org/) ，一个基于 Apache[Torque](http://db.apache.org/torque/)(Java)的对象关系映射工具。一旦构建了映射层，您就可以对 SQL(和底层数据库抽象层)一无所知，并且可以纯粹处理对象。上面的例子可能会变成:

 `$Users = UserPeer::doSelect();`

`foreach($Users as $User) {
echo $User->getName() . " (" . $User->getId() . ")n"
}`

最后一个项目是 [Phing](http://phing.tigris.org/) ，这是一个 PHP 构建工具，与 Java 的 [Ant](http://ant.apache.org/) 有很多共同之处。任何人都想为 PHP 编写一个类似 ant 的构建工具(它已经进入 2.x 版本号),这表明并非所有人都同意 PHP =小型网站……

这最后三个项目来自与二进制云相关的开发人员。我相信，一个 PHP 框架项目，我希望它能更多地暴露在光天化日之下(少数人对此赞不绝口，而我们其余的人则在寻找“下载”链接……)。

这是未来的积极信号。如果更多的 PHP 项目遵循这种方式，那么 PHP5“企业就绪”的承诺将成为现实。

## 分享这篇文章