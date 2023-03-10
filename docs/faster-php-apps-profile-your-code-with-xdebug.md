# 更快的 PHP 应用——用 Xdebug 分析你的代码

> 原文：<https://www.sitepoint.com/faster-php-apps-profile-your-code-with-xdebug/>

面向对象编程非常流行，PHP 也不例外。所以你已经采用了[特有的](http://www.zend.com/zend/trick/tricks-app-patt-php.php) [设计](http://www-128.ibm.com/developerworks/library/os-php-designptrns/) [模式](http://www.phppatterns.com/docs/start)，在[最新的](http://www.symfony-project.com/) [PHP](http://www.cakephp.org/) [框架](http://framework.zend.com/)上构建了你的站点，并且你的数据库访问已经抽象到你已经忘记了 SQL。

**到底怎么回事？**

当您第一次转向面向对象编程时，您可能注意到的第一件事是，您可能会为试图通过您的代码和构建它的框架来遵循执行路径而感到头痛。

下一件你可能会注意到的事情是，你的网站并没有变得更快——事实上，可能比以前更慢了。面向对象编程通常旨在提高代码的质量、可维护性和可重用性，但有时这是以一些原始速度为代价的。

所以我们得到了潜在的更慢的代码，我们不能再打开简单的 PHP 脚本，从文件的顶部到底部跟踪它的执行。我们如何弄清楚里面到底发生了什么？

Xdebug
[Xdebug](http://xdebug.org/) 2(目前是测试版——我运行的是 2.0.0RC3)是一个不可或缺的 PHP 扩展，它提供了在开发服务器上调试和分析代码的各种方法。今天，我们对 PHP 脚本的[概要信息感兴趣。](http://xdebug.org/docs-profiling2.php)

在 apache/PHP 开发服务器上安装 Xdebug 超出了本文的范围，但是一个快速的“sudo pecl install xdebug-beta”和对我的 php.ini 的一行编辑就让它在我的 Ubuntu 6.10 虚拟机上运行了。

一旦安装了 Xdebug 2，您的。htaccess 将为 PHP 处理的所有请求打开自动分析:

```
 php_value xdebug.profiler_output_dir /tmp
php_value xdebug.profiler_output_name timestamp
php_value xdebug.profiler_enable 1 
```

现在要打开出现在/tmp 目录中的输出文件…

**WinCacheGrind**
[WinCacheGrind](http://sourceforge.net/projects/wincachegrind/)是从 CacheGrind 输出文件中获取有用信息的最简单方法。它提供了一个简单的 PHP 执行树视图，包括类、函数和文件名的引用。最重要的是，它告诉你每个函数调用花了多长时间。

![WinCacheGrind example](img/162bda1769be46d2ab5783c4db510764.png)
**KCachegrind**

如果你运行的是 [KDE](http://www.kde.org/) (如果你敢的话，可以通过 [fink](http://finkproject.org/) 在 Linux 或 Mac OS X 上运行)，你可能想试试 [KCachegrind](http://kcachegrind.sourceforge.net/) 。它比 WinCacheGrind 复杂得多，但以一些非常有趣的方式呈现了您的 CacheGrind 数据。

在这个例子中，callmap 视图清楚地显示了通过 mysql_query()的 SQL 查询负责大部分执行时间。
![Xdebug call map example](img/ee3cb2cc51acc3fba265c89ba6a7e9b6.png)

这里我们看到了调用图的一部分，显示了函数交互。
![Xdebug call graph example](img/a6c101880ea5b7fe8091c0e60a4f37c0.png)
**少磨，多缓存**

让代码运行得更快的最简单的方法是什么？缓存，缓存和更多缓存。

使用你的新工具找到你脚本中的慢点。确保慢速查询利用您的 [MySQL 查询缓存](http://dev.mysql.com/doc/mysql/en/query-cache.html)。尝试 PHP 字节码缓存，如 [APC](http://pecl.php.net/package/APC) 。用 [memcached](http://www.danga.com/memcached/) 缓存你的 PHP 对象。缓存生成的 HTML 的公共部分。[在发送之前缓存整个页面](http://www.jpcache.com/)。让你的用户用 [Expires、ETag 和 Last-Modified HTTP header](https://www.sitepoint.com/php-anthology-3-php-mysql/)缓存你的页面。缓存层数越多越好。

**更多 Xdebug**

Xdebug 不仅仅输出分析数据——我认为它是后端开发人员的 [Firebug](http://getfirebug.com/) 。我建议任何使用 PHP 的人在他们的开发服务器上设置它，即使只是为了丰富多彩的 PHP 错误报告和完整的堆栈跟踪:

![Xdebug error reporting](img/111661281885df50b3f17b7a5ca47a4a.png)

从这个谷歌代码项目之夏来看，用 Xdebug 进行实时交互调试将很快对所有人开放。

## 分享这篇文章