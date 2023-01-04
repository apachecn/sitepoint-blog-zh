# LAMPPIX:CD 上的可引导 web 服务器

> 原文：<https://www.sitepoint.com/lamppix-bootable-webserver-on-a-cd/>

这是一个好主意——[LAMPPIX](http://lamppix.tinowagner.com/)一个 LAMP 网络服务器，可以从光盘启动。它使用了 [KNOPPIX](http://www.knopper.net/knoppix/) ，一个被设计成从光盘启动的 Linux 发行版，以及[XAMPP](http://www.apachefriends.org/en/xampp.html)——一个 Apache +常见嫌疑人的发行版(例如 [mmCache](http://turck-mmcache.sourceforge.net/) )。

对于 PHP 解决方案提供商来说，这似乎是一个向客户分发产品的好方法，不需要他们的专业知识——只需放入 CD 并重启即可。

感谢[乔恩](http://sourceforge.net/users/jon-bangoid/)的提示。

**更新:**——来自 [Deskpro](http://deskpro.com) 的 Chris 的一个很好的观点——看起来你可能需要购买一个 MySQL [MySQL 许可证](http://www.mysql.com/products/licensing/commercial-license.html)如果你像这样打包你自己的代码，当代码没有 GPL 兼容的许可证时。从执照上看；

> 请注意，即使您发布了自己应用程序的免费演示版本，上述规则也适用。

我绝不是许可专家，所以可能是错误的，但请务必调查。我觉得这是 XAMPP 的一个缺陷——也许 Postgres 和/或 Firebird 会是更好的选择。

## 分享这篇文章