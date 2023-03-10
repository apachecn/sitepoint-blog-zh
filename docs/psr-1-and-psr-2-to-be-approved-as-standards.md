# PSR-1 和 PSR-2 将被批准为标准

> 原文：<https://www.sitepoint.com/psr-1-and-psr-2-to-be-approved-as-standards/>

我希望你们都知道 [PHP 框架互操作性组]( https://groups.google.com/forum/?fromgroups#!forum/php-standards " PHP Standards Working Group - Google Group")。该组织提出了开发人员可以遵守的标准(PSR ),以便更容易地一起使用他们不同的库和框架。第一个提议是用于自动加载类定义的 [PSR-0](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md "fig-standards/accepted/PSR-0.md at master - php-fig/fig-standards - GitHub") ，并获得了巨大的成功。最近，该小组发现解决应该在不同项目中使用的编码标准很重要。这个提议最早是由[克劳斯·西尔维拉](https://github.com/php-fig/fig-standards/pull/14 " Pull Request #14: PSR-1, a standard coding convention for PHP by klaussilveira - php-fig/fig-standards - GitHub")提出的，在该组织的邮件列表中经过大量讨论后，由[保罗·M·琼斯](http://paul-m-jones.com " Paul M. Jones | It's not enough to be smart; you have to actually know things.")进行了大量修改。

当然，在意见上有很多分歧，小组成员以友好的方式努力促成了 PSR 一号和 PSR 二号提案。他们最初是一个提案，但第一轮投票没有获得多数支持。然而，参与者确实看到了各种需求的优点，于是决定将其分成两个提案——一个是强制性的互操作性，另一个是建议的风格。

对 PSR-1 和 PSR-2 的投票已经开始，只有获得多数票，它们才会被接受为标准。有 20 个成员，PSR-1 和 PSR-2 都有超过 11 票，这意味着它们将很快被接受为标准。这些建议是:

*   [PSR-1 提案-基本编码标准](https://github.com/pmjones/fig-standards/blob/psr-1-style-guide/proposed/PSR-1-basic.md "fig-standards/proposed/PSR-1-basic.md at psr-1-style-guide - pmjones/fig-standards - GitHub ")
*   [新 PSR 协议提案–编码风格指南](https://github.com/pmjones/fig-standards/blob/psr-1-style-guide/proposed/PSR-2-advanced.md "fig-standards/proposed/PSR-2-advanced.md at psr-1-style-guide - pmjones/fig-standards - GitHub")

我感谢琼斯作为一个领导者为 PSR 付出的时间和奉献。这将有助于为整个 PHP 社区带来一个美好的未来。

还有更多好消息，PSR-1 和 PSR-2 可以用 PHP-CS-Fixer 来执行。目标是自动修复大多数问题，该工具知道如何修复 PSR-1 和 PSR-2 文档中定义的编码标准的问题。感谢杨奇煜在嗅探器上所做的努力；你做得很好。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章