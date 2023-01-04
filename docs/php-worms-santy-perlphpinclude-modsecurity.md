# PHP 蠕虫:Santy / Perl。PHP include–ModSecurity

> 原文：<https://www.sitepoint.com/php-worms-santy-perlphpinclude-modsecurity/>

看起来有人终于变得讨厌了，写了针对人们在 PHP 中经常犯的潜在错误的代码。尽管你可能在度假，但建议至少考虑一下这些，如果有疑问，就按照基督教的做法去做，让它离线，直到你有时间处理它。

**桑蒂**

希望你已经学会了这一点，但是如果你使用 phpBB，你应该升级到 2.0.11(见[公告](http://www.phpbb.com/phpBB/viewtopic.php?t=244451))。

原因？圣虫。这个甚至制作了 [BBC 网站](http://news.bbc.co.uk/1/hi/technology/4117711.stm)(讽刺的是，可能是 PHP 或 PHP 应用的第一次)。

围绕 Santy 有一些困惑，例如[这个公告](http://www.computerworld.com/securitytopics/security/virus/story/0,10801,98453,00.html)，这表明该蠕虫利用了 PHP 本身的一个漏洞。[吴镇男](http://www.derickrethans.nl/)对此进行了澄清[这里](http://www.derickrethans.nl/month-2004-12.php?item=200412241207#200412241207)–phpBB*也*暴露了 PHP 的 unserialize()函数的问题(在最新的 PHP 版本中已修复)，但这是*而不是*蠕虫所使用的。[伊利亚](http://ilia.ws/)在这里讨论了 unserialize() vulns [。](http://ilia.ws/archives/31-phpBB-unserialize-bug.html)

到目前为止，我所发现的经过深思熟虑的最新信息可以在[这里](http://www.viruslist.com/en/weblog)找到。现在有不止一个版本的蠕虫，最新的版本是 [Santy.e](http://www.viruslist.com/en/weblog?discuss=156861672&return=1) ，也被称为 [Perl。PhpInclude.Worm](http://www.k-otik.com/exploits/20041225.PhpIncludeWorm.php) (显然与 Santy 无关)。

**Perl。PhpInclude**

Perl。蠕虫似乎利用了一个常见的*潜在的*漏洞/错误，我在这里描述了(去年 4 月 1 日)[。*确保你读到了*](https://www.sitepoint.com/blog/)*[这个](http://www.securereality.com.au/studyinscarlet.txt) : 4。远程文件*并且非常认真的对待这个*。*

 *应该可以创建一个 shell 脚本来扫描文件系统中可能包含此类问题的代码，查找包含 PHP 变量的 include 语句(以及类似的语句)。今天没有时间了，但明天会尝试(除非有人先到了)。

**ModSecurity**

考虑了一段时间关于 ModSecurity 的博客/讨论。这让我觉得是时候继续前进了。我欠它的作者[伊万](http://www.webkreator.com/)一些邮件——ModSecurity 出现在[奥斯卡上。这只是一些快速笔记…](http://www.oscom.org/events/oscom4/)

首先，我强烈推荐网站托管公司和像 phpBB 这样关注 ModSecurity 的项目开发者。Ivan 已经在博客上为 Santy [做了一个过滤。](http://www.modsecurity.org/blog/archives/000046.html)

关于 ModSecurity 的一些关键的东西(这里指的是 C 版本，不是 Java 版本)；

这是一个阿帕奇模块

–它首先过滤传入的请求，然后 Apache 对它们做其他事情(比如移交给 PHP)

–它是基于规则的，规则可以包含正则表达式(以及其他内容)

–您可以让它执行非常精细的过滤(例如过滤一个名为“highlight”的 GET 变量)

–可以在 httpd.conf 和中进行配置。htaccess 文件

–它可以像“允许所有/拒绝一些”或“拒绝所有/允许一些”模式一样工作，类似于其他 Apache 规则

–这真的是你能做的最简单的事情

-这里有一篇伊万关于奥莱利的教程

潜在的主机可以使用 ModSecurity 来筛选所有可能被利用的请求，例如 Javascript XSS 和 Santy 蠕虫(允许所有/拒绝一些)。像 phpBB 团队这样的开发人员也可以为他们的应用程序发布“有效的请求签名”——他们认为是有效输入的任何东西(拒绝所有/允许一些)。* 

## *分享这篇文章*