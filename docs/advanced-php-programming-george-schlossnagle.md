# 高级 PHP 编程:George Schlossnagle

> 原文：<https://www.sitepoint.com/advanced-php-programming-george-schlossnagle/>

我早就想这么说了，但是乔治·施洛斯纳格的书-[高级 PHP 编程](http://www.amazon.com/exec/obidos/tg/detail/-/0672325616/georgeschloss-20)非常棒。这并不意味着一个广泛的审查，而是一些想法，在图书目录页的相反顺序…

–关于 Zend 内部和扩展 PHP 的报道是我见过的最好的(PHP 非常需要这个),这也是我购买它的原因。不仅解释了如何扩展 PHP 还有对 Zend 引擎、PHP 执行生命周期等等的详细讨论，我相信这是基于 George 对 apd 的经验。即使您不打算编写自己的 PHP 扩展，也有一些有价值的见解可以帮助影响您的 PHP 应用程序的设计，特别是在性能方面。对 C 的知识是假定的(否则它会是一本*非常*的大书)，所以被警告，但乔治认为已经成功地证明了[这本](http://www.php.net/manual/en/zend.php)盗用《道德经》的错误(有人请它杀人)。当我在谈论 PHP 扩展时，还有另一本书，[Building Custom PHP Extensions](http://www.amazon.com/exec/obidos/tg/detail/-/1411601882/)是一个有用的参考(可能还没有为 PHP5 更新),但是缺乏洞察力和解释。

–关于性能问题和可扩展性的讨论占了很大一部分，George 从他工作过的网站中获得了独特的见解。在某种程度上，我几乎想质疑这个标题——“高性能 PHP”可能更好。除了对缓存等问题的开放式讨论之外，这次讨论的一个重要部分着眼于帮助站点处理高流量的支持技术；发现它们作为“大画面”的一部分在单本书中进行了检查是这本书的另一个特别之处——即使你不使用 PHP，如果你处于必须运行/开发暴露在严重流量下的 web 应用程序的位置，它也值得一读。底线是，再一次，它是 PHP 目前唯一的同类材料。为了了解预期的结果，有必要阅读一下 George 最近在 Oracle 技术网上发表的文章:[扩展 Oracle 和 PHP](http://otn.oracle.com/pub/articles/php_experts/scaling_oracle_and_php.html) 。

–本书的第一部分探讨了我称之为“开发实践”的东西，以及大多数关于 PHP 本身的讨论发生在哪里——如编码标准、面向对象程序设计、CVS、单元测试等主题。乔治“敢于”使用 [Smarty](http://smarty.php.net) 和 [PHPUnit](http://pear.php.net/phpunit) ，这可能不会取悦所有人，但概念是可以转移的。

这本书的风格是讨论和“实现”，而不是一步一步的解决方案。例如，关于缓存的那一章从未触及“答案”(有答案吗？)但是提供了外观和许多不同的方法，以及何时何地他们做出了好的选择。

这可能不是开始编写 PHP 代码的好地方，除非你已经有了其他编程语言的经验。一旦你读了它，你可能会发现自己已经穿戴整齐，却无处可去(例如，寻找一个高流量网站的雇主)，但即使你不能将它提供的一切付诸实践，也有一大堆弹药回击那些对 PHP 出言不逊的 Java 开发人员。

总的来说，高级 PHP 编程与红皮书不相上下，是 PHP 书架上非常需要的补充。正如[约翰所说](http://php.weblogs.com/discuss/msgReader$3510?mode=day)；“乔治，你是男人！”。

## 分享这篇文章