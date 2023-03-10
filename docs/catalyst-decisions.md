# 催化剂决策

> 原文：<https://www.sitepoint.com/catalyst-decisions/>

继关于 [Catalyst](http://catalyst.perl.org) ([ [1](https://www.sitepoint.com/looking-at-catalyst/) 、[ [2](https://www.sitepoint.com/catalyst-impressions/) 、[ [3](https://www.sitepoint.com/setting-up-a-catalyst-development-environment/) 、[ [4](https://www.sitepoint.com/who-gets-rest/) )的各种文本流之后，下一个问题是选择 Catalyst 插件。

我个人是通过在命令行上做[粘合层](http://koranteng.blogspot.com/2005/05/get-on-bus.html)的事情来了解 Perl 的，所以各种 web 相关的 API 几乎都是未知的领域。更重要的是，已经通过 [DBI](http://dbi.perl.org/) 幸福地传递 SQL(也许在 PHP 中最接近 DBI 的是 [PDO](http://www.php.net/pdo) ，尽管 [PEAR DB](http://pear.php.net/package/DB/) 我相信是受它的启发)，但是 Catalyst 使你面对的不仅仅是 DBI，而是更多的选择。

无论如何——第一步是看看有什么可能是最容易做到的[这里](http://cpan.uwinnipeg.ca/search?query=Catalyst&mode=dist)—那里列出的许多软件包提供独特的附加功能，但其他的提供竞争的替代方案。几乎在所有情况下，这些都是 CPAN 其他库的包装器，以一种易于使用 Catalyst 的形式包装它们。至少在开始时，两个关键的选择是“模型”和“视图”，所以这是一个调查各种`Catalyst::Model::*`和`Catalyst::View::*`的问题。

首先从后者开始，视图使用什么，我们正在谈论模板引擎。Catalyst 文档似乎表明[模板工具包](http://www.template-toolkit.org/) (TT)是首选，如果你从 CPAN 安装了`Bundle::Catalyst`，那么它已经和它的`Catalyst::View::TT`包装一起被引入了 TT。模板工具包(显然——传说中)是 [Smarty](http://smarty.php.net) 的灵感来源，基本原理是一样的——命令式语法(if/else/etc。)嵌入 HTML。虽然这不是我做模板的首选方式(WACT 插件和你需要知道的关于模板的一切——见[这里](http://www.phpwact.org/pattern/template_view))，尽管有一个用于 [PHP::Interpreter](http://www.schlossnagle.org/~george/blog/index.php?/archives/247-Perl-from-PHP-from-Perl-from-....html) 的 Catalyst 插件，我现在只考虑显而易见的/稳定的选择，加上 TT 的学习曲线，从基础实验来看，相当浅。这并不是说 TT 是唯一明智的选择——这是我听说过的另一个明显的名字[梅森](http://www.masonhq.com/)亚马逊和 del.icio.us 等公司都在使用这个名字，但直觉告诉我，学习曲线要陡峭得多。不过，这里可以接受建议。

在模型方面，这基本上是在 CPAN 的两个对象关系映射层之间的选择——[Class::DBI](http://www.class-dbi.com)和 [DBIx](http://cpan.uwinnipeg.ca/htdocs/DBIx-Class/DBIx/Class.html) (或者简单的通过 DBI 的普通 SQL，我猜不需要插件)。围绕 DBIx 阅读似乎是“热门话题”，但请注意“实验性”警告。目前，我在这里没有任何强烈的意见——因为我只计划了很少的表，倾向于老式的 SQL + DBI。

以了解更多的名义，与`Catalyst::Plugin::CDBI::Sweet`一起玩——这里有一个“Hello World”以引起兴趣；

假设从现在开始使用 Ubuntu，如果你安装了`Bundle::Catalyst`,你将需要添加一些零碎的东西并运行一些命令；

```
$ sudo apt-get install sqlite3
$ cd
$ sqlite3 example.db
sqlite> CREATE TABLE user ( name VARCHAR(15) PRIMARY KEY );
sqlite> .quit
$ sudo perl -MCPAN -e shell
cpan> install DBD::SQLite
cpan> install Class::DBI::SQLite
cpan> install Catalyst::Model::CDBI
cpan> install Catalyst::Model::CDBI::Sweet
$ catalyst.pl Example
$ cd Example
$ ./script/example_create.pl model User CDBI dbi:SQLite:/home/harryf/example.db < < insert correct path

```

这就建立了一个只有一个表的数据库(称为 user，包含一个列“name”)和框架 Catalyst 应用程序。

现在在`~Example/lib/Example/Model`中创建了一个文件`CDBI.pm`包含；

```
 package Example::Model::CDBI;
use base 'Catalyst::Model::CDBI::Sweet';
Example::Model::CDBI->connection('dbi:SQLite:/home/harryf/example.db'); 

```

这是一个用于其他模型类扩展的基类，它建立了到数据库的连接，并从`Catalyst::Model::CDBI::Sweet`继承，而`Catalyst::Model::CDBI::Sweet`又从`Class::DBI::Sweet`继承，而`Class::DBI::Sweet`又从`Class::DBI`继承。获取这些类的 API 文档的最快方法是在命令行上，例如`$ man Class::DBI`，而不是在网上冲浪。

为了给我的表提供一个模型，在`~Example/lib/Example/Model/User.pm`中放置了以下内容

```
 package User::Model::Tag;

use strict;
use base 'Example::Model::CDBI';

__PACKAGE__->table('user');
__PACKAGE__->columns( Primary => qw[ name ] ); 

```

就是这样——它继承了前面的类，继承了到`Class::DBI`的所有东西，所以手头有很多方法。向表中插入一些记录，创建了一个脚本`~Example/lib/insert.pl`包含；

```
 #!/usr/bin/perl -w
use strict;
use Example::Model::User;

my @names = qw(Joe Mary John Mike);

foreach my $name ( @names ) {
    my $user = Example::Model::User->create({'name'=>$name});
    print "Inserting $namen";
} 

```

现在可以这样运行:

```
$ chmod +x insert.pl
$ ./insert.pl

```

为了再次选择插入的行，下面的脚本在`~Example/lib/select.pl`中完成这项工作；

```
 #!/usr/bin/perl -w
use strict;
use Example::Model::User;

my $userClass = Example::Model::User->new();
my $iterator = $userClass->retrieve_all;

while ( my $user = $iterator->next ) {
    print $user->{name}."n";
} 

```

至少这么多是很容易的，但是离真正的用例还有很长的路要走...

再看一次奥姆的《T1 》,让我进入另一个小的兰特/ WACT 插头。令人惊讶的是[杰夫](http://www.procata.com/blog)似乎是唯一一个想到构建[数据库简化层](http://cvs.sourceforge.net/viewcvs.py/wact/wact/framework/db/db.inc.php?view=auto)(我自己的术语)——使用普通的 SQL，它是轻量级的，并且它使得为*常见的网络相关问题*获取数据变得异常容易。例如，需要一个来自数据库的索引数组来填充一个 select 标记。这条班轮怎么样？

```
 $countries = DBC::getOneColumnAsArray('SELECT name FROM countries'); 

```

它负责建立连接(如果需要的话)、执行查询、遍历结果并返回一个索引数组。但是嘿，它只是一个 PHP 框架[詹姆斯](http://weblog.rubyonrails.com/articles/2005/11/06/liquid-templates-announced)；)

## 分享这篇文章