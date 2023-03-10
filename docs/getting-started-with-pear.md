# PEAR 入门——PHP 唾手可得的果实

> 原文：<https://www.sitepoint.com/getting-started-with-pear/>

上一次你用 PHP 开发一个应用程序，你花了多少时间？

“管道？！?"你哭了。“我是在写代码，不是修下水道！”

我真正想说的是:在你的上一个 PHP 项目中，你花了多少时间来编写之前已经完成的代码？你开发了一个完整的用户认证系统吗？你花了多长时间建立菜单系统？用 PHP 发 HTML 邮件你纠结过吗？你知道——所有这些事情在 PHP 中已经被反复做了。

在这篇文章中，我将向你介绍 [PEAR](http://pear.php.net) ，简而言之，它可以帮助你避免成为一名水管工，而是专注于按时完成项目。今天的菜单上有:

*   为什么要摘梨？别让医生靠近！
*   梨和困惑的简史
*   安装 PEAR:启动并运行
*   包管理器:找到你的路
*   即时 RSS:用梨喂自己

##### 为什么要摘梨？

你的客户要求你建立一个网上商店。你从哪里开始？考虑到预算和时间限制，你花在重新发明轮子上的精力越少越好。

PHP 附带了大量内置功能，如 [htmlentities()](http://www.php.net/htmlentities) 和 [strip_tags()](http://www.php.net/strip_tags) ，它们极大地简化了 Web 应用程序的构建，但由于明显的原因，你不会发现像`shopping_cart()`这样的功能。

你花在编写“管道代码”上的时间越多，你的项目错过最后期限或超出预算的风险就越大。

回车:[梨](http://pear.php.net)。虽然脑海中浮现出水果的图像，但 PEAR 实际上代表“PHP 扩展和应用程序库”，并被吹捧为“可重用 PHP 组件的框架和分发系统”。换句话说，PEAR 提供了按时完成项目所需的管道。

后退一步；描述源代码的一种方式是根据它所应用的*域*来考虑它，正如这里的[所描述的](https://www.sitepoint.com/forums/showpost.php?p=847198&postcount=70)。

PEAR 主要面向“架构领域”:可以在许多应用程序中重用的代码，但是面向特定的架构(即 Web 应用程序)。PEAR 提供的大多数“包”解决了那些构建 Web 应用程序的人经常遇到的特定问题，而不管正在构建的是什么类型的站点。

例如， [PEAR::Validate](http://pear.php.net/package/Validate) 提供了一个通用验证例程库，帮助您验证从 URL(如果您想防止 [XSS 漏洞利用，这非常重要)](http://www.cgisecurity.net/articles/xss-faq.shtml)到国际银行账号(IBAN)的任何内容。您可以很容易地找到在各种应用程序中重用 PEAR::Validate 的理由，这样可以节省大量精力，避免替代方案(正则表达式)存在漏洞的可能性(因为没有时间进行仔细的测试)。

##### 简短的历史

如果你想观察“疯狂而多变的反应”，向一群 PHP 开发人员提及 PEAR。作为一个辩论的主题，它引发了各种各样的反应，很快就变得很明显，围绕梨有一些混乱。

PEAR 项目始于斯蒂格·巴肯(他的名字你可能在 [PHP 手册](http://www.php.net/manual)中见过)。传说 Stig 想为 PHP 创建一个类似 Perl 的 [CPAN](http://www.cpan.org) (一个可重用 Perl 解决方案的大规模存储库)的东西。

PEAR 的工作始于 1999 年 11 月，最开始的可能是 PEAR 使用最广泛的包， [PEAR::DB](http://pear.php.net/DB) 。在接下来的三年里，一个由贡献者组成的小社区围绕着 PEAR 成长起来，并在 2003 年 1 月逐渐形成了它的第一个公开发行版(1.0)。

事实上，“公开发布”是一个用词不当的词。与许多开源项目一样，PEAR 的开发总是“公开的”，通过 PHP CVS 服务器，而 [PEAR 网站](http://pear.php.net)于 2000 年 10 月首次出现，尽管“正在建设中”。这可能无意中过早设定了预期。谈论 PEAR 的“发布”可能也有点令人困惑；[举例来说，PEAR::DB](http://pear.php.net/) 作为一个稳定的发行版，在 2002 年 4 月就已经提前上市了(版本 1.2)。毕竟，PEAR 不是一个单独的实体，而是代码分发的基础设施*和*存储在存储库中的所有库(或包)。

最终的结果是，我们的开发人员知道 PEAR 发生了什么，早在 2001 年 5 月就开始谈论它([PEAR 简介](http://www.onlamp.com/pub/a/php/2001/05/24/pear.html))，而我们其余的人却有些困惑。

公开发行版真正代表的是 PEAR 安装程序和包管理器(基础设施)的第一个稳定版本，它使得从 PEAR 获取包变得非常容易，我们很快就会看到。这次发布也是邀请所有人尝试 PEAR。

PEAR 的另一个令人困惑的方面提出了这个问题:“如果 PEAR 真的是 PHP 代码的存储库，为什么它有一个安装程序？”从 [Hotscripts](http://www.hotscripts.com/PHP/Scripts_and_Programs/index.html) 中，每个人都知道，要安装一个 PHP 应用程序，你只需解压并运行，对吗？这是千真万确的。PHP 代码通常非常容易部署。但是，在使用第三方 php 代码时(因为 PHP 在 include_path 中找不到 someClass.php)，你有多经常遇到这样的错误消息:“打开' lib/someClass.php '进行包含失败”？PEAR 有一个标准的方法来提供现成的 PHP 组件，它使用一个固定的包含路径。这意味着，只要您的 PEAR 设置正确，例如，包含 [PEAR::Cache_Lite](http://pear.php.net/Cache_Lite) 总是可以通过以下方式完成:

```
require_once 'Cache/Lite.php';
```

当您将大量外部库混合到您的应用程序中时，该怎么办？所有不同的编码风格都能很好地混合吗？由于 PHP 升级，当你依赖的某个开源项目开始产生各种错误信息时，你会怎么做？车主还支持吗？更不用说所有其他 PHP“陷阱”，如 magic_quotes 和 register_globals。

有很多有经验的 PHP 开发人员为 PEAR 做贡献，在用户反馈的帮助下，bug 修复相对较快，像 magic_quotes 这样的问题很快就解决了，如果它们真的发生的话。还有人愿意收拾残局，所以如果 PEAR 的一个贡献者退出了，这个包通常会交给其他人。

还有迹象表明，尽管受到 Perl 的 CPAN 的启发，PEAR 团队的目标是一个更加集成的库——更像 Java 的库。例如， [PEAR::DB_DataObject](http://pear.php.net/DB_DataObject) (正如您之前在 SitePoint [上看到的，这里是](https://www.sitepoint.com/blog/))提供了一个数据库持久层，允许您像对待对象一样对待表，并且在大多数情况下，忘记 SQL。DB_DataObject *使用* PEAR::DB 对数据库进行真正的调用，无论您使用什么数据库引擎，都可以从它提供的功能中获益。另外，新的[PEAR Package Proposal System(PEPr)](ttp://pear.php.net/pepr/pepr-overview.php)首先提供了一个对进入 PEAR 的代码的级别筛选。不完全是 Java 社区进程，而是朝着正确方向迈出的一步。

PEAR 包是如何设计的这个问题引发了一场讨论，这场讨论分裂了许多 PHP 开发人员。四处搜索(也许在 SitePoint 论坛上搜索)你会找到对 PEAR 和里面的包的意见，从“太烂了！”到“太棒了！”—涵盖两者之间的一切。那么，为什么对 PEAR 有这些不同的反应呢？

梨，作为一个项目，对很多人来说是很多东西。对于面向对象应用程序设计的一般问题，伴随积极反应而来的是诸如“它没有做我所需要的”和“它很臃肿”的评论。在某种程度上，PHP 是一种独特的语言，因为没有典型的 PHP 程序员。它吸引了各种背景的开发人员，从 Perl 黑客到 Java 大师，从 Web 设计师到职业程序员。建造适合所有人的东西是不可能的。你*会*在梨子里找到宝石。更多的时候，尽管你选择的包并不“完美”，但对于你试图解决的问题来说已经足够好了。

还有社交方面。PEAR 开发者社区正在改变。PEAR 来自一个相对较小的开发团队，他们互相认识，能够很容易地协调，现在，PEAR 吸引了整个 PHP 社区更多的输入。虽然这是一件好事，但也会带来成长的烦恼。管理这些新输入需要付出巨大的努力，幸运的是，人们放弃了空闲时间来做这件事。在过去，有时对某人“我如何……”的问题反应不佳，或者对一个梨包(别人的辛苦工作)的非外交批评，都会导致怨恨。

最后，还有文档的问题，包括如何使用 PEAR，以及存储在其中的包的细节。简单地说，没有太多的文档。而且，如果有详细的说明，如果它们过时了也不要惊讶。

也就是说，在过去的七个月里，我参与了一点 PEAR，我看到了一个越来越好的项目。PEAR 的常客对新人非常开放，愿意听取建设性的反馈。他们甚至更愿意接受提供的帮助，无论是添加新的软件包、维护现有的软件包、帮助编写(急需的)文档，还是仅仅参与[邮件列表](http://pear.php.net/support.php)。

无论你对 PEAR 有什么先入之见，(或者即使你从未听说过它)，它都是一个非常有价值的项目。当前的代码库已经代表了数千小时工作的总和。更重要的是，PEAR 代表了一个发展 PHP 本身的机会。从基本框架(您可以从 php.net 下载)加上分散在 Sourceforge 等地方的许多其他不连贯的项目，PEAR 提供了一个统一的、得到良好支持的组件库的机会，可以被世界各地的 PHP 开发人员重用，大大减少了每个人的开发时间。

所以如果有你不喜欢的，不要袖手旁观。参与并改革它。毕竟，进化比革命更有效！

##### 安装 PEAR

好了，说够了。是采取行动的时候了！

设置 PEAR 并能够从中检索包是一个两阶段的过程。首先是 PEAR 安装程序，然后是 PEAR 包管理器。安装程序的工作纯粹是设置软件包管理器。包管理器是用来获取感兴趣的 PHP 库(PEAR 包)的工具。

让我们从 PEAR 安装程序开始。本质上，它只是一个 PHP 脚本，连接到 PEAR 网站并下载一大堆其他东西(特别是包管理器)。要执行安装程序，您需要能够从命令行运行 PHP。所以，让我们从这里开始…

***命令行 PHP***

如果你运行的是 Windows，打开一个 DOS shell (Windows NT / 2000 / XP:开始>运行> cmd.exe，Windows 95 / 98:开始>运行> command.exe——如果你有任何疑问，请参见 Kev 的[命令提示符备忘单](https://www.sitepoint.com/blog/))。

如果您在自己的 PC 上运行 Linux，我假设您知道如何启动 shell。如果您有一个 LAMP 主机(Linux / Apache / MySQL / PHP)，当您启动到服务器的 ssh 或 telnet 会话时(假设您的主机允许这样做)，您应该被带到命令提示符(通常是 Linux 的 BASH shell)。向 Mac 用户道歉(你知道，有一个重要的 Mac / PHP 用户群)，但是去年 12 月圣诞老人没有在我的袜子里留下一个。也许明年吧。

无论您使用什么操作系统，在命令提示符下键入:

```
php -v
```

作为响应，您应该会看到一些关于 PHP 的版本信息，比如:

```
PHP 4.3.4 (cli) (built: Nov 26 2003 10:46:59)  

Copyright (c) 1997-2003 The PHP Group  

Zend Engine v1.3.0, Copyright (c) 1998-2003 Zend Technologies
```

如果您看不到这一点，您首先需要找到 PHP 可执行文件并将其添加到您的系统路径变量中。

在基于 Windows 的系统上，这将位于 PHP 安装目录下的“cli”子目录中，例如:

```
c:php-4.3.2-Win32cliphp.exe
```

在基于 Linux 的系统上，PHP 通常位于:

```
/usr/local/bin/php
```

请注意，php 二进制文件没有“.”。exe”扩展名。如果有疑问，您可以在 Linux 上找到它，如下所示:

```
$ whereis php
```

如果失败，请尝试以下操作(在/usr 目录下搜索):

```
$ find /usr -name php
```

现在您知道了 php 可执行文件的位置，您需要更新您的 path 变量以使其可用。

在 Windows 上，转至开始>控制面板>系统>高级>环境变量。向下滚动“系统变量”列表，直到找到 path 变量，选择它并单击编辑。现在，在“变量值”的末尾，添加路径到 php 可执行文件，例如“；c:php-4.3.2-Win32cli”(注意分号，它将一个路径与下一个路径分开)。可执行文件现在位于您的路径中。如果您启动一个*新的*命令提示符(任何现有的命令提示符仍将使用旧的 path 变量)，您应该能够键入“php -v”并获得预期的响应。

如果您使用 LAMP 主机，他们应该已经为您设置好了，但如果没有，您需要编辑(或创建)文件”。profile ”,在该文件的末尾添加以下行:

```
export PATH=$PATH:/usr/local/bin
```

注销，下次连接到 shell 时，您应该能够直接执行 PHP 二进制文件(假设您的主机已经为您提供了足够的权限)。

关于从命令行使用 PHP 的更多一般提示和技巧，参见[用 PHP 脚本替换 Perl 脚本](http://www.phpbuilder.com/columns/jayesh20021111.php3)，以及关于[从命令行使用 PHP 的 PHP 手册](http://www.php.net/manual/en/features.commandline.php)。

***运行 PEAR 安装程序***

现在您已经在系统路径中获得了 PHP 可执行文件，是时候获取并运行 PEAR 安装程序了。现在，安装程序与 PHP 发行版捆绑在一起，所以您的系统上可能已经有了。安装脚本也可以从[http://pear.php.net/go-pear](http://pear.php.net/go-pear)获得，我将用它来告诉你如何从头开始安装 PEAR。

Windows 用户，打开浏览器进入[http://pear.php.net/go-pear](http://pear.php.net/go-pear)，然后简单地“将页面另存为…”。将其存储在系统中的某个位置，例如:

```
c:tempgo-pear.php
```

现在，键入:

```
cd c:temp  

php go-pear.php
```

安装程序现在启动。

Linux 用户的生活更加轻松，只需要输入:

```
$ wget http://pear.php.net/go-pear -O go-pear.php  

$ php go-pear.php
```

或者，如果您安装了 Lynx 文本模式浏览器，只需键入:

```
$ lynx -source http://pear.php.net/go-pear | php
```

…它将下载*并*执行安装程序。

***安装选项***

随着安装程序的运行，您需要回答几个关于如何设置 PEAR 包管理器的问题。大多数问题应该很简单(比如您是否使用代理服务器——安装程序通过 HTTP 与 PEAR 网站对话)。

在定义 PEAR 的文件布局时，需要进行一些思考。如果你在自己的 Linux 系统上安装*，通常最好接受默认设置(首先以 root 用户身份登录)。在 Windows 或 LAMP 主机上安装时，最好更换它们。这些是我的首选项，与安装程序提供的默认设置略有不同:*

*   **选项 1(别名`$prefix`):安装前缀**——这是一个你可以选择放置任何东西的基准位置。在 Windows 系统上，一个好的选择可能就是`c:pear`。在 LAMP 上，你最好使用类似于`/home/yourname/pear`的东西，其中“你的名字”是你登录的用户名(所以，你安装在你的主目录下)。
*   **选项 2:二进制文件位置**–当它说“二进制文件”时，它实际上意味着可执行的 PHP 脚本、shell 脚本等等。PEAR 包管理器的命令行版本放在这里(这是您感兴趣的地方！).这个目录需要转到您的系统路径变量中(使用与将 php 二进制文件添加到您的路径中相同的过程)。通常，对于 Windows 来说，`$prefixbin`可能是一个不错的选择(对于 LAMP 帐户来说，`$prefix/bin`)。
*   **选项 3(别名`$php_dir` ): PHP 代码目录**——这是放置所有 PEAR 包的目录。它将需要被添加到您的 PHP `include_path`(稍后会详细介绍)。你可以为窗户选择`$prefixlib`(`$prefix/lib`—灯)。
*   **选项 4:文档基目录**–这是放置 PEAR 包中提供的文档和示例的地方。您可能会参考这个目录来了解软件包是如何工作的。在您自己的开发系统上，您可以选择将这个目录更改为 Web 根目录下的一个目录(例如`c:apachehtdocspear`)，这样您就可以用浏览器快速查看示例。但是要小心！请确保您没有向外界公开该目录！更安全的选择可能是 Windows 的`$prefixdocs`或 Linux 托管账户的`$prefix/docs`。
*   **选项 5:数据库目录**——一些 PEAR 包使用以 XML 格式存储的数据，包提供的 PHP 脚本将以某种方式使用这些数据(例如， [PEAR::Science_Chemistry](http://pear.php.net/Science_Chemistry) 在数据目录中用[化学标记语言](http://www.xml-cml.org/)放置一个水的描述，尽管纯粹是为了测试)。一个好的选择可能是 Windows 的`$prefixdata`(或者 LAMP 主机帐户的`$prefix/data`)。
*   **选项 6:测试目录**——将随包提供的任何单元测试脚本，通常在 [PEAR::PHPUnit](http://pear.php.net/PHPUnit) 下执行，放在这里。同样，类似于窗口的`$prefixtests`(`$prefix/test`—灯)。

这是最难的部分。继续按照提示操作，安装程序将下载并设置 PEAR 包管理器，以及一些额外的常用包，如果您选择接受它们的话(推荐)。

一旦完成，安装程序将尝试修改 php.ini 文件，如果可能的话，将 PEAR 库目录(上面的选项 2)添加到 PHP `include_path`中。在您自己的系统上，如果有正确的权限，它可能会正确地进行这些修改。不过，仔细检查它做了什么是个好主意，所以打开 php.ini(通常在 c:Windows 中；它在 Linux 上有所不同)并查看修改是否正确。当然，您的主机提供的共享帐户可能不允许您修改 PHP . ini——马上就有解决方法了！

正如我前面提到的，PEAR 包使用相同的相对路径包含在您的 PHP 代码中。所以，拥有正确的`include_path`非常重要(手动修改 PEAR 包并不好玩！).

使用我上面建议的目录，您的`include_path`可能看起来像下面这样:

```
include_path = ".;c:pearlib" ;
```

…在 Windows 上。注意分号。

```
include_path = ".:/usr/local/lib/php" ;
```

…在您自己的 Linux 系统上。

在您无权修改 php.ini 的共享 LAMP 主机上，最好的方法是将一个`.htaccess`文件放在您的 Web 文档根目录中(假设您的主机已经将 Apache 配置为允许使用`.htaccess`文件)，其中包含:

```
php_value "include_path" ".:/home/yourname/pear/lib"
```

这应该递归地应用于子目录。如果您遇到内部服务器错误，就该骚扰您的主机了——他们需要为您的虚拟主机配置 Apache，使其具有:

```
AllowOverride Options
```

(“选项”或“全部”应该可以。)

如果你感到绝望，你也可以在一些已经包含在你的应用程序中的全局脚本中修改你的包含路径。例如:

```
<?php  

ini_set("include_path", ".:/home/yourname/pear/lib");
```

请注意，您使用 [ini_get()](http://www.php.net/ini_get) 获取现有的包含路径，这意味着您可以使用以下代码来保留现有的包含路径:

```
<?php  

$current_path = ini_get("include_path");  

// Append a new include path to the existing path  

ini_set("include_path", "$current_path:/home/yourname/pear/lib");
```

***更多环境变化***

最后，您需要对您的环境做进一步的修改。

Windows 用户应该找到一个文件调用`PEAR_ENV.reg`，由安装程序放置在 PEAR“binaries”目录中(上面的选项 2)。这包含允许您编辑您的注册表和创建一些指向您上面指定的各种目录的环境变量的指令，因此 PEAR 包管理器(和任何需要它的包)知道 PEAR 是如何设置的。

LAMP 主机上的用户应该键入:

```
$ ls -l ~/.pearrc
```

您应该看到一个名为`.pearrc`的文件，PEAR 将它放在您的主目录中。软件包管理器将使用它来查找您在运行安装程序时指定的目录。内容是一个序列化的 PHP 数组，但是如果你看一下，你应该能够仔细检查设置是否正确。

最后一步是将 PEAR“binaries”目录添加到系统路径变量中，这样就可以在目录结构中的任何地方执行它们。

按照上面将 php 二进制文件添加到路径中的步骤，Windows 用户应该添加以下内容(取决于 PEAR 的安装位置):

```
;c:pearbin
```

有 LAMP 账号的，必须编辑。再次剖析，添加:

```
export PATH=/home/yourname/pear/bin:$PATH
```

注意,`$PATH`变量位于 PEAR 二进制目录的之后的*。这只是一个预防措施，以防你的主机安装了自己版本的 PEAR 当您键入`pear`时，您的任何 PEAR 命令行脚本版本都应该首先执行。*

就是这样！现在，您可以继续尝试 PEAR 包管理器(我保证，从这里开始事情会变得容易得多)。

***其他选项***

这不是设置 PEAR 包管理器的唯一方法。特别是，LAMP 帐户主机可能已经安装了 PEAR(如果您在执行上述任何操作之前从命令行键入`pear`，您可能会看到软件包管理器帮助页面)。你可以选择“背驮式”安装你的主机，正如这里的所解释的。就我个人而言，我不喜欢这样做，因为我是一个控制狂(至少，当谈到托管，我是！)，但相信你的主人知道他们在用梨做什么可能会让你的生活更轻松。

此外，虽然我在这里只看命令行界面，但是 PEAR 包管理器还有另外两个“前端”:一个 Web 界面和一个 PHP-GTK GUI。我从来没有接触过 GTK 版本，但 Web 界面可能是有用的，尤其是如果你被困在一个不允许你访问命令行(*boo*，*hiss*)的主机上。有一些关于在 [PHP 厨房](http://www.phpkitchen.com/article.php?story=20021201062516970)设置 Web 前端的注意事项，这应该很容易适应一个共享的托管账户。确保你用用户名/密码组合来保护网页界面(一个`.htaccess`文件可以帮你做到这一点)！

##### 软件包管理器

好吧，你已经走了这么远了。从现在开始，事情变得简单多了。PEAR 包管理器现在应该已经设置好了，可以开始运行了。首先，从命令行输入:

```
$ pear
```

Up 应该弹出 PEAR 包管理器的基本用法——您可以使用的可能的子命令列表。安装 PEAR 时，您几乎可以更改所有指定的设置。输入以下内容:

```
$ pear config-show
```

在这里你可以看到当前的配置，从你安装时指定的目录到代理服务器配置等等。一般来说，你不需要担心这些，但是知道如何控制它们是值得的。如果您键入:

```
$ pear config-help <optioname>
```

…您将收到关于该配置选项含义的简短描述。您可以使用以下方式更改您的选项:

```
$ pear set <optioname>
```

还没印象深刻？所有这些都是为了什么？嗯，打这个怎么样…

```
$ pear install XML_Parser   

$ pear install XML_Util   

$ pear install XML_Serializer
```

第一行可能会告诉您已经安装了，这取决于您在安装时是否接受了推荐的软件包。如果有，就继续下一行。会发生什么？应该有一些行动，例如:

```
downloading XML_Serializer-0.9.1.tgz ...   

...done: 12,675 bytes   

install ok: XML_Serializer 0.9.1
```

这里发生了什么？PEAR 包管理器联系 PEAR 网站，找到最新版本的 [XML_Serializer](http://pear.php.net/XML_Serializer) ，下载并安装到您的 PEAR 包库中。您可以立即开始使用 PHP 脚本中的包。

印象深刻吗？顺便说一下，包管理器是用 PHP 编写的。这种语言比看上去的要复杂得多…

PEAR 包管理器提供了许多安装 PEAR 包的机制。上面使用的第一个是 [XML-RPC](https://www.sitepoint.com/blog/) 。它与 PEAR Web 服务器“对话”,并找出应该下载哪个版本之类的东西。

它还能够使用 HTTP 直接获取和安装，下载软件包的方式与浏览器非常相似。例如，如果您想要一个特定版本的 [HTML_QuickForm](http://pear.php.net/HTML_QuickForm) ，比如 3.1.1，您可以从软件包主页获得该文件的链接——在本例中，是【http://pear.php.net/get/HTML_QuickForm-3.1.1.tgz.】T2,然后您可以使用以下命令直接安装它:

```
$ pear install http://pear.php.net/get/HTML_QuickForm-3.1.1.tgz
```

您还可以从本地文件系统安装手动下载的文件，如下所示:

```
$ pear install /home/harryf/downloads/HTML_QuickForm-3.1.1.tgz
```

这也意味着一个包不一定要在 PEAR 网站上(或者根本不是 PEAR 的一部分)才能让你使用。其他项目，比如 ISMO 框架(T1)，发布了为包管理器准备好的版本。tgz 文件)并可以使用相同的方法直接安装。如果你有一个你想让人们更容易安装的库，你也可以这么做。

在尝试安装 HTML_QuickForm 时，您可能遇到了一个问题:一条错误消息说它依赖于 [HTML_Common](http://pear.php.net/HTML_Common) 。一些包需要其他包可用。如果你看看底部的 [HTML_QuickForm 主页](http://pear.php.net/HTML_QuickForm)，你会注意到它依赖于 HTML_Common。这意味着首先需要安装 HTML_Common。

那么，PEAR 包管理器如何知道如何处理一个包呢？例如，如果你用你的浏览器下载某个版本的 HTML_QuickForm 并解压，你会在那里找到一个名为`package.xml`的文件。这包含了关于包的代码应该放在哪里的所有信息，相对于您的 PEAR 库目录。如果你曾经*真的*死在水里并且不能安装包管理器，你可以手动解压一个包并根据`package.xml`中的信息将其正确地放入你的包含路径中(参见[这里的完整描述](http://pear.php.net/manual/en/developers.packagedef.php))。不过，这是最后一招——为什么要给自己增加额外的工作呢？

另一个需要了解的有用命令是:

```
$ pear upgrade <package_name>
```

如果你已经安装了一个软件包，这就是你升级到最新版本的方法。确保您首先检查发行说明，以防新版本破坏您的代码。

在安装软件包时，一个小的“陷阱”是“preferred_state”配置设置。目前，这可以设置为“稳定”、“测试版”、“阿尔法”、“开发”或“快照”。现在，假设您将“preferred_state”设置为“stable”(默认值)，并键入:

```
pear install HTML_QuickForm
```

这将导致软件包管理器安装最新的*稳定的*版本的软件包。这意味着，如果有一个新版本的软件包可用，但开发人员将其标记为“beta”，它将被忽略，优先于一个旧的、稳定的版本。这可能意味着，对于从来没有稳定版本的软件包，软件包管理器不会让您安装它们，除非您降低首选状态。

不管怎样，这足够让你开始了。如你所见，多亏了 PEAR，生活已经轻松了许多。

##### 即时 RSS

好吧——该是我说话算数的时候了。毫无疑问，你已经读过 Kev 关于 [PHP 和 XML 的文章:解析 RSS 1.0](https://www.sitepoint.com/blog/) 。它解释了为解析 RSS 1.0 构建自己的 SAX 处理程序的细节，并向您展示了如何从 https://www.sitepoint.com/rss.php 的[的 SitePoint 提要中获取它。](https://www.sitepoint.com/feed)

当然，编写处理 XML 的所有代码需要做大量的工作。另外，如果您试图从代理服务器后面访问提要，您将需要某种能够理解代理服务器生成的头的 HTTP 客户机。PHP 的 [fopen()](http://www.php.net/fopen) 函数帮不了你。

多亏了 PEAR，您可以大大减少解析 RSS 提要的工作量。假设您在那里安装了 XML_Serializer，您还需要 [PEAR::HTTP_Request](http://pear.php.net/HTTP_Request) 。我将把它留给你来安装和解决任何依赖问题。

一旦安装了所需的软件包，请使用以下代码:

```
<?php   

// Include PEAR::HTTP_Request   

require_once 'HTTP/Request.php';   

// Include PEAR::XML_Unserializer   

require_once 'XML/Unserializer.php';   

// Create the HTTP_Request object, specifying the URL   

$Request = & new HTTP_Request('https://www.sitepoint.com/rss.php');   

// Set proxy server as necessary   

// $Request->setProxy('proxy.myisp.com', '8080', 'harryf', 'secret');   

// Send the request for the feed to the remote server   

$ErrorCheck = $Request->sendRequest();   

// Check for errors   

if ( PEAR::isError($ErrorCheck) ) {   

    die ( "Connection problem: ".$ErrorCheck->toString() );   

}   

// Check we got an HTTP 200 status code (if not there's a problem)   

if ( $Request->getResponseCode() != '200' ) {   

    die ( "Request failed: ".$Request->getResponseCode() );   

}   

// Get the body of the response - the XML document   

$xml = $Request->getResponseBody();   

// Create an instance of XML_Unserializer   

$UnSerializer = &new XML_Unserializer();   

// Unserialize the feed and dump the raw array   

if ( $UnSerializer->unserialize($xml) ) {   

    $feed = $UnSerializer->getUnserializedData();   

    echo "<pre>";   

    print_r ( $feed );   

    echo "</pre>";   

} else {   

    die ( "Error unserializing feed" );   

}   

?>
```

看一下上面的例子，很明显需要处理的代码要少得多，然而，由于 PEAR::HTTP_Request 对 HTTP 协议有一些“理解”,我们享受到了更详细的错误处理。此外，您可以立即访问 RSS 提要中的数据(不需要考虑 XML)。最后，只需遍历`$feed["item"]`数组并在 HTML 中显示每一行。这次不讨论 PEAR::HTTP_Request 或者 XML_Serializer 的细节。这个例子只是为了让你了解 PEAR 是如何让你的生活变得更简单的。

任何给定的 PEAR 包*是否会*节省您的时间取决于您是否知道如何使用它，当然，由于文档经常出错，这经常是一个问题。我强烈建议您在将包规划到项目中之前研究它们(这通常意味着查看源代码)。

按照这些思路，你可能会奇怪为什么我不使用 [PEAR::XML_RSS](http://pear.php.net/XML_RSS) 而使用 XML_Serializer。在浏览了 XML_RSS 的源代码之后，我注意到它希望获得一个文件句柄，以便从中获取提要。这将导致 HTTP_Request 出现问题，它将响应作为字符串返回。XML_RSS 应该适合许多需求，但是在这个例子中，我需要一些稍微不同的东西。

##### 包裹

那应该给你足够的梨的味道让你开始！

就个人而言，我认为这是一个伟大的项目，我发现自己越来越多地利用 PEAR 包，只要有一个好的适合。当然，并不是所有的东西都是完美的，有些时候你需要使用技巧和变通方法来使一个包符合你的需要。

PEAR 已经提供了很多功能，并且是目前为止最大的 PHP 编码组合。随着越来越多的 PHP 开发人员支持 PEAR，情况会越来越好。

## 分享这篇文章