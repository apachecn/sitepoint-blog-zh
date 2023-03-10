# HTML::Template 简介

> 原文：<https://www.sitepoint.com/introducing-html-template/>

全面的 Perl Archive Network (CPAN)包含大量的模块，使开发人员的工作变得更加容易。在开始任何新项目之前，你最好浏览一下 http://cpan.org 的宝藏。

例如，在本教程中，我将简要介绍如何安装 HTML::Template 模块。但是首先，让我们看看如何在您的系统上安装 Perl 模块。

##### 安装 Perl

***在 Windows 上安装 Perl***

在 http://www.activestate.com 可以免费下载 Perl for Windows。安装一个模块非常简单；但是，它要求模块是 ActiveState 的`ppd`格式。

可以从`ppm`外壳进行安装:

```
C:>ppm 

PPM - Programmer's Package Manager version 3.1\. 

Copyright (c) 2001 ActiveState SRL. All Rights Reserved. 

Entering interactive shell. Using Term::ReadLine::Stub as readline library. 

Type 'help' to get started. 

ppm> install HTML-Template
```

就这么简单。如果存在任何依赖项，将首先安装它们。注意，并不是所有的 Perl 模块都是为`ppm`打包的。要了解某个模块是否可用，请在 shell 中键入`search <module>`。

如果您需要一个不在 ActiveState 的库中的模块，那么有可能其他人已经打包了它。通过安装 PPM::Repositories 模块，然后运行以下代码，您可以找到附加存储库的列表:

```
use PPM::Repositories; 

for my $rep ( keys %Repositories ) { 

    next unless $Repositories{$rep}->{Active}; 

    next unless grep { $_ == 5.8 } @{ $Repositories{$rep}->{PerlV} }; 

    print $rep, $/, $Repositories{$rep}->{location}, $/, 

      $Repositories{$rep}->{Notes}, $/, $/; 

}
```

如果没有其他方法，您可以手动安装该模块。我们将在下面的 Unix/Linux 部分讨论如何做到这一点。

***Unix/Linux 上的 Perl 安装***

可以通过两种方式在 Unix 和 Linux 上安装 Perl:手动或自动。

*手动方式*

下载模块，解压缩。

```
$ tar zxvf HTML-Template-xx.tar.gz 

$ cd HTML-Template-x.x 

$ perl Makefile.PL 

$ make 

$ make test 

$ make install
```

如果你想在 Windows 下手动安装该文件，你需要从微软的[下载 nmake.exe。然后，只需将上面说明中的`make`替换为`nmake`。](http://support.microsoft.com/default.aspx?scid=kb;en-us;132084#appliesto)

虽然大多数模块都是纯 Perl，但有些可能包含 C 代码，在这种情况下，您将需要一个 C 编译器。(对于`ppm`包，从来不需要编译器)。

在安装模块之前，请务必仔细阅读自述文件。

*自动方式*

一些模块依赖于其他模块，而其他模块又可能有其他依赖关系。安装所有这些模块可能有点乏味，但是有一个替代方法:安装`CPAN`包。然后，只需从命令行调用`cpan` shell:

```
$ cpan
```

第一次运行`cpan`时，它会自行配置。回答提示，配置完成后，您就可以安装您的软件包了:

```
cpan> install HTML::Template
```

如果存在任何依赖项，`cpan`可以获取并安装它们。

现在您已经知道了如何安装 Perl 模块，让我们仔细看看 Perl 的一个模板解决方案:HTML::Template。

##### HTML::Template 简介

Perl 作为一种原型语言非常棒，因为它非常具有表现力:开发人员可以用几行代码做很多事情。由此得出的一个推论是，一个小应用程序很容易演变成比预期大得多的东西。维护和扩展这样的应用程序会变得非常麻烦。

使用传统的 CGI 技术来编写大型 Web 应用程序是一种扩展性不好的方法。如果您是唯一的开发人员，将 HTML 与 Perl 混合使用可能是可以容忍的，但是当您是团队的一员时，这种方法就行不通了。如果设计需要更改，典型的 Web 设计人员不会想要开始更改 Perl 代码。

大多数 Web 语言都提供了一些将应用程序代码与标记代码分开的方法。在大多数情况下，HTML 放在单独的模板文件中，这些模板文件充当动态数据的容器。对于像 PHP 这样的语言，模板可以被认为是核心语言本身的扩展，尽管，如果你像我一样，你仍然会更喜欢第三方模板解决方案，比如 [Smarty](http://smarty.php.net/) 。

Perl 有许多模板解决方案。Mason 和 Template Toolkit 是两个最流行的解决方案，提供了强大的功能。开发人员可以在他们的模板中放置复杂的逻辑，但是除非他们遵守纪律，否则他们可能会从一开始就取消使用模板的许多好处，包括业务逻辑与界面的分离。

HTML::Template 是一个轻量级的替代方案。它旨在为创建网页提供最基本的功能，仅此而已。哲学是将 Perl 和 HTML 分开。

***一个简单的例子***

下面是一个简单模板的例子，`template1.tmpl`。注意，它主要由带有一个非标准标签`tmpl_var`的标准 HTML 组成，其中属性名设置为 day。

```
<html> 

<head> 

  <title>Template 1</title> 

</head> 

<body> 

Today is <tmpl_var name=day> 

</body> 

</html>
```

使用 Perl 中的`template`模板非常简单:

```
1\.  #!c:/perl/bin/perl -T 

2\.  use CGI qw(:all); 

3\.  use HTML::Template; 

4\.  use POSIX; 

5\.  print header; 

6\.  my $template = HTML::Template->new(filename => 'template1.tmpl'); 

7\.  $template->param(day => strftime('%A', localtime()) ); 

8\.  print $template->output();
```

第 1 行简单地说明了 Perl 解释器的位置。开关意味着我们处于污染模式。这为 Web 应用程序提供了额外的安全级别，告诉 Perl 除非我们明确声明，否则我们不应该信任任何用户输入。这是一个很好的实践:您应该总是理所当然地包括这个开关。

第 2 行导入 CGI.pm 模块。这并不是 100%必要的:我们可以手工写出自己的 CGI 函数，但是这有引入各种错误和安全问题的风险。CGI 模块经过了很好的测试，在编写 CGI 应用程序时，您应该总是使用它。

在第 3 行，我们导入了 HTML::Template 模块。第 4 行引入了标准/POSIX 功能。这使得我们可以很容易地确定一周中的某一天。请记住 Perl 的哲学，即有不止一种方法可以做到这一点(TMTOWTDI)——我们可以使用任意数量的日期模块来做到这一点。

第 5 行是`print "Content-Type: text/htmlnn"`的 CGI 简写。在输出网页的其余部分之前，我们需要返回内容类型。在第 6 行，我们创建了模板对象。模板的文件名作为唯一的参数提供。

第 7 行将名为`"day"`的`<tmpl_var>`标记设置为星期几。如果您不熟悉 POSIX `strftime`函数，`%A`是返回星期几的简写。PHP 和 c 都可以使用相同的格式掩码。

最后，在第 8 行，我们显示了网页。

现在我们已经了解了基本的 HTML::Template 理念:我们主要创建 HTML 代码，并为动态内容添加一些`<tmpl>`占位符。语法刻意保持简单；你的网页设计者可以简单地通过标准 HTML 和少量附加标签来维护网站的外观。

***循环***

虽然`<tmpl_var>`标签非常有用，但它本身是不够的。通常，我们需要以表格的形式呈现一组数据。这就是`<tmpl_loop>`标签的用武之地:

```
<html>  

<head>  

<title>Template 2</title>  

</head>  

<body>  

<table>  

  <tr>  

    <th>Language</th>  

    <th>Description</th>  

  </tr>  

  <tmpl_loop name="language">  

  <tr>  

    <td><tmpl_var name="language_name"></td>  

    <td><tmpl_var name="description"></td>  

  </tr>  

  </tmpl_loop>  

</table>  

</body>  

</html>
```

本质上，我们有一个叫做`language`的循环，里面有两个变量，叫做`language_name`和`description`。在 Perl 方面，HTML::Template 希望将循环作为散列(关联数组)引用的数组引用来传递:

```
1\.  #!c:/perl/bin/perl -T  

2\.  use CGI qw(:all);  

3\.  use HTML::Template;  

4\.  my @languages = (  

5\.      {  

6\.          language_name => 'Perl',  

7\.          description   => 'Practical Extraction and Report Language'  

8\.      },  

9\.      {  

10\.          language_name => 'PHP',  

11\.          description   => 'Hypertext Preprocessor'  

12\.      },  

13\.      {  

14\.          language_name => 'ASP',  

15\.          description   => 'Active Server Pages'  

16\.      },  

17\.  );  

18\.  print header;  

19\.  my $template = HTML::Template->new( filename => 'template2.tmpl' );  

20\.  $template->param( language => @languages );  

21\.  print $template->output();
```

第 4-17 行定义了散列引用的数组。散列关键字名称必须与`<tmpl_loop>`中的模板`<tmpl_var>`名称相同。在第 20 行，对语言数组的引用被传递给模板标签`<tmpl_loop name=language>`。

在我们的 Perl 脚本中手工设置循环数据非常简单，但是在现实世界中，我们很可能从数据库中检索模板数据。假设我们有一个名为`"languages"`的表，有一个名为`"language_name"`的列和一个名为`"description."`的列，我们可以很容易地从查询中生成输出:

```
1\.  #!c:/perl/bin/perl -T  

2\.  use CGI qw(:all);  

3\.  use HTML::Template;  

4\.  use DBI;  

5\.  my $dbh = DBI->connect('dbi:<driver>:<my db>', '<user>', '<password>');  

6\.  my $sql = "select * from languages";  

7\.  my $sth = $dbh->prepare($sql);  

8\.  $sth->execute();  

9\.  my $languages = $sth->fetchall_arrayref({  

10\.     language_name => 1,   

11\.     description   => 1}  

12\.  );  

13\.  print header;  

14\.  my $template = HTML::Template->new( filename => 'template2.tmpl' );  

15\.  $template->param( language => $languages );  

16\.  print $template->output();
```

在第 4 行，我们引入了 DBI，Perl 的数据库模块。第 5 行连接到 MySQL 数据库。将`<driver>`、`<my db>`、`<user>`和`<password>`替换为适合您系统的选项。这不是生产代码——我们通常不会对这些信息进行硬编码，我们通常会检查是否有任何数据库交互失败。

第 6-8 行准备我们的 SQL select 语句并执行它。在第 9-12 行，我们获取整个结果集。DBI 为数据提供了许多不同的格式。数组引用对我们来说是最好的，因为这是 HTML::Template 期望的格式。我们可以在散列引用中指定我们希望在数组中命名的列。

***条件句***

最后，我们可能希望根据某些条件改变输出。HTML::Template 提供了一个`IF`和一个`UNLESS`标签。例如，假设我们希望在找不到语言的情况下显示一条特殊消息:

```
<html>  

<head>  

<title>Template 3</title>  

</head>  

<body>  

<table>  

    <tmpl_if name=language>  

  <tr>  

    <th>Language</th>  

    <th>Description</th>  

  </tr>  

  <tmpl_loop name="language">  

  <tr>  

    <td><tmpl_var name="language_name"></td>  

    <td><tmpl_var name="description"></td>  

  </tr>  

  </tmpl_loop>  

    <tmpl_else>  

  Sorry, no languages were found  

    </tmpl_if>  

</table>  

</body>  

</html>
```

我们在这里所做的就是用一个条件。如果语言参数为真，则显示结果；否则，我们会让查看页面的人知道没有找到记录。

```
UNLESS is similar to IF, except that instead of checking if a condition is true, we're seeing if it isn't (i.e. "unless true, do this"). ***每页多个模板*** 

除非你的页面很简单，否则你很可能想要添加页眉、页脚，甚至是某种形式的导航。您可以很容易地包含其他模板:

```
<tmpl_include name="menu.tmpl">
```

就这样吗？
我们已经在这里介绍了 HTML::Template 的基础知识。它的作者 Sam Tregar 非常小心，没有加入任何可能破坏保持业务逻辑与用户界面分离的哲学的附加功能。尽管模板语法有限，但我从未要求任何更复杂的东西。然而，与 Sam 更好的判断相反，他创建了一个名为 HTML::Template::Expr 的扩展，它允许开发人员添加表达式和函数支持。
最后，对于 PHP 和 Python 程序员来说，HTML::Template 已经移植到了您选择的语言中。更多信息可在[http://htmltmpl.sourceforge.net/](http://htmltmpl.sourceforge.net/)找到。

```

## 分享这篇文章