# PHP5:即将来到你身边的网络服务器

> 原文：<https://www.sitepoint.com/coming-soon-webserver-near/>

如果你对互联网上最流行的服务器端编程语言 [PHP](http://www.php.net) 有很多了解，你无疑会意识到当前 PHP 第 4 版的继任者已经蓄势待发。

在过去的几年里，PHP5 一直是 PHP 讨论的边缘，经常出现在 PHP4 的对象模型(PHP 为面向对象编程提供的支持)的讨论中。

对于一些人来说，PHP5 是“圣杯”,它将提供一些他们一直努力摆脱的基本特性。对于其他人来说，PHP5 有点令人不安，因为它似乎威胁到现状，暗示它采用了类似 Java 的编程原则。

不管你对这种情况有什么看法(或者你甚至还没有意识到)，PHP5 的时代正在迅速到来。2003 年 6 月 29 日，Sterling Hughes 宣布了 PHP5 Beta 1，这是第一个旨在让整个 PHP 社区体验未来的版本。

我一直在旁观者和邮件列表中观察 PHP5 的发展，试图避免从 PHP4 的实际工作中分心，但随着 Beta 1 的发布，我的好奇心最终战胜了我。在这篇文章中，我将基于测试版向您深入介绍 PHP5 提供的重要特性。我们还将考虑它对 PHP 作为一项技术的未来的意义。

首先，我可以说(对于那些担心第 5 版会影响他们使用 PHP 的人来说)，*不要惊慌！核心 PHP 开发团队非常了解 PHP 的最大优势，包括快速原型、易用性和解决问题的务实方法。在开发版本 5 时，很明显这些原则仍然是团队的首要任务，尽管事实上驱动力是使 PHP 对专业软件开发人员更有吸引力。*

今天我们将讨论:

*   为什么选择 PHP5？
*   测试驱动:新的对象模型、新的数据库、异常处理、新的 XML 引擎等等…
*   兴风作浪:PHP 的未来

**重要提示:** PHP5 Beta 1 应该*而不是*在实时 Web 服务器上使用；仍然有许多问题需要解决。测试版旨在让开发人员感受一下所提供的东西，并帮助 PHP 团队寻找 bug。

##### 为什么选择 PHP5？

大家都爱 PHP 吧？也许每个使用它的人都知道，但是 PHP4 有很多地方吸引了习惯于 Java 等语言的开发人员的批评。大量的这些批评源于 PHP 的历史；PHP 最初是一种基本的过程编程语言。

***面向过程与面向对象***

程序性编程语言是这样一种语言，在这种语言中，开发人员编写一系列指令供计算机“成批”执行。过程语言的重点是函数，它比数据(变量)优先。过程化编程通常对于一次性的“抛弃式”问题解决非常有效，例如帮助备份数据库的命令行脚本，或者带有表单的简单网页。当您开始构建更大、更复杂的应用程序时，过程化编程的缺点就显而易见了。变量可以从所有区域获得，很容易被错误覆盖，应用程序的一般结构通常会导致例程被复制，当不可避免的变化发生时，这会导致*大*头痛。

一种被广泛接受的替代方法是面向对象的范例，它认为数据(或变量)具有第一优先权，应该小心保护，而函数用处理它们的输入和输出的例程“支持”数据。OOP 带来了一种不同的应用程序设计方法，因为在这种范式下，开发人员更关心什么进什么出，而不是一批过程的具体流程。OOP 已经证明是一种处理变化的成功方法，它帮助开发人员“抽象出”应用程序中可能变化的元素，将它们编译成易于维护的定义良好的单元。

***设计图案***

可以说，面向对象范例的最大成功是[设计模式](http://c2.com/cgi/wiki?DesignPatterns)的概念。试图用一小段话来公正地评价设计模式并不是我要尝试的事情(如果你想要一些意见，可以去[高级 PHP 论坛](http://www.sitepointforums.com/forumdisplay.php?forumid=147))，但是，一般来说，“设计已死”这个论点也可以应用于编程。设计模式代表了世界领先的软件架构师的浓缩经验，并使像你我这样的开发人员有可能从其他人的成功中学习，而不是浪费时间痛苦地从我们自己的错误中学习。

有人说 PHP4 对 OOP 的支持是后来才加入的。当然，与像 Java 这样的语言相比，PHP4 只实现了最基本的语言特性，使得面向对象编程成为可能。也许问题的一个指标是 PHP4 中引用的工作方式([这里有更多信息](http://www.phppatterns.com/index.php/article/articleview/23/1/2/))——本质上，它们的工作方式与 Java 之类的语言完全相反。毫无疑问，PHP4 参考文献让每个人都感到困惑，从开始学习 HTML 的初学者，到来自 Java、C++和其他背景的有经验的开发人员。

经验丰富的 PHP4 开发人员*已经*证明了他们能够在设计中实现高度的抽象，从而产生强大而灵活的应用程序，如 [eZ publish](http://ez.no/developer) 和 [Krysalis](http://www.interakt.ro/products/Krysalis/) ，但是达到这种复杂程度需要的训练程度是巨大的。此外，就应用设计模式而言，PHP4 通常需要丑陋的、非标准的变通方法(例如[单例模式](http://www.phppatterns.com/index.php/article/articleview/6/1/1/))。

这些因素的结合影响了 PHP4 的整体“开发体验”。对于一个大型类库来说，问题被放大了，这使得开发真正坚固的、内聚的、经得起时间考验的类库变得困难。

***回到现实***

理论上这一切都很好。很快我们可能都在谈论我们的内容管理系统的投资回报率(我们在一个晚上搞砸了)，留起了胡子，养成了严肃的举止。但是让我换一种方式来说…

也许突出当今 PHP 应用程序构建方式缺陷的第一个问题是“我如何将我的站点的其余部分与 phpBB/vBulletin/Invision Board/***在此插入 PHP 论坛* * *？”一般来说，答案是“黑！”

因为开发 PHP 应用程序的过程化方法是 PHP4 最流行的方法，所以重用率很低。试图将现有的 PHP 应用程序与您自己的应用程序集成通常意味着要花费数小时来研究和重写第三方代码。

因此，今天的大多数 PHP 项目或多或少都是从零开始，实现认证系统、表单处理逻辑、模板机制等等——这些东西以前已经被成千上万的其他开发人员做过无数次了。这里的部分问题是，PHP4 的对象模型在帮助标准应用程序组件的发展方面只取得了部分成功。像 [PEAR](http://pear.php.net) 和 [PHP 类](http://www.phpclasses.org)这样的库突出了 PHP4 对象模型的成功和失败。

底线是 PHP 开发人员浪费了很多时间，因为他们不必要地重新发明轮子。PHP5 很可能是你正在寻找的护发素…

***错误处理***

当我公开 PHP4 的不可告人的秘密时，另一个弱点是处理错误的方式。

当出现问题时(这是不可避免的),代码的反应方式非常重要。例如，假设您的 MySQL 服务器暂时“停机”。你的站点的访问者是否丢失了他们将要在你的论坛上发表的广泛的、详细的咆哮？

今天的 PHP 开发人员已经采用了许多不同的错误处理方法。有些已经开发了相对复杂的错误处理机制(例如 PEAR 错误)。其他人干脆完全忽略错误处理，除了偶尔有人犯了可怕的错误时 die()。

现在的问题是*而不是*你根本不能处理错误，而是没有标准的、语言级别的错误处理解决方案。这意味着当您使用第三方代码时，您经常会遇到统一不同错误处理机制的问题。此外，您自己的错误处理方法可能会有所不同，因此您六个月前编写的代码可能不适合您今天编写的代码。

好消息是 PHP5 有了答案！

***XML 支持***

鉴于呈现 XML 的某个子集(例如 HTML)是 PHP 的真正目的，它对 XML 的不同支持留下了明确的改进空间。

今天，PHP 使用三组第三方库来实现 XML:

*   詹姆斯·克拉克的 [Expat](http://www.jclark.com/xml/expat.html) SAX 解析器
*   Gnome 项目的 [libxml](http://xmlsoft.org/) 库提供一个 DOM 解析器
*   Ginger Alliance 的 XSLT 库

在这三者中，只有 [Sax 扩展](http://www.php.net/xml)是 PHP 开发人员可以 100%依赖的。 [XSLT 扩展](http://www.php.net/xslt)相当可靠，尽管偶尔会出现与平台相关的错误，并且该扩展没有得到 Web 主机的广泛支持。PHP 的 [DOM XML](http://www.php.net/domxml) 扩展一直是最薄弱的一环，这是众多 bug 和 API 变化的结果，至今仍处于试验阶段。

使用三个独立的第三方库的问题是 PHP 团队需要维护。Expat 是稳定的，基本上没有变化，但功能集有限，而 libxml 和 Sablotron 仍在发展中。试图让使用它们的 PHP 扩展保持并发是一件令人头疼的事情。在 PHP5 中，已经为 PHP 的 XML 支持奠定了基础，我们很快就会看到这一点。

一吐为快，是时候搭上 PHP5 的顺风车了…

##### 试车

正如前面的讨论所表明的，PHP5 提供的新特性可以称之为“高级”,就我们使用 PHP 作为语言的方式而言。如果你还不习惯 PHP4 的对象模型，或者你没有在另一种语言中使用 OOP 的经验，如果你不理解下面的例子，请原谅我。但是如果你一直在拖延学习 PHP 中的 OOP，现在将是一个非常好的开始时间，因为版本 4 的语法非常简单。

帮助就在手边，以精选的[高级 PHP 资源](http://www.sitepointforums.com/showthread.php?threadid=78687)的形式，以及凯文·扬克的文章[面向对象的 PHP:分页结果集](https://www.sitepoint.com/blog/)，这是关于 PHP4 的类语法的很好的入门。对于更深入的面向对象编程入门，我推荐布鲁斯·埃凯尔的《Java 中的 T4 思维》(电子版免费提供)。如果你想知道为什么我向 PHP 程序员推荐一本关于 Java 的书，“用 Java 思考”提供了一个非常坚实的面向对象范例的所有基本方面的总结，并且说明它们的代码简单明了。从面向对象的角度来看，Java 的语法与 PHP4 非常接近，对于掌握 PHP 语法的人来说，可以理解 Java 在做什么。

也就是说，如果 OOP 真的让您感到厌烦，PHP5 中还有一些其他的新特性可能会让您感兴趣，尤其是 SQLite、异常处理和 SimpleXML 领域，我稍后会介绍所有这些特性。

说到安装 PHP5，我打算把它留给[高级 PHP 论坛](http://www.sitepointforums.com/forumdisplay.php?forumid=147)来帮你解决。在这里，你会找到在 Linux、Mac OSX 和 Windows 下成功安装它的人。我设法在 Suse 8.1 Linux 和 Windows 2000 下成功安装，这个过程相对来说没有痛苦，本质上与 PHP4 相同(更多信息请参见[安装手册](http://www.php.net/manual/en/installation.php))。我确实遇到了一些 Windows XP 的问题，Apache 在运行任何 PHP 脚本时都会崩溃，尽管这个问题可能是我的系统特有的。

给 Windows 用户的一个提示(在“快速和肮脏”的标题下)是将 iconv.dll 从 php 5/dll 复制到 php5/sapi 目录(处理 PHP 找不到 php4apache.dll 的事实)。

关于 Apache 配置，首先，确保您使用的是 Apache 的 1.3.x 版本(因为 PHP4 和 5 还没有在 Apache 2.x 中声明是稳定的)。在 httpd.conf 中，对于 PHP4，通常会有如下内容:

```
 LoadModule php4_module libexec/libphp4.so" # Unix   

#LoadModule php4_module "c:/php4/sapi/php4apache.dll" # Windows 
```

您现在有:

```
 LoadModule php5_module libexec/libphp5.so" # Unix   

#LoadModule php5_module "c:/php4/sapi/php4apache.dll" # Windows 
```

请注意，在 Windows 上，您仍然在 PHP5 中使用 php4apache.dll(不，这不是打字错误)。

请注意，当 PHP5 达到完整版本时，我将在这里展示的示例代码可能无效。完整的代码可以在本文末尾的 ZIP 文件中找到。

##### SQLite

首先，第一个大消息是 PHP5 自带了一个名为 SQLite 的嵌入式数据库引擎，一旦安装了 PHP，您就可以开始使用它。

惊呆了？

SQLite 是一个用 C 语言编写的轻量级公共域数据库引擎，由 Hipp，Wyrick & Company，in C .的 D. Richard Hipp 开发，可以从 http://www.hwaci.com/sw/sqlite/免费获得。

对于开发团队决定将其嵌入 PHP 核心的一些理由，请尝试[这篇文章](http://news.php.net/article.php?group=php.internals&article=2715)。

SQLite 不是用来替代现有的数据库服务器，比如 MySQL 和 Postgresql。

如果你认为 MySQL 在它所提供的方面是初级的(例如，缺少触发器、存储过程等等)。)，SQLite 设法让它看起来功能丰富。SQLite 所追求的是速度和在文件和内存中存储数据的能力。基准测试表明，对于常见操作，它的速度大约是 MySQL 的两倍，MySQL 当然也不慢。

根据应用程序的设计，我可能会考虑使用 SQlite 的一些任务可能是应用程序配置信息、存储会话、存储页面请求之间的序列化对象以及*可能还有*来存储用户登录信息。我是否会将它作为我的主要数据存储还有待观察——我还没有看到 SQLite 如何在一个流量很大的实时网站上屹立不倒。来自 [PHP Everywhere](http://php.weblogs.com) 的 John Lim 对 SQLite 有一些有趣的[想法。](http://php.weblogs.com/discuss/msgReader$2727)

下面是如何在 PHP5 中使用 SQLite。请注意，这不是一个广泛的教程，而是旨在给你一个如何工作的粗略想法。请阅读 SQLite [FAQ](http://www.hwaci.com/sw/sqlite/faq.html) 和 [SQL](http://www.hwaci.com/sw/sqlite/lang.html) 文档以获取更多信息。现在还没有官方的 PHP 文档(除了[这个展示](http://talks.php.net/show/php5-intro-oscon-2003/33)，但是我发现实现(就 PHP 函数而言)符合 [SQLite C 接口](http://www.hwaci.com/sw/sqlite/c_interface.html)。

```
<?php   

// Create SQLite Database and Table   

// Display some version information   

echo ( 'SQLite Version: '.sqlite_libversion().'<br />');   

echo ( 'SQLite Encoding: '.sqlite_libencoding().'<br />');   

// The file path to the database   

$sqliteDb='/www/sitepoint/php5/sitepoint.sqlite';   

// Connect to the database (creates the file)   

if ( !$db = sqlite_open($sqliteDb, 0666, $err) )   

    die($err);   

// A query to create a table   

$sql = "CREATE TABLE   

            users   

                (   

                id INTEGER PRIMARY KEY,   

                login STRING UNIQUE,   

                password STRING,   

                email STRING   

                )";   

// Run the query   

if ( !sqlite_query($sql, $db) )   

    // Die if errors happen, displaying the error message   

    die(sqlite_last_error($db).': '.   

        sqlite_error_string(sqlite_last_error($db)));   

echo ( "Database $sqliteDb created successfully" );   

// Close the connection   

sqlite_close($db);   

?> 

Script: sqlite_create.php
```

上面的示例在数据库文件“/www/site point/PHP 5/site point . SQLite”中创建了一个表“users”(如果该文件不存在，则自动创建)。

现在，将一些数据放入表中；

```
 <?php    

// Insert some data into the table    

// Connect to the database    

$sqliteDb='/www/sitepoint/php5/sitepoint.sqlite';    

if ( !$db = sqlite_open($sqliteDb, 0666, $err) )    

    die($err);    

// Some data to insert    

$users = array(    

    array(    

        'login'=>'jbloggs',    

        'password'=>md5('secret'),    

        'email'=>'jbloggs@yahoo.com'    

        ),    

    array(    

        'login'=>'jsmith',    

        'password'=>md5('secret'),    

        'email'=>'jsmith@php.net'    

        ) );    

foreach ( $users as $user ) {    

    // An INSERT query    

    $sql = "INSERT INTO    

                users (login, password, email)    

            VALUES    

                (    

                    '".$user['login']."',    

                    '".$user['password']."',    

                    '".$user['email']."'    

                )";    

    // Perform the query    

    if ( !sqlite_query($sql, $db) )    

        // Die if errors happen, displaying the error message    

        die(sqlite_last_error($db).': '.    

            sqlite_error_string(sqlite_last_error($db)));    

}    

echo ( 'Values inserted successfully' );    

// Close connection    

sqlite_close($db);    

?> 

Script: sqlite_insert.php
```

因为“id”列是一个整数主键，SQLite 为我处理自动增量(像 MySQL 一样)。

最后，我从表中选择一些数据；

```
 <?php    

// Performs a select on the table    

// Connect    

$sqliteDb='/www/sitepoint/php5/sitepoint.sqlite';    

if ( !$db = sqlite_open($sqliteDb, 0666, $err) )    

    die($err);    

// Select the data    

$sql = "SELECT    

            *    

        FROM    

            users";    

// Get the result    

if ( !$result = sqlite_query($sql, $db) )    

    // Die if errors happen, displaying the error message    

    die(sqlite_last_error($db).': '.    

        sqlite_error_string(sqlite_last_error($db)));    

echo ( '<h2>User List</h2>' );    

// Fetch the results into an array, row by row    

while ($row = sqlite_fetch_array($result, SQLITE_ASSOC)) {    

    echo ( $row['id'].'. <b>Login:</b> '.$row['login'].    

           ' <b>Email:</b> '.$row['email'].'<br />' );    

}    

// Close connection    

sqlite_close($db);    

?> 

Script: sqlite_select.php
```

这很简单——和在 PHP 中使用 MySQL 差不多。

如前所述，SQLite 还可以在内存中存储数据，这可能为 PHP 的[共享内存函数](http://www.php.net/shmop)提供了一种替代方案，后者在 Windows 上一直运行不佳。它还有一个面向对象的 API 来替代我上面使用的 PHP 函数。更多信息参见[本次展示](http://talks.php.net/show/php5-intro-oscon-2003/33)。

##### PHP5 对象模型

首先，一个警告——从现在开始，事情会变得更加激烈。这里有很多内容要介绍，我假设你了解 OOP，并且对当今 PHP4 有一个大致的了解。如果你想了解更多的细节，请随时添加到本文末尾的讨论中。大多数情况下，我将把它留给代码来说明，所以您可能需要非常仔细地检查它。如果有任何不清楚的地方，欢迎在本文末尾的讨论中或者在[高级 PHP 论坛](http://www.sitepointforums.com/forumdisplay.php?forumid=147)中提出问题。

如果你曾经使用过 Java，PHP5 的对象模型对你来说会非常熟悉。这并不是说和 Java 一样；就开发人员友好性和 PHP 是一种动态类型语言这一事实而言，二者有很大的不同。

在这里，我将带你参观一些例子。网上已经有很多资源(见本文末尾)提供了更多的信息。

##### 私有、受保护和公共

解决 PHP4 的第一个问题，现在可以声明类中的成员变量和方法是类外部代码的“禁区”。使用 PHP4，您可以通过实施一些自律来度过难关，但是对于某些人(可能是那些缺乏必要自律的人！)这还不够。所以，有了 PHP5，你现在可以保护对象的内部不受外界影响，只通过你定义的 API(应用程序接口)提供访问。

***私有(和公有)？***

下面是一个私有关键字被用来保护类变量和类方法例子；

```
 <?php     

class Color {     

    // Available only from within the class     

    private $rgb = array();     

    // Note "public" and "var" are basically the same thing     

    public $colorname = '';     

    // This method is only available from within the class     

    private function setRGB ($rgb) {     

        $this->rgb=$rgb;     

    }     

    // "public" is optional     

    public function setColor($color) {     

        $colorMap = array (     

            'white' => array ('r'=>255,'g'=>255,'b'=>255),     

            'gray' => array ('r'=>190,'g'=>190,'b'=>190),     

            'yellow' => array ('r'=>255,'g'=>255,'b'=>0),     

            'black' => array ('r'=>0,'g'=>0,'b'=>0)     

            );     

        // Use private method     

        $this->setRGB($colorMap[$color]);     

        $this->colorname = $color;     

    }     

    // More public methods     

    function r() {     

        return $this->rgb['r'];     

    }     

    function g() {     

        return $this->rgb['g'];     

    }     

    function b() {     

        return $this->rgb['b'];     

    }     

    function name() {     

        return $this->colorname;     

    }     

}     

$color = new Color();     

# Private property - FATAL Error!     

// $color->rgb = array ('r'=>255,'g'=>255,'b'=>0);     

# Private method - FATAL Error!     

// $color->setRGB(array ('r'=>255,'g'=>255,'b'=>0));      

$color->setColor('yellow');     

echo ( 'Yellow is made from:<br />' );     

echo ( 'Red: '.$color->r().' ' );     

echo ( 'Green: '.$color->g().' ' );     

echo ( 'Blue: '.$color->b().' ' );     

?> 

Script: private.php
```

注意，我在上面的例子中也使用了关键字“public ”,只是为了说明这是可能的。实际上，“public”是可选的，因为任何没有声明为“private”或“protected”的方法或类变量都默认为“public”(允许向后兼容)。

任何被声明为“私有”的东西都只能从声明它的类内部访问。这意味着子类也被禁止访问。如果你创建了一个子类，你可以用相同的名字声明方法和变量，但是它们将是独立于那些在父类中声明的实体。

***被保护的***

这是一个受保护变量的例子。

```
 <?php     

class Color {     

    // Accessible only inside this class and subclasses of this class     

    protected $rgb = array();     

    var $colorname = '';     

    private function setRGB ($rgb) {     

        $this->rgb=$rgb;     

    }     

    function setColor($color) {     

        $colorMap = array (     

            'white' => array ('r'=>255,'g'=>255,'b'=>255),     

            'gray' => array ('r'=>190,'g'=>190,'b'=>190),     

            'yellow' => array ('r'=>255,'g'=>255,'b'=>0),     

            'black' => array ('r'=>0,'g'=>0,'b'=>0)     

            );     

        // Use private method     

        $this->setRGB($colorMap[$color]);     

        $this->colorname = $color;     

    }     

    function r() {     

        return $this->rgb['r'];     

    }     

    function g() {     

        return $this->rgb['g'];     

    }     

    function b() {     

        return $this->rgb['b'];     

    }     

    function name() {     

        return $this->colorname;     

    }     

}     

class RedFilter extends Color {     

    function setColor($color) {     

        parent::setColor($color);     

        $this->rgb['r'] = 0;     

    }     

}     

$color = new RedFilter;     

# Protected property - FATAL Error!     

// $color->rgb = array ('r'=>255,'g'=>255,'b'=>0);     

$color->setColor('yellow');     

echo ( 'Yellow filtered by Red is made from:<br />' );     

echo ( 'Red: '.$color->r().' ' );     

echo ( 'Green: '.$color->g().' ' );     

echo ( 'Blue: '.$color->b().' ' );     

?> 

Script: protected.php
```

受保护的变量(或方法)*是子类可用的*。

##### 构造函数和析构函数

PHP5 为类构造函数方法提供了 PHP4 的替代方法。您可以使用名为`__construct()`的方法作为构造函数，而不是给构造函数取与类本身相同的名称。这样做的好处是，如果你重命名类，你不需要重命名构造函数(或者你重命名的类的子类中的任何代码)。请注意，您仍然可以使用旧的构造函数方法(保留了向后兼容性)。

这里有一个例子:

```
 <?php      

class Foo {      

    var $variable;      

    /**      

     * Constructors now have the common __construct()      

     * function.      

     */      

    function __construct() {      

        $this->variable = 'Something';      

    }      

}      

echo 'Constructing Foo with the new __construct()<br />';      

$f = new Foo();      

echo ( $f->variable );      

?> 

Script: constructor.php
```

PHP5 还引入了析构函数方法`__destruct()`，一旦你使用完一个对象，你可以用它来确保这个对象被正确地“清理”(例如关闭与数据库的连接)。PHP 的垃圾收集机制会在从内存中移除对象时调用析构函数；或者，您可以直接从代码中调用析构函数。

比如:
`<?php      
class File      
{      
   var $resource;      
   function __construct($name) {      
       $this->resource = fopen($name,'r');      
   }      
   function read() {      
       if ( !feof($this->resource) )      
           return fgets($this->resource);      
       else      
           return false;      
   }      
   function __destruct() {      
       fclose($this->resource);      
   }      
}      

$file = new File('example.html');      

echo ('<pre>');      
while ( $contents = $file->read() ) {      
   echo ( htmlspecialchars($contents) );      
}      
echo ('</pre>');      
?>` 
剧本:destructor.php

上面的`__destruct()`方法确保当对象被 PHP 的垃圾收集器丢弃时，打开的文件被关闭。

注意，自己调用`__destruct()`并没有真正从内存中移除对象——它只是执行您用`__destruct()`方法定义的代码。在最初的 [Zend 2 引擎概述【PDF】](http://www.zend.com/engine2/ZendEngine-2.0.pdf)中，有一个从内存中删除对象的删除语句的说法；我在测试版中使用它的尝试导致了语法错误。

##### 摘要

PHP5 在引擎级别上形式化了抽象类和方法。抽象类(或方法)是不应该直接使用的；相反，它应该通过一个具体的子类来使用。拥有抽象类的意义在于允许一组子类共享父实现，同时警告其他开发人员不要直接使用父实现。例如，你可能有一个名为`HTML_Widget`的抽象类，它是由`HTML_Table`和`HTML_Form`这样的类扩展的；你希望人们只使用子类而不是父类本身。

一个常见的 PHP4 惯例是将`die()`语句放在应该是抽象的类和方法中，但是在 PHP5 中，我们现在有了一个标准的方法(这是另一件好事)。

这里有一个例子:

```
 <?php      

abstract class Senior {      

    protected $speech = 'Hello World!';      

    function speech() {      

        return $this->speech;      

    }      

}      

class Junior extends Senior {      

    protected $speech = 'Goodbye World!';      

    abstract function think() {      

        sleep(5);      

    }      

}      

# Attempt to instantiate abstract class: FATAL Error!      

// $g = new Senior();      

$g = new Junior();      

# Attempt to instantiate abstract method: FATAL Error!      

// $g->think();      

echo ( $g->speech() );      

?> 

Script: abstract.php
```

如果你试图实例化抽象类高级或者初级类的`think()`方法，你会得到致命的错误。

##### 接口

接口是 PHP5 的一个新特性，对于使用 PHP4 的有纪律的开发人员来说是可能的，但只是作为一个松散的约定。

接口本质上是一个“契约”,任何实现接口的具体类都必须遵守它。拥有接口的好处是它们允许开发人员在类库中陈述他们的意图，从而帮助其他开发人员添加他们自己的代码来符合类库的标准。当需要通过内省来识别对象时，接口也是有用的。

自省是指代码在运行时检查数据并确定类型、可用方法(如果是对象)等的能力。PHP 已经提供了许多自省函数，比如 [is_a](http://www.php.net/is_a) 和 PHP5 的一个新关键字“instanceof”，它实际上做了与 is_a()相同的事情，但应该可以提高可读性。等到 PHP5 稳定的时候，自省可能会获得更多的特性。

接口类简单地声明了一组方法名，而这些方法没有主体。例如:

```
 <?php       

interface Iterator {       

    function fetch();       

    function howMany();       

}       

class Presidents implements Iterator {       

    private $presidents = array();       

    function __construct() {       

        $this->presidents = array (       

            'Jimmy Carter','Ronald Reagan','George Bush',       

            'Bill Clinton','George W. Bush'       

        );       

    }       

    function fetch() {       

        $president = each($this->presidents);       

        if ( $president ) {       

            return $president['value'];       

        } else {       

            reset ( $this->presidents );       

            return false;       

        }       

    }       

    # Commented out to produce error       

    /*       

    function howMany() {       

        return count($this->presidents);       

    }       

    */       

}       

#Fatal Error - Presidents does not implement the howMany() method in Iterator!       

$presidents = new Presidents();       

while ( $president = $presidents->fetch() ) {       

    echo ( $president.'<br />' );       

}       

?> 

Script: interface.php
```

在上面的例子中，迭代器接口定义了实现它的类必须提供的两个方法。实现类为方法提供逻辑。因为我在 Presidents 类中注释掉了`howMany()`方法，所以当我实例化它时，PHP 给出了一个致命错误，提醒我需要使`howMany()`方法再次可用。

注意:关于迭代器，请参阅本文后面的“那不是所有人…”。

当 PHP5 发展的时候，有一些关于多重继承(C++就是这样)和接口(Java 方式)的争论。多重继承(多个父类的扩展)可能更强大，但是它也给语言带来了更多的复杂性，并且(可以说)经常导致糟糕的设计。PHP 团队选择了 Java 方法，这可能不会让每个人都满意，但肯定是明智的。

我个人认为接口*是*更好的选择；PHP 需要更多的关注标准化而不是选择(它已经提供了很多)。与此同时，性能问题可能会在涉及接口时出现，因为 PHP 是解释而不是编译的。

关于接口和多重继承的更多深度，请尝试[这篇讨论](http://www.artima.com/forums/flat.jsp?forum=32&thread=2959)和它提到的[文章](http://www.artima.com/intv/abcs.html)。

##### 过载

通过(实验性的)[重载扩展](http://www.php.net/overload)，PHP4 中的重载对象成为可能，你可以在这里找到更详细的[解释。对于 PHP5，重载已经成为该语言的一个核心特性，在处理 Web 服务以及与其他平台(如 Java 和. NET)的集成时，重载无疑会变得非常重要。](http://www.phppatterns.com/index.php/article/articleview/28/1/2/)

在 PHP 上下文中，重载与 C++和 Java 等(静态类型)语言中的方法重载是不同的。PHP 提供了函数 [func_num_args()](http://www.php.net/func_num_args) ，如果你需要达到同样的效果，这个函数可以和类型内省结合使用，比如 [is_int()](http://www.php.net/is_int) 。但是实际上，由于 PHP 是动态类型的，所以很少需要 Java/C++风格的方法重载。

在 PHP 中，重载指的是调用类中没有明确声明的方法和对象属性的能力。PHP 允许使用三种“神奇”的方法`__get()`、`__set()`(这两种方法都适用于对象属性)和`__call()`(适用于对象方法)，在这三种方法中，您可以放置代码来处理“意外”调用。

下面是一个使用 PHP5 中的`__call()`方法来允许通过对象调用原生 PHP 函数的例子:

```
 <?php       

class MyString {       

    private $string;       

    function __construct($string) {       

        $this->string = $string;       

    }       

    function __call($method,$params) {       

        switch ($method) {       

            case'strlen':       

                return strlen($this->string);       

            break;       

            case'substr':       

                return substr($this->string,$params[0],$params[1]);       

            break;       

            default:       

                return false;       

            break;       

        }       

    }       

}       

$myString = new MyString('Hello World!');       

echo ( 'The string length is '.$myString->strlen().'<br />' );       

echo ( 'Substr: '.$myString->substr(3,6).'<br />' );       

?> 

Script: overload.php
```

原生 PHP 函数`substr()`和`strlen()`现在可以通过 MyString 类的一个实例调用，不需要显式地将它们声明为方法。

***一点装饰***

重载*可能*也用于实现装饰模式(装饰模式提供了子类化的替代方案)。例如:

```
 <?php        

class FileList {        

    var $listing;        

    function __construct($path) {        

    $this->listing = array();        

    $path = realpath($path);        

    $dir = Dir($path);        

    while (false !== ($entry = $dir->read())) {        

      if ( is_file($path.'/'.$entry) ) {        

        $file = array();        

        $file['name'] = $entry;        

        $file['size'] = filesize($path.'/'.$entry);        

        $file['ctime'] = filectime($path.'/'.$entry);        

        $file['atime'] = fileatime($path.'/'.$entry);        

        $this->listing[]=$file;        

      }        

    }        

    }        

  function fetch() {        

    $file = each ( $this->listing );        

    if ( $file ) {        

      return $file['value'];        

    } else {        

      reset ( $this->listing );        

      return false;        

    }        

  }        

  function getFileByName($name) {        

    foreach ( $this->listing as $file ) {        

      if ( $file['name'] == $name ) {        

        return $file;        

      }        

    }        

    return false;        

  }        

}        

class FileSortDecorator {        

  var $fileList;        

  function __construct($fileList,$sortBy='name') {        

    $this->fileList = $fileList;        

    $sort = array();        

    foreach ( $this->fileList->listing as $file ) {        

      $sort[]=$file[$sortBy];        

    }        

    array_multisort($sort,$this->fileList->listing);        

  }        

  function __call($method,$params) {        

    return call_user_func_array(        

            array($this->fileList,$method),$params);        

  }        

}        

$fileList = new FileList('./');        

$sortDecorator = new FileSortDecorator($fileList,'size');        

echo ('<b>fetch:</b>');        

while ( $file = $sortDecorator->fetch() ) {        

  echo ('<pre>');        

  print_r($file);        

  echo ('</pre>');        

}        

echo ('<b>getFileByName:</b><pre>');        

print_r($sortDecorator->getFileByName('overload.php'));        

echo ('</pre>');        

?> 

Script: overload_decorator.php
```

`FileSortDecorator`类`decorates``FileList`类，通过特定的数组键对文件列表进行排序。同时，它允许通过它调用`FileList`方法，使用`__call()`方法。

**警告:**重载非常强大，但在大多数情况下会构成“黑客攻击”。例如，如何对重载的类进行自省呢？《PHP 单元测试的优秀简单测试框架》的作者 Marcus Baker 提出了一些关于 PHP 过载的非常有效的担忧[这里](http://www.sitepointforums.com/showpost.php?postid=840152&postcount=52)。

除了与其他平台的集成和黑客攻击，重载为 PHP 开发人员提供了一些有趣的机会来应用[面向方面的范例](http://lambda.weblogs.com/discuss/msgReader$4816?mode=day)(正如 Markus 在他的帖子中提到的)，并实现类似 [Python 的元类](http://www.onlamp.com/pub/a/python/2003/04/17/metaclasses.html)的东西。

注意:PHP 不支持运算符重载(所以没有`$myObject++`)。

##### 静态、最终和恒定

PHP5 提供了三个更重要的关键字；静态、最终和恒定。

***静***

可以针对类变量或方法声明 static 关键字，以允许在外部访问它们，而无需实例化类本身。在 PHP4 中，使用`::`操作符对于类方法来说这已经是可能的了，但是重要的一步是它现在可以应用于类变量。

下面是一个静态类成员变量:

```
 <?php        

class MyStatic {        

    static $foo;        

    var $bar;        

}        

MyStatic::$foo = 'Red';        

# Fatal error: Access to undeclared static property: mystatic::$bar        

// MyStatic::$bar = 'Blue';        

echo ( MyStatic::$foo );        

?> 

Script: static_var.php
```

就类方法而言，static 关键字不太重要，因为无论如何都可以静态调用方法(否则 PHP5 会破坏与 PHP4 的向后兼容性):

```
 <?php        

class MyStatic {        

    static function Foo() {        

        return 'This is foo()';        

    }        

    function Bar() {        

        return 'This is bar()';        

    }        

}        

echo ( MyStatic::foo().'<br />' );        

echo ( MyStatic::bar().'<br />' );        

$obj = new MyStatic();        

# Fatal error - cannot call static method via object instance        

// echo ( $obj->foo().'<br />' );        

echo ( $obj->bar().'<br />' );        

?> 

Script: static_method.php
```

本质上，所有方法都可以在 PHP5 中静态调用，就像在 PHP4 中一样。但是，static 关键字会阻止通过对象实例调用用它声明的方法，如示例所示。

***一胎***

static 关键字的一个优点是它使单例模式的实现变得非常容易:

```
 <?php         

class Singleton {         

    /**         

    * The singleton instance is stored here         

    */         

    static private $instance = false;         

    private $text = 'Empty message';         

    /**         

    * Make the constructor private to prevent the class being         

    * instantiated directly         

    */         

    private function __construct() {}         

    /**         

    * Use this static method to get a singleton instance         

    */         

    static function instance() {         

        if(!Singleton::$instance) {         

            Singleton::$instance = new Singleton();         

        }         

        return Singleton::$instance;         

    }         

    function setText($text) {         

        $this->text = $text;         

    }         

    function getText() {         

        return $this->text;         

    }         

}         

class Hello {         

    function __construct() {         

        $single = Singleton::instance();         

        $single->setText('Hello World!');         

    }         

}         

class Goodbye {         

    function __construct() {         

        $single = Singleton::instance();         

        $single->setText('Goodbye World!');         

    }         

}         

$single = Singleton::instance();         

echo ( $single->getText().'<br />' );         

$hello = new Hello();         

echo ( $single->getText().'<br />' );         

$hello = new Goodbye();         

echo ( $single->getText().'<br />' );         

?> 

Script: static_singleton.php
```

当您运行这个脚本时，您会注意到，虽然我每次都通过相同的对象引用(`$single->getText()`)调用相同的方法，但是构造`Hello`和`Goodbye`类会修改输出。这是因为它们都在处理对象的同一个副本(单例)。当你有一个许多对象都需要使用的类(可能是一个数据库连接)但你不想有对象的多个实例(例如，你不想连接数据库 10 次)时，Singleton 是有价值的。有了 static 关键字，PHP 开发人员就可以标准化单例类，而不必求助于 PHP4 中所需的变通方法。

***决赛***

最后的 keywooo
j

## 分享这篇文章