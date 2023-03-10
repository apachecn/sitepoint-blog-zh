# 对速度的需求:用 XHProf 和 XHGui 剖析 PHP

> 原文：<https://www.sitepoint.com/the-need-for-speed-profiling-with-xhprof-and-xhgui/>

虽然耐心是一种美德，但在今天的社会中，耐心通常不是一种固有的美德。由谷歌、微软和雅虎等技术巨头进行的研究。已经表明，即使页面加载时间上的亚秒级差异也会对转换产生重大影响。如果这还不够的话，谷歌[早在 2010 年 4 月就将页面加载时间](http://googlewebmastercentral.blogspot.com/2010/04/using-site-speed-in-web-search-ranking.html "Official Google Webmaster Central Blog: Using site speed in web search ranking")纳入了他们的搜索排名，所以它也影响了 SEO。

许多因素会影响页面加载时间:网络延迟、web 服务器配置或负载、持久性存储的使用、缓存或缺少缓存，以及应用程序响应时间。一般来说，应该[怀疑应用程序响应时间，并在最后](http://www.crestechglobal.com/upload/1271225032_Whitepaper_PerformanceTesting.pdf "CresTech: Top Performance Bottlenecks in Web Applications")处理，因为与[数据库服务器或静态资产处理](http://www.puremango.co.uk/2010/04/fast-php/ "Fast PHP - effective optimisation and bottleneck detection - puremango.co.uk")相比，这是一个不太常见的问题，并且调整它的努力通常具有较低的 ROI。然而，知道如何定位影响应用程序性能的问题(通常称为瓶颈)是一项很好的技能，如果您需要这样做的话。

分析是测量服务单个页面请求所涉及的每个函数或方法调用的执行时间的过程。从这个过程中收集的数据可以揭示一些问题，比如某个方法需要很长时间来执行，或者被调用了很多次。

分析 PHP 脚本通常需要安装 PHP 扩展。为此目的，两个比较流行的扩展是[吴镇男·雷瑟斯](http://derickrethans.nl/ "Home - Derick Rethans") ' [Xdebug](http://pecl.php.net/package/xdebug "PECL :: Package :: xdebug") 和脸书的 [XHProf](http://pecl.php.net/package/xhprof "PECL :: Package :: xhprof") 。本文将重点介绍 XHProf。

## 安装 XHProf

XHProf 可以通过 PECL 获得，所以安装它非常容易。例如，可以在基于 Debian 的系统上安装以下软件:

```
sudo pecl install xhprof-beta
echo 'extension=xhprof.so' | sudo tee /etc/php5/conf.d/xhprof.ini >/dev/null
sudo apachectl restart
```

XHProf 生成的原始分析数据不是一眼就能看出来的。因此，某种类型的界面或可视化对于查看它可能是有用的。[xh prof 的一个分支 XHGui](https://github.com/preinheimer/xhprof "preinheimer/xhprof | GitHub") 提供了这个。

XHGui 托管在 GitHub 上，所以有两种安装选择。第一个选项涉及使用 git 来克隆存储库，这提供了一种以后更新它的简单方法。

```
git clone git://github.com/preinheimer/xhprof.git
```

如果您不喜欢使用 git，第二种选择是下载并提取一个反映存储库当前状态的 tarball，如下所示:

```
wget -O xhgui.tar.gz https://github.com/preinheimer/xhprof/tarball/master && tar -zxf xhgui.tar.gz
```

此时，您可以使用`INSTALL`文件中的指示来指导您完成特定服务器环境的剩余过程。`xhprof_html`目录包含了用于加载 XHGui 接口的`index.php`文件，所以要确保您的 web 服务器被配置为使用符号链接、别名指令或类似的东西在您的主机的文档根目录中公开该路径。

创建用于存储分析数据的数据库表的 SQL 位于`xhprof_lib/utils/Db`目录中 PHP 文件顶部的注释块中。使用适合您的环境的数据库服务器的文件来创建数据库表，然后将访问它的凭证添加到 XHGui `config.php`文件中。

如果您的 web 服务器没有在您的本地主机上运行，不要忘记将您的本地主机的 IP 地址添加到 XHGui `config.php`文件中的`$controlIPs`变量中，否则您将得到一条错误消息，指出“您没有权限查看此页面”

一旦所有这些都准备就绪，当您浏览到 XHGui 时，它应该能够成功加载。

![](img/d1b83366f63c334908240e7a77bd7e8a.png "xhprof in browser")

关于 XHGui 提供的其他配置设置的更多信息，[参见链接在`INSTALL`文件底部的 PHP Advent 博客文章](http://phpadvent.org/2010/profiling-with-xhgui-by-paul-reinheimer "PHP Advent 2010 / Profiling with XHGui")。

如果您在开发环境中运行 XHGui，那么有一个地方可能与指令略有不同，那就是配置`auto_prepend_file` PHP 配置设置的指令。[根据 PHP 手册](http://php.net/manual/en/ini.list.php "PHP: List of php.ini directives - Manual")，该设置可以根据目录进行配置，这意味着如果您的应用程序有一个服务请求的中央端点，您可以使用一个`.htaccess`文件，而不必修改您的虚拟主机配置并退回您的 web 服务器。这方面的一个例子如下所示。

```
php_value auto_prepend_file "../../xhprof/external/header.php"
```

该设置在提供任何 PHP 文件之前执行指定的 PHP 文件。这个特定的文件允许在要分析的代码被执行之前收集分析数据，并且一旦代码已经完成它的执行，[建立一个关闭函数](http://php.net/register_shutdown_function "PHP: register_shutdown_function - Manual")来存储该数据。

## 分析您的应用程序

出于本文的目的，为 Zend Framework 2 提供的[框架示例应用程序](https://github.com/zendframework/ZendSkeletonApplication/ "zendframework/ZendSkeletonApplication - GitHub")被用作概要分析的主题。上面的`.htaccess`文件放在该应用程序的公共目录中，该目录被配置为主机文档根目录。要为特定的 URL 启用概要分析，请将`_profile=1`追加到它的查询字符串中。例如，本文中使用的环境使用主机名`zf2.local`，使得 URL 能够访问启用了概要分析的应用程序`http://zf2.local/?_profile=1`。这将设置一个 cookie，然后重定向回应用程序，删除查询字符串变量；有了 cookie，您就不需要修改查询字符串来为后续请求启用概要分析。

一旦应用程序请求完成，概要分析数据将存储在您配置 XHGui 使用的数据库中。只需刷新 XHGui 界面，它的数据网格就会在顶部列出新的分析运行。数据网格还有一些链接，可以根据时间段、内存使用、运行时间、服务器和域轻松过滤运行。

单击为一次运行列出的时间戳，XHGui 将显示一个页面，详细说明在那次运行期间收集的数据。左上角的表格显示了汇总的统计数据，比如总运行时间、内存使用量和函数调用计数。右上方显示了 GET、POST 和 cookie 数据的表格，以及一个饼图，详细说明了请求期间进行的昂贵的函数调用。

向下滚动，您会发现一个列出函数及其相应统计数据的数据网格。如果您正在为运行时进行分析，您通常希望关注的两列被标记为“独占墙时间”和“调用计数”对于内存使用，请查看标有“独占内存使用”和“独占峰值内存使用”的列。单击任意网格列，按该列对网格数据进行排序。单击单个函数名称可查看一个详细页面，该页面包括该函数及其父函数(即调用它的函数)和子函数(即它调用的函数)的统计信息。

## 比较分析运行

分析的常见用例是分析一个应用程序，对该应用程序的源代码进行更改，然后再次进行分析，以查看所做的更改会对性能产生什么影响。XHProf 和 XHGui 旨在使这一工作变得相当容易。

在 XHGui 中，在用于分析运行的数据网格中，每次运行都有一个显示在其时间戳下的哈希值。选择两次运行中您想要比较的一次，并复制其哈希值。单击另一次运行的时间戳以显示其详细信息页面。最后，在左上角数据表的底部，将哈希值输入标有“Perform Delta”的文本框，并点击旁边的“Delta”按钮。

这将显示一个页面，其中您输入哈希值的运行被标记为“运行二”，您查看详细信息页面的运行被标记为“运行一”与详细信息页面一样，它显示聚合统计信息和每个函数调用的统计信息。这些函数调用统计数据是通过从第二次运行的统计数据中减去第一次运行的统计数据计算出来的。例如，函数统计表中 main()函数行的“墙时间”列值对应于聚合统计表中“运行二”和“运行一”的“包含墙时间”单元格的值之差。

![](img/b86bff6c3280fe3e2026bda8ba495d62.png "statistics tables")

您可能在不做任何更改的情况下对一个应用程序进行了两次分析，并在两次分析中发现了显著的差异。一个可能的原因是 PHP 维护自己的文件系统查询的 stat 缓存以提高性能。为了减少 xhprof 分析运行中的这种差异，在 XHGui 安装中的 external/header.php 的顶部添加一个对 clearstatcache 函数的调用。

## 查看调用图

XHGui 提供的另一个特性是生成调用图，即显示函数间调用的[有向图](http://en.wikipedia.org/wiki/Directed_graph "Directed graph - Wikipedia, the free encyclopedia")，直观地突出显示占用大量运行时间的函数调用。xh GUI shell out to the dot[graphviz](http://www.graphviz.org/ "Graphviz | Graphviz - Graph Visualization Software")实用程序来做这件事。下面是在基于 Debian 的系统上安装软件包的命令:

```
sudo apt-get install graphviz
```

注意，您还需要取消对 XHGui `config.php file`中类似下面的代码块的注释，否则 XHGui 将返回一个相当不直观的错误消息来代替调用图。感谢 Lorna Jane Mitchell 的博客文章指出了这一点。

```
<?php
//These are good for linux and its derivatives.
$_xhprof['dot_binary']  = '/usr/bin/dot';
$_xhprof['dot_tempdir'] = '/tmp';
$_xhprof['dot_errfile'] = '/tmp/xh_dot.err';
```

在单次运行或两次运行之间的差异的详细信息页面的顶部附近，您会发现一个“查看调用图”链接。单击它将生成一个调用图，如下所示。请注意，对于带有大量函数调用的概要文件运行，这可能需要一段时间。您也可以单击单个函数名，然后单击其详细信息页面上的“查看调用图”链接，查看特定于该函数的调用图。

![](img/281b39c1116bb22fb024e1d9f36d9ddb.png "callgraph")

## 最后

我希望您喜欢对 XHProf 和 XHGui 提供的功能的概述，并且它提供了对有效的分析工具集的价值的洞察。虽然您的应用程序层可能是导致性能瓶颈的一个不常见的原因，但是您现在应该已经具备了隔离这些原因并采取进一步措施消除它们的必要知识。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章