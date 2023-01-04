# 发件人政策框架–对抗垃圾邮件的选择

> 原文：<https://www.sitepoint.com/sender-policy-framework-option-to-battle-spam/>

发件人政策框架(spf)是由孟翁翁(Meng Weng Wong)开发的，它是哈德穆特·达尼什(Hadmut Danisch)的 RMX 和戈登·费切克(Gordon Fecyk)的 T2·DMP(Gordon Fecyk)的分支——这两人以前都致力于打击垃圾邮件，主要基于域名伪造。

Wong 是 pobox.com 的创始人兼首席技术官，2004 年，pobox.com 和微软联合向互联网工程任务组(IETF)提交了一份 RFC 状态草案，以获得 spf 和发件人 ID(由微软开发)。围绕微软的发送者 ID 存在争议，因为他们在该过程的一部分申请了专利——这可能会阻碍它成为一个全球开放标准。IETF 在 2004 年末放弃了对 RFC 地位联合提案的考虑。此后，微软向 IETF 重新提交了一份新的发件人 ID 草案。

SPF 暂时又回来了——据信有超过一百万个域名在使用它。

它试图通过拒绝域(即您管理下的域)伪造的电子邮件来打击垃圾邮件。这是通过 DNS 完成的，即在 DNS 区域中指定可以从您的域发送邮件的唯一授权主机，并将它们协调到一个公共 IP 地址。

因此，有人伪造的域名，它被拒绝，因为它不匹配任何现有的批准域名在 DNS。与纯粹的垃圾邮件工具类似，有例外白名单，以及对发现是伪造的邮件是否被退回、标记或允许通过的一些控制。后一种选择有助于在生产中正式使用之前进行实现测试。

如果 SMTP 认证(SASL)应该是强制性的，关于使用 spf 有一些争论。我已经安装了在 SMTP 和 SASL 之前使用 POP 的系统——并且无法想象不使用任何类型的传出限制——然而——不是每个环境都为 SMTP 认证做好了准备。这部分取决于系统管理员和用户。

一个非常基本的 spf DNS 记录如下所示:

" v=spf1 a ~all "

这是 spf 在基本设置的初级水平。这个添加到您的 dns 记录中的声明域 myplace.com 是 A 记录，并且也从 myplace.com 发送邮件。在这种情况下，DNS 区域没有 MX 记录。

它当然支持标准场景，比如存在 MX 记录，第三方也可以从您的域发送邮件(例如 sparklist 这样的电子新闻服务)。

" v = sp f1 MX include:spark list . com ~ all "

该条目声明通过您域名的 MX 记录发送的邮件以及来自 sparklist.com 的邮件(电子邮件简讯)是合法的。

通过添加额外的主机和 MX 记录来说明可以为特定域发送邮件的所有主机，该记录可以继续增长。

向 dns 添加 spf 记录非常简单。在绑定环境中，它作为以下内容添加到区域文件中:

文本中的 myplace.com " v = sp f1 MX include:spark list . com ~ all "

它作为“其他新记录”添加到 Windows DNS 中，并被选为文本记录。

不过，在更改 DNS 之前，你还必须确保你的 MTA 支持它。在三个最受欢迎的开源 MTA 中，Postfix 已经为 spf 做好了准备，其中包括一个名为 libspf2 的插件。Sendmail 在升级过程中相对简单，Qmail 需要比普通补丁更简单的[(惊喜！).](http://www.saout.de/misc/spf/)

对于那些使用 Exim 的人来说，也有一个 spf 如何使用 T1。

## 分享这篇文章