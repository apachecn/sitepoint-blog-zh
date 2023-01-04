# PHP 命令行——第 1 部分文章

> 原文：<https://www.sitepoint.com/php-command-line-1/>

我们都知道 PHP 是构建网络应用的绝佳语言，但是我们都*也*知道运营一个严肃的网站不仅仅是用户界面。

有备份要执行，日志文件要分析，数据要清理，以及所有那些在后台发生的基本但平凡的任务。通常，这种日常管理任务是自动化的主要目标，如果您曾经使用过基于 UNIX 的系统，您可能会遇到 shell 脚本和 cron 的概念。

可能会让你吃惊也可能不会让你吃惊的是 PHP 在 Web 上也做得很好。如果在阅读了[PEAR 入门](https://www.sitepoint.com/article/getting-started-with-pear)之后，您安装了 PEAR 包管理器，那么您已经体验了 PHP 在命令行上的功能。

在这个由两部分组成的系列中，我将详细介绍 PHP 的命令行界面(CLI)。这第一部分将集中于基本原理；命令行脚本的输入和输出。第二篇文章将进一步探讨 PEAR 为命令行提供了什么，以及 PHP 的一些不太明显的功能。

***今日命令行参数***

*   CLI 简介:它的全部内容
*   设置:检查您的 CLI
*   Hello World:您的第一个 CLI 脚本
*   流入、流出:处理输入和输出
*   多项选择:交互式用户输入
*   错误和管道:好习惯
*   应对争论:更多的投入
*   PEAR::Console_Getopt:管理参数
*   兼容性:让每个人都开心

此外，您可能希望[下载包含我们将在本教程中看到的所有示例的代码档案](https://www.sitepoint.com/examples/phpcli/phpcli1_code.zip)。

##### CLI 简介

PHP 的命令行界面最早出现在 PHP 4.2.0 中，作为实验功能。随着 PHP 4.3.0 的出现，CLI 成为了官方的 SAPI(服务器 API)。实际上，这意味着 PHP 的 CLI 版本现在作为一个单独的 PHP 二进制文件(可执行文件)出现，您可以使用它在命令行上运行脚本，并提供对世界其他地方的“访问点”，就像通过 Web 访问传入数据的`$_GET`和`$_POST`变量一样。

在我继续讲下去之前，我应该提到，正如您可能知道的，PHP 并不是编写命令行脚本的唯一选择。仅举两个例子，Perl 和 Python 都被广泛用于编写命令行应用程序，在许多情况下，它们是比 PHP 更好的选择。它们为常见问题提供了一套成熟的工具，并且通常具有更好的性能。

那么，为什么要用 PHP 呢？一个明显的——也是非常好的——原因可能仅仅是您比其他人更了解 PHP。不太明显的是，如果您正在用 PHP 开发 Web 应用程序，用另一种语言编写支持脚本会导致额外的麻烦——即使您对这两种语言都很有信心。既有不得不转换编程“思维定势”的人的方面，也有不得不支持两个平台的开销和可能错过的重用机会；例如，数据访问逻辑可能需要实现两次。

此外，如果您正在构建一个将由“未知”第三方使用的 PHP 应用程序，要求他们在 PHP 之外安装其他平台很可能会导致挫败感，特别是如果他们使用的是 Windows。尽管 PHP 可能不是编写命令行实用程序的最佳选择，但它也是一个不错的选择，而且，在构建工具来支持已经用 PHP 编写的应用程序时，坚持使用单一平台是有意义的。

什么类型的问题适合用命令行脚本来解决？显而易见的选择是与运行 Web 应用程序的服务器的管理相关的任何事情，例如备份、清除和归档旧数据，以及分析日志文件——也许在出现严重错误时发送电子邮件警报。这些都是适合使用 [cron](http://www.unixgeeks.org/security/newbie/unix/cron-1.html) 自动化的任务，脚本的“预期用户”是另一个脚本或应用程序。

然而，这并不一定就此止步。您可以考虑为您的 Web 应用程序编写一个命令行“installer ”,负责将 PHP 脚本复制到正确的位置，设置正确的文件系统权限，加载数据库模式，对于 intrepid 来说，还负责管理应用程序的升级。当通过 Apache 在 Web 上执行 PHP 脚本时，您通常无权完成诸如更改文件系统权限之类的任务——此外，您也不一定想公开能够进行彻底更改的 Web 代码。但是，使用普通用户帐户从命令行运行，脚本可以保持脱机，同时为您提供复制文件、更改权限等必要的权限。

命令行脚本的优点是它们通常更容易开发，更重要的是，比基于 Web 的桌面 GUI 使用起来更快。因此，它们也可以在你的开发过程中发挥重要作用，帮助你快速轻松地执行“构建”任务，比如用[PHP document](http://www.phpdoc.org)生成 API 文档，或者运行你的[简单测试](http://www.lastcraft.com/simple_test.php)套件。 [Phing](http://phing.info/wiki/index.php) (一个基于 Apache 的 ANT 的构建工具)和 [rephlux](http://sourceforge.net/projects/rephlux) (一个基于 [CruiseControl](http://cruisecontrol.sourceforge.net/) 的持续集成工具)提供框架来帮助你自动化你的开发过程。

##### 安装

我已经在【PEAR 入门，第 2 页中介绍了设置 PHP 的基础知识，因此您可以从命令行运行它。同样值得注意的是手册对从命令行使用 PHP 的[是怎么说的。](http://www.php.net/manual/en/features.commandline.php)

当编写您自己的命令行脚本时(与使用 PEAR 包管理器相反)，您需要意识到您正在使用哪个 PHP 二进制文件(例如，您的主机可能已经为您设置了 CGI 二进制文件)。

测试这一点的快速方法是从命令行键入以下内容。

`$ php -v`

这应该会产生类似下面的消息:

`PHP 4.3.6 (cli) (built: Apr 19 2004 10:02:14)
Copyright (c) 1997-2004 The PHP Group
Zend Engine v1.3.0, Copyright (c) 1998-2004 Zend Technologies`

注意上面的`(cli)`，如果你看到的是`(cgi)`，不要担心。当使用 CGI 二进制文件时，可以告诉它像 CLI 二进制文件一样工作，但是您需要知道这是有区别的。在本文的最后，我将讨论如何解决这两个版本以及旧版本 PHP 之间的兼容性问题。

##### 你好世界

在其最基本的形式中，CLI 界面可以像任何 PHP 脚本一样使用。这里有一个例子:

`<?php
print "Hello World!";
?>`

文件名:helloworld.php

如果您在基于 UNIX 的系统上运行，首先需要使用以下命令使该脚本可执行:

`chmod +x helloworld.php`

您可以通过键入以下命令来执行该脚本:

`php helloworld.php`

通过在脚本的开头放置一个 [SheBang](http://c2.com/cgi/wiki?SheBang) ，可以避免在基于 UNIX 的系统上首先调用 php 二进制文件。这将 shell“指向”应该用于执行脚本的二进制文件。这里有一个例子:

`#!/usr/local/bin/php
<?php
print "Hello World!";
?>`

注意 PHP 可执行文件的路径可能不同，`/usr/bin/php`是另一个常见的位置。

我现在可以用以下命令执行这个脚本:

`$ helloworld.php`

在基于 UNIX 的系统上，您也可以考虑删除。php 文件扩展名，因为它不是执行脚本所必需的，而且这样做有一些好处；如果以后您选择用用另一种语言编写的脚本来替换它，同时保持功能不变，就不会出现混淆。

在 Windows 上，您可以通过将 PHP 文件扩展名与 PHP 二进制文件相关联来实现几乎相同的目的。你会在用 PHP 脚本替换 Perl 脚本的最后找到解释。

在这里，我将坚持使用. php 扩展，并且我将假设您将通过首先调用 PHP 二进制文件来执行脚本，因为这是最有效的跨平台方法。

##### 流入，流出

当显示命令行脚本的输出时，使用普通的 [echo](http://www.php.net/echo) 或 [print](http://www.php.net/print) 命令并不是最好的方法，原因有二。

首先，在 Windows 上，看看在命令提示符下执行以下命令时会发生什么:

`<?php
$i = 0;
while ( $i < 10 ) {
print $i."n";
sleep(1);
$i++;
}
?>`

文件名:printproblem.php

这些数字不是在打印时显示出来的，它们之间有一秒钟的间隔，而是在脚本终止时全部被刷新出来(注意，在 Linux 上运行相同的脚本会导致数字在发生时显示出来)。

另一个原因更多的是一个良好实践的问题。PHP(实际上是底层的 shell)允许您将面向用户的正常输出定向到所谓的“标准输出”，同时将任何错误消息转移到“标准错误”。进行这种划分的优势在于，它允许您将错误与脚本正常、平稳运行所生成的消息分开记录，这在运行批处理作业时非常有用。

PHP CLI 界面的一部分是三个“流”，您可以用或多或少与从 [fopen()](http://www.php.net/fopen) 返回的文件资源相同的方式与之交互。流用字符串标识:

`php://stdin (read)
php://stdout (write)
php://stderr (write)`

对于 PHP 4.3.0+ CLI 二进制文件，这三个流是自动可用的，分别用常量 STDIN、STDOUT 和 STDERR 标识。下面是我如何使用 STDOUT 来修复上面的脚本，以便它在 Windows 上正确运行:

`<?php
$i = 0;
while ( $i < 10 ) {
// Write some output
fwrite(STDOUT, $i."n");
sleep(1);
$i++;
}
?>`

文件名:streamout.php

现在计数应该在调用 [fwrite()](http://www.php.net/fwrite) 时显示。

请注意，在 Windows 上，PHP 会为您处理换行符，以便它们能够正确显示(您没有试图使用`<br>`，是吗？).

为了能够读取来自脚本用户的输入，您可以使用 STDIN 结合 [fgets()](http://www.php.net/fgets) 、 [fread()](http://www.php.net/fread) 、 [fscanf()](http://www.php.net/fscanf) 或 [fgetc()](http://www.php.net/fgetc) 。例如:

`<?php
fwrite(STDOUT, "Please enter your namen");`

//读取输入
$ name = fgets(STDIN)；

fwrite(STDOUT, “Hello $name”);

//正确退出
退出(0)；
？>

Filename: nameplease.php

脚本执行时会发生什么？嗯，当解释器到达`fgets()`命令时(它看到从 STDIN 读取的代码)，执行暂停并等待用户按 enter 键。当按下 return 键时，`fgets()`接收用户从执行暂停点开始输入的所有内容，直到他或她按下 return 键。这包括返回换行符——看看如果在将变量`$name`传递给 [nl2br()](http://www.php.net/nl2br) 后显示它的内容会发生什么。

这种“暂停/继续”行为实际上是由终端(命令提示符)控制的。它逐行工作，被称为[规范模式输入处理](http://www.opengroup.org/onlinepubs/007908799/xbd/termios.html#tag_008_001_006)。

请注意，仅在 UNIX 上，可以使用 [pcntl_signal()](http://www.php.net/pcntl_signal) 让您的脚本以[非规范模式](http://www.opengroup.org/onlinepubs/007908799/xbd/termios.html#tag_008_001_007)处理输入，这允许您直接响应特定的按键序列。

还要注意，我已经开始在脚本末尾使用 [exit()](http://www.php.net/exit) 命令，并向它传递一个整数值。这又是一个“礼貌”的问题。基于 UNIX 的系统上的常见做法是，当执行停止时，脚本返回一个状态代码，零是表示“一切正常”的标准。其他代码(1 到 254 之间的整数)用于识别导致脚本提前终止的问题(由您自己来定义脚本的错误代码，这通常是通过在开始时定义一组常量来实现的)。如果使用另一个命令行脚本(可能是用另一种语言编写的)来执行您的脚本，它将依赖从 exit()返回的状态代码来确定您的脚本是否成功运行。关于这个主题的更多想法，请参见关于[退出和退出状态](http://www.faqs.org/docs/abs/HTML/exit-status.html)的[高级 Bash 脚本指南](http://www.faqs.org/docs/abs/HTML/)。

**Go to page:** [1](https://sitepoint.com/php-command-line-1) | [2](https://sitepoint.com/php-command-line-1-2/) | [3](https://sitepoint.com/php-command-line-1-3/)

## 分享这篇文章