# PHP 6.0 成分

> 原文：<https://www.sitepoint.com/php-60-ingredients/>

在周末的其他新闻中， [Rasmus](http://lerdorf.com/bio.php) 发布了他个人[对 PHP 6.0](http://news.php.net/php.internals/17883) 特性的愿望清单。[在 SitePoint 论坛上，关于这些提议的评论大多是正面的，但也有一些警告。Web 主机担心向后兼容性问题，提到 4.x 版和 5.0 版之间的兼容性中断已经证明是一个大问题，而 5.x 版和 6.0 版之间的向后兼容性中断甚至更大。](https://www.sitepoint.com/forums/showthread.php?t=290049)

很长一段时间以来，我一直非常希望在 PHP 中看到对包括 UTF-8 在内的字符编码的适当支持。目前，PHP 没有内部 Unicode 支持，尽管可以用 [mbstring](http://au3.php.net/mbstring) 扩展名(遗憾的是默认情况下没有启用)或像 [iconv](http://au3.php.net/iconv) 这样的外部工具进行转换。由于 PHP 假定所有的字符都是一个字节，所以编写一个使用 UTF-8 作为其内容的 Web 应用程序可能会很困难。

[php.general post 本身](http://news.php.net/php.internals/17883)包含了愿望列表，它的回复包含了更多的信息和建议。这些建议中包括完全删除 [register_globals](http://au2.php.net/register_globals) 和[magic quotes](http://de.php.net/magic_quotes)——正如[你可能会猜到](https://www.sitepoint.com/blog/)，我认为这是一个很棒的主意。我不太确定他的第三个建议，它涉及到 get、post 和 cookie 变量的某种输入过滤器，因为它让我想起了 magic_quotes_gpc。然而，这些建议在这个非常早期的阶段是非常假设性的，不应该被看作是 PHP 正在努力的官方指示。

前往我们的论坛查看[关于愿望清单](https://www.sitepoint.com/forums/showthread.php?t=290049)的进一步讨论。

## 分享这篇文章