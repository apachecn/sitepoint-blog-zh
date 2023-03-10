# PHP 5 PDO 简介

> 原文：<https://www.sitepoint.com/introduction-to-php-5-pdo/>

嗨！我是大卫·米顿，新的 SitePoint PHP 博客作者，我将会在接下来的三个月里为这个博客写作。我将试着把重点放在技术文章上，提供一些有用的代码和 PHP 新(旧)特性的信息，希望这对我和读者来说都是一次很好的学习经历。

关于我的一点点:

在过去的里，我为 SP 写了几篇文章，大部分是评论或采访，我也有一些文章发表在国际 PHP 杂志上。我也是[视觉电源板 2:用户指南](http://www.packtpub.com/invision_power_board/book)的作者，这是一本即将为 IPB 出版的新书。除了为印刷品写作，我还通过我在英国的有限公司[olet](http://www.olate.co.uk)编写商业和开源 PHP 软件。

除了计算机，我还潜水和击剑(是的，用剑！).

如果你对博客的帖子有任何想法，请联系我们。在这篇文章的左下方有一个“建议一篇文章”的框，你可以这样做。

无论如何，在主帖上…

PHP 5 PDO 简介 [/u]

新的 PHP 数据对象(PDO)已经在过去的博客文章中介绍过了，但是我想我应该更详细地介绍一下。

在读完博文和 Wez Furlong 的博客后，我尝试了一些 PDO 代码。尽管已经有[一些](http://adodb.sourceforge.net/) PHP 数据库抽象类[可用](http://pear.php.net/manual/en/package.database.db.php)，PDO 的优势在于它将在运行时在任何 PHP 安装中可用(只要它被启用)，而不需要任何重新编译。此外，因为它是专门为 PHP 5 从头开始编写的，所以它具有很高的性能。如果你像我一样，经常只想要关键的功能，那么 PDO 提供了所有你需要连接到不同数据库的基本功能。如果你需要“完整的作品”，那么 [PEAR MDB2](http://pear.php.net/package/mdb2) 包可以处理。

[u]注意:[/u] PHP 5.1 Beta 3 于 7 月 14 日发布，包含了许多对 PDO 的更新。你可以在 http://netevil.org/node.php?nid=325 了解这些变化

在我们继续之前，这里有一个来自 Wez Furlong(PDO 的开发者)的重要提示:

> 请注意，PDO 和它的司机目前处于“阿尔法”状态；这意味着我们有理由相信没有大的错误，但是这个包的特性并不完整

## 分享这篇文章