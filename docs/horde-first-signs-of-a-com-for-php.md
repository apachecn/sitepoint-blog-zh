# horde:PHP COM 的最初迹象

> 原文：<https://www.sitepoint.com/horde-first-signs-of-a-com-for-php/>

从[一月](http://janschneider.de/cweb/home/index.html)–[评论 API 可用](http://janschneider.de/cweb/home/index,channel,25,story,177.html)；

> 使用这个 API，现在每个 Horde 应用程序都可以向任何对象添加线程讨论。

终于！一个[论坛引擎](http://cvs.horde.org/agora/)你可以与你自己的应用集成。我假设“每个 Horde 应用程序”意味着使用 [Horde 框架](http://www.horde.org/horde/)或者至少适应它的代码。

我认为这是 PHP 的第一次。有些项目(从 PHP Nuke 开始)打包了多个应用程序，但是以一种“全有或全无”的形式，紧密集成在多个层中。这种方法与众不同的地方在于，你拥有有效的独立应用程序，你可以根据需要从自己的代码中使用它们(就像你使用 Windows 应用程序一样)。

一点背景(来自零碎的二手知识)。部落是用 PHP 编写的最成熟的框架/应用程序集合之一，我相信可以追溯到五年前。如果你接触 PHP 有一段时间了，你可能会认出一些来自其他地方的[贡献者](http://www.horde.org/about/team/)，比如 [PECL](http://pecl.php.net) 和 [PEAR](http://pear.php.net) 。同时 Horde 是 PHP 的黑马之一；尽管这个项目已经很久了，但是除了那些*知道*的人之外，我怀疑在 PHP 圈子里没有多少人知道他们在做什么。

可能最著名的 Horde“可交付产品”是基于网络的电子邮件客户端。你可能也在 cvs.php.net 的[看过](http://cvs.php.net)[合唱团](http://www.horde.org/chora/)的演出。查看[项目列表](http://www.horde.org/projects.php)，它包含了几乎所有常见的 web 应用类型。

这就是为什么我对 Jan 的宣布感到兴奋——他所说的在我看来很像 Windows COM 一种将现有应用程序的 API 暴露给新应用程序的机制，使有用的集成成为可能。

除了部落分支之外，我对部落代码库并不熟悉(因为时间的原因，我从来没有停下来认真看过它)(如果这样说公平的话？)像[梨::Log](http://pear.php.net/package/Log) 。对于以这种方式公开 API 成为可能的[分离](http://www.catb.org/~esr/writings/taoup/html/ch01s06.html#id2877777)，似乎确实有一种体面的尊重。

看到 ASP 也很惊讶。在[模板](http://cvs.horde.org/co.php/agora/templates/forums/forums.html)中类似. NET 的标记，尽管当看到[模板实现](http://cvs.horde.org/co.php/framework/Template/Template.php)而*似乎*在每个请求上重新解析模板时会紧张。

会有兴趣听听其他人对 Horde 框架的体验。

从“这是否意味着一个集中的配置存储库？”的角度，我也想知道[注册中心](http://cvs.horde.org/co.php/framework/Horde/Horde/Registry.php)的概念。有一个很好的观点[这里](http://www.catb.org/~esr/writings/taoup/html/ch06s01.html#id2910334)就是这个意思；

> 注册表是 Windows 本身和应用程序使用的属性数据库。每个注册表都存在于一个大文件中。注册表混合了文本和二进制数据，需要专门的编辑工具。其中，一个大文件的方法导致了臭名昭著的

## 分享这篇文章