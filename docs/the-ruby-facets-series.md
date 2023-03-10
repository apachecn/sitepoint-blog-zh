# 红宝石刻面系列

> 原文：<https://www.sitepoint.com/the-ruby-facets-series/>

几年前，我在从事一个遗留的企业 Java 项目，客户对生产环境非常着迷。他们不允许我的团队部署我们正在开发的应用程序的新版本。最有趣的事情是他们不允许我们在数据库上工作。我指的是备份、性能调整等等。

在一个晴朗的日子里，他们的系统管理员有了一个非常棒的主意。他们决定在不改变 IP 地址的情况下，将数据库物理移动到另一台机器上。当然，他们没有告诉我们，因为他们确信应用程序不会注意到任何事情。但是你要知道:*理论上，理论和实践是没有区别的。但是，实际上是有的。*事实上，由于一个我至今仍忽略的原因，当他们恢复数据库时，所有表的空字段都被设置为空字符串。很好。

奇怪的是，他们打电话给我们抱怨我们的申请。为什么我们的应用程序停止工作了？是的，我会杀了他们，但是他们制造了一个有趣的问题。我们需要一个脚本来恢复数据库中所有的空字符串。当然，他们仍然对生产环境心存疑虑，所以他们要求在运行之前可以检查一个 SQL 脚本。

露比出场了。我需要一个快速的脚本来查找数据库中所有的空字符串。我匆忙写了这个脚本，我真的错过了我在 Perl 时代的一个特性。我想把所有的空字符串和表名、字段名以及其他我现在不记得的东西保存在一个多层散列中。嗯，缺少的功能是[自动逼真化](http://en.wikipedia.org/wiki/Autovivification)。现在，总的来说，我一点也不想念它，但是，在那种困难的情况下，我不能花太多时间去寻找一个 Ruby 方法。所以我搜索了一个能给我这个特性的库。红宝石刻面来了。

我终于找到了这个神奇的库，它给了我以下特性: