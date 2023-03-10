# 更多关于梨的争论

> 原文：<https://www.sitepoint.com/more-debate-on-pear/>

Via [Tobias Schlitt](http://www.schlitt.info/applications/blog/index.php?/archives/155_PEAR_starts_rethinking_itself.html) ， [Alexey Borzov](http://pear.php.net/user/avb) 引发了一场[关于梨质量和标准的大辩论](http://marc.theaimsgroup.com/?t=108151882100001&r=4&w=2)，部分是受[另一场讨论](http://forums.devnetwork.net/viewtopic.php?t=17235&postdays=0&postorder=asc&start=15)的启发，这场讨论提出了许多与梨相关的非常有效的问题(Sitepoint 论坛的常客会发现一两个熟悉的名字)。

在某种程度上，我有点厌倦这个话题，它似乎没有明确的解决方案重复自己。这很像在 PHP 中谈论[模板引擎](http://developers.slashdot.org/article.pl?sid=04/03/30/0026217&mode=thread&tid=126&tid=156&tid=169)。与此同时，Alexey 最初的评论之后的评论设法保持相当集中在具体问题上，而不是全力以赴的咆哮，这是令人鼓舞的。问题是，当它从每个人的思维雷达上消失时会发生什么？具体点位会跟进吗？我们会看到更多的志愿者愿意贡献时间来“纠正错误”吗？

只是为了让这场辩论更进一步，一些关于 PEAR 的随想

–应该使用 PHP 论坛应用程序，如 [phpBB](http://www.phpbb.com) 。无论技术问题(镜像等。等等。)，PEAR 的一个方面是它是一个开发者的*社区*。在我看来，邮件列表只为小团队保持联系而工作，除此之外别无他用。像 [Sitepoint 的高级 PHP 论坛](https://www.sitepoint.com/forums/forumdisplay.php?f=147)这样的论坛带来了更高层次的辩论和更多的参与。比如问 Sitepoint，成功的社区是怎么建立起来的。

–接受[简单测试](http://www.lastcraft.com/simple_test.php)作为替代 [PHPUnit](http://pear.php.net/package/PHPUnit) 。抱歉，但是(有人需要冒险)PHPUnit 不能降低等级。像[模拟对象](http://simpletest.sourceforge.net/SimpleTest/tutorial_MockObjects.pkg.html)这样的缺失特性是必不可少的。现在我猜只有大约 20%的 PEAR 包带有单元测试(PHPUnit 本身不在其中)。简单的测试将改变这一点——它易于使用，记录非常完整，自带测试(大提示)，发展良好，甚至打包了“web 测试”功能(它可以充当 web 浏览器，用于测试 HTML)。单元测试对整个项目的成功至关重要。如果没有单元测试，PEAR 将仍然是许多小块。

–质量无法直接量化。试试[禅与摩托车保养的艺术](http://www.amazon.com/exec/obidos/tg/detail/-/0553277472/)。同时，结合“近似度量”，如[下载统计](http://pear.php.net/package-stats.php?pid=46&rid=&cid=7)，可以用来查看什么是“重要的”。

–调查用户(调查工具？).“我们可以抛弃 PEAR.php 吗？”

–PEAR 由自愿捐助者“资助”，在很大程度上独立运作。这永远是一个限制，大多数开源软件都是如此。一般来说，如果需要太多的人工干预，我的猜测是 PEAR 会停止。与此同时，CPAN 的方法——欢迎所有来者——导致了一片混乱。巧妙利用工具是未来的发展方向——由人类做出关键决策的自动化。提议系统似乎是正确的选择。

–简单化的观点“sux/rocks”毫无贡献。梨是一个复杂的课题，需要思考。它也需要积极的贡献者。

–数千个工时已投入 PEAR。从头开始不是一条路。

–PEAR 需要一个 wiki——最好与一个类似 PHP 手册的用户评论系统集成。

–更多想法[此处](https://www.sitepoint.com/article/getting-started-with-pear)。

## 分享这篇文章