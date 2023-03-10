# 使用 Stunnel 保护 POP、IMAP 和 SMTP

> 原文：<https://www.sitepoint.com/use-stunnel-to-secure-pop-imap-and-smtp/>

我最近一直在为一些漫游用户和我自己运行安全的 pop 和 smtp 电子邮件。在 Postfix 上运行非常简单，并且在很大程度上保护了检查电子邮件的事务(除了来自中间人的攻击，有点不太可能)。

美妙之处在于[利用 Stunnel](http://www.stunnel.org/)——它允许你按照自己的意愿配置你的首选邮件服务器，并简单地拦截你的安全端口(例如 POP3s 的端口 995 和 SMTP 的端口 465)。这可能不是大规模解决它的方法，因为人们可以在邮件服务器中构建安全的配置——尽管在测试期间，它在小规模上对我来说工作得很好。

使用 Stunnel 的一个好处是它不关心邮件服务器 one 正在运行什么——幸运的是，它唯一依赖的是 OpenSSL——我们大多数人的机器上都默认安装了 OpenSSL。

Stunnel 有一个简单的 Man doc 和一些简单的例子，可以让你快速测试。显然，确保您的邮件客户端处理 SSL 连接——在这一点上相当普遍。

我在我的链接中也有一点过时的 HowTo，即[提供了一个更深入的例子](http://www.linuxquestions.org/questions/answers.php?action=viewarticle&artid=50)。本文还介绍了打开 iptables 防火墙端口、配置 xinetd，并包括 IMAP 信息。

## 分享这篇文章