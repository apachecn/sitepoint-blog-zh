# 有用的 PHP 浏览器内开发工具

> 原文：<https://www.sitepoint.com/useful-in-browser-development-tools-for-php/>

虽然调试器是存在的，但是在 PHP 中使用它们并没有太多的传统。人们很大程度上依赖于将调试代码直接注入到程序中来检查程序范围。臭名昭著的 [var_dump](http://docs.php.net/var_dump) 已经为此服务，PHP 4 . 3 . 0 版本为我们带来了另一个同样有用的函数——[debug _ back trace](http://docs.php.net/debug_backtrace)。

## 跟踪器和错误处理程序

尽管这两个函数都产生了相当粗糙的输出，所以人们很自然地编写了包装器来解决这个问题。我认为[哈利的漂亮蓝屏](https://www.sitepoint.com/pretty-blue-screen/)是我见过的第一批专用图书馆之一。 [Xdebug](http://xdebug.org/) 在出错时也给出了类似的输出，尽管可以说没有那么漂亮。或者蓝色。

什么*蓝屏*是为了`debug_backtrace`，[克鲁莫](http://krumo.sourceforge.net/ "Version 2.0 of print_r(); and var_dump();")是为了`var_dump`。最近，[FirePHP](http://www.firephp.org/ "FirePHP - Firebug Extension for AJAX Development")——基于[Firebug](http://www.getfirebug.com/ "Firebug - Web Development Evolved")——做了类似的事情。FirePHP 使用 HTTP-headers 将数据从服务器发送到客户端，这在处理非 HTML 输出(例如 Ajax)时非常方便。因为它是建立在 Firebug 之上的，所以它只能在 Firefox 2 上运行(这也是用户从 Firefox 3 降级的另一个原因)。

## 结构

除了这些通用的跟踪工具，一些框架有它们自己的或多或少特定的工具。Symfony 的 [调试工具栏](http://morethanseven.net/posts/nice-bits-of-symfony-web-debug-toolbar/)可能是最令人印象深刻的(相比之下， [Zend Framework 最不深刻](http://framework.zend.com/manual/en/zend.debug.html))，甚至提供了关于数据库查询的信息，这对分析很有帮助。

另一个让我印象深刻的框架专用工具是 [Drupal](http://drupal.org/ "drupal.org - Community plumbing
") [主题开发模块](http://drupal.org/node/209561)。它被宣传为“Drupal 主题化的 Firebug”，这是相当描述性的。如果你没有使用过 Drupal，它有一个复杂的主题化系统来呈现页面，这个工具让你更容易判断哪个部分在做什么。即使您不打算使用 Drupal，看到它看起来有多完美也是很吸引人的。

我认为，大多数具有集成表示层的框架都可以从类似的东西中受益匪浅，尽管具体细节显然会有所不同。我自己的一个项目， [Konstrukt](http://konstrukt.dk/) ，有一个工具可以显示关于哪个控制器以什么顺序渲染的调试信息。这很有帮助，因为给定的页面通常会有几个控制器协同工作来产生最终的响应。

另一个例子是 Cake PHP 的[调试消息](http://www.archive.org/details/UsingDebugMessages)。这个扩展在不同的地方添加了非常具体和描述性的错误消息，这几乎就像是新用户的教程；似乎是个好主意。

## Webgrind

我已经提到了 Xdebug，它对于分析 PHP 应用程序是不可或缺的。上周， [Webgrind](http://code.google.com/p/webgrind/) 首次发布。这是一个基于 HTML 的前端，用于显示 Xdebug 生成的分析输出。已经有了实现这一目的的工具；如果你用的是 Windows， [WinCacheGrind](http://sourceforge.net/projects/wincachegrind/) ，对于基于 Linux 的系统， [kCacheGrind](http://kcachegrind.sourceforge.net/) 。然而，我可以看到几个 Webgrind 的好案例；

首先，零安装客户端更容易实现。对于 Mac 用户来说，这也是唯一的选择。特性集非常有限，但这在某种程度上是一个好处。kCacheGrind 可能相当令人生畏，就剖析的目的而言，Webgrind 的特性似乎相当充分。最重要的是，由 Xdebug 生成的日志文件(它是各种工具的数据格式)会变得相当大。很自然，这使得服务器和客户端在不同的机器上很尴尬——这对 Webgrind 来说不是问题，因为它运行在服务器端。

## 分享这篇文章