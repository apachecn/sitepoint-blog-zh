# 使用 PHP 和 vpopmail 的入站电子邮件

> 原文：<https://www.sitepoint.com/inbound-mail-with-php-and-vpopmail/>

我怀疑你们中的许多人——像 SitePoint 一样——出于各种目的使用 PHP 处理入站电子邮件。例如，我们的仓储/履行公司发送给我们一封带有 XML 附件的**电子邮件，通知我们订单已经发货。它包括各种有用的细节(运输时间，跟踪号码，运费等)，我们需要**存储在我们的数据库**。**

我在过去试图将这种行为硬塞进我们的 **[vpopmail](http://www.inter7.com/index.php?page=vpopmail)** 安装时遇到过问题。Vpopmail 是一套用于 [qmail](http://cr.yp.to/qmail.html) 的便捷补丁，可以轻松处理大量的电子邮件域名和用户。

**关键问题**是，vpopmail 一般以“vpopmail”用户的身份运行，在这个 UID 下执行的同时投递所有邮件。因此，它调用的任何脚本都没有权限访问使我们的应用程序运行的 PHP 库。

大量的谷歌搜索让我一无所获，除了让我们所有的 PHP 库都是世界可读的这个有问题的选项。不用了，谢谢！

我发现我们最好的办法是创建一个本地域来接收相关的电子邮件。在 vpopmail 安装中，本地域的行为就像常规的 qmail 域，而不是 vpopmail 的“虚拟域”方法。

例如**someuser@yourlocal.domain.com**

其中“processing.sitepoint.com”被设置为本地域。

设置本地域很简单；只需将它添加到/var/qmail/control/locals 中(不要忘记也将它放在 rcpthosts 中)。然后-HUP qmail-发送，你就可以开始了。

一旦完成，发给[someuser@yourlocal.domain.com](mailto:someuser@yourlocal.domain.com)的电子邮件就会被发送到 UNIX 系统账户“someuser”中。最重要的是，它是在用户的 UID/GID 的**下交付的。**

因此，处理这些电子邮件的脚本现在作为“someuser”执行，该用户可能已经访问了相关的库，并且可以完成工作。

## 分享这篇文章