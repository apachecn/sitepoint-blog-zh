# 扭曲的加密

> 原文：<https://www.sitepoint.com/encryption-with-a-twist/>

在设计公司工作的一大好处就是你可以非常专注于你的日常活动。在我之前的雇主那里，我是一名开发人员、行政人员、销售人员&营销人员、客户服务人员和技术支持人员。我身兼数职，但从未如愿以偿地玩 ColdFusion。

但是这一切都变了！我们设计团队的人(和女孩)都是一流的，多亏了他们，我提高了自己的技能，到处学到了新的技巧。

其中一个技巧涉及到 cfusion_encrypt()和 cfusion_decrypt()函数。这两个功能没有文档记录，可能对共享主机计划的用户不可用，因为它们是 ColdFusion 后端&管理的一部分。因此，在匆忙将它部署到生产环境之前，您会被预先警告要进行测试。

cfusion_encrypt()和 cfusion_decrypt()之所以是唯一的，并且不同于所述的 encrypt()和 decrypt()函数，是因为产生的加密字符串将只包含字母和数字。因此，它非常适合 URL 和向数据库中插入数据，但不适合密码或其他更敏感的信息。

所以帮你自己一个忙，不要把这些函数用于真正重要的数据，比如密码或私人信息。但是它们非常适合加密你的 URL 字符串，从而隐藏你的变量，并可能阻止 SQL 注入对你的网站的攻击。

要做到这一点，只需遵循这段代码。

首先你需要像这样加密你的 URL 字符串:

上面的代码将“show = user data & secretid = # secretid # & userID = # userID #”加密成一个 URL 友好的字符串。这个函数的“MyPassw0rd”部分是锁定和解锁这个字符串的关键。没有这个密钥，我无法打开加密的字符串，任何人也不能。

现在，当用户点击这个链接时，他们的 URL 应该是这样的:

`http://www.example.com/index.cfml?pass=JLASW5UTHOUHIUGL9STIASLAV4ECLA91ATR6EMLAJLE37UQIET9AMLE6IEM9AGOE`

现在，因为它是加密的，你需要解密字符串，然后使变量成为你可以实际使用的东西。

为此，我们使用以下代码:

 `<cfloop list="#thisURLString#" index="thisVar" delimiters="&amp;"><cfset><cfoutput>a:: #listFirst(thisVar,'=')# = #listLast(thisVar,'=')#</cfoutput></cfset></cfloop>` 

上面的代码首先将我们解密的字符串设置为变量“thisURLString”。然后我们循环这个字符串，在它被解密后，我们在我们的页面中重置变量作为局部变量。您可以将这些设置为任何您想要的范围变量，比如 URL、会话甚至应用程序。

这里的想法不是为了 100%的应用程序安全性，而是作为一个障碍，让人们去克服并可能防止 SQL 注入攻击。这也是隐藏网站内部运作的好方法。

尽情享受吧，如果你有一个很酷的技巧，一定要把它发布在 SitePoint.com[的](https://www.sitepoint.com) [ColdFusion 论坛](https://www.sitepoint.com/forums/forumdisplay.php?f=214)上

## 分享这篇文章