# Spamproof 您的网站

> 原文：<https://www.sitepoint.com/spamproof-site/>

任何运营自己网站的人都知道，你需要为访问者提供一种通过电子邮件与你联系的方式。最大的挑战是为你的访问者提供方便的电子邮件访问，而不要让垃圾邮件充斥你的收件箱。本文中描述的技术使我能够显著减少通过我的所有站点收到的垃圾邮件的数量。

##### 准备和抢占

在对垃圾邮件采取有效措施之前，您需要做几件事情。你的电子邮件软件必须能够过滤收到的电子邮件——所有主要的电子邮件应用程序(如 Eudora、Outlook 和 Pegasus)都支持这一功能。我们将使用多个电子邮件地址来过滤垃圾邮件并确定其来源，如果不进行过滤，您将无法有效打击垃圾邮件。

你还需要使用一个提供无限电子邮件别名或地址的网络主机，和/或一个包罗万象的电子邮件地址。“别名”是转发到其他地址的电子邮件地址(例如，webmaster@domain.com 转发到你的真实电子邮件地址)。一个“无所不包”的电子邮件地址将转发任何发送到你的域名中未知地址的电子邮件。

对于我自己的网站，我使用的是“无所不包”,这样每条信息都会发送到我真实的电子邮件地址。然而，如果你有不止一个人的操作，多个电子邮件帐户和别名是非常必要的。

##### 反击

反击垃圾邮件发送者的第一步是了解他们在哪里找到你的电子邮件地址。如果你希望阻止他们，你必须努力保护你的电子邮件地址。一旦你的电子邮件地址落入坏人之手，它就会被制成光盘(当然是通过垃圾邮件)出售给成千上万的垃圾邮件发送者。一旦发生这种情况，你就输了。

***垃圾邮件来源#1:域名注册***

当您注册域名时，您必须提供一个联系电子邮件地址。如果你给他们你真实的电子邮件地址，你就把它给了所有人，包括垃圾邮件发送者。相反，使用便携式电子邮件地址(如 Hotmail)来设置您的域。

如果您有多个域，您还可以在您的主域上使用别名(domains@yourdomain.com 进行所有注册。有了别名，你可以使用你的电子邮件软件过滤掉并保存从你的注册商的域名发送到该地址的任何电子邮件。

***垃圾邮件来源#2:网络表单&电子邮件简讯***

如果你在任何网络表单上给出真实的电子邮件地址，或者用它订阅电子邮件时事通讯，你就是在自找麻烦。相反，为每个网站或时事通讯创建一个独特的电子邮件地址。我只是用了网站的域名。

例如，如果你以“sitepoint.com@yourdomain.com”的名义订阅了 SitePoint 论坛报网站，并让你的无所不包的地址将它发送给你，你将永远知道电子邮件来自哪里。如果该地址开始接收垃圾邮件，您可以使用电子邮件软件将其过滤掉。

如果你向搜索引擎或免费链接页面提交，每次都要使用唯一的电子邮件地址。尤其是 FFAs，以垃圾邮件充斥世界而闻名。一旦你给了 FFA 一个电子邮件地址，你可能就不会再使用它了。

***垃圾邮件来源#3:您的网站***

垃圾邮件发送者使用的电子邮件地址的最大来源是您的网站。大多数网站会列出多个联系地址——任何时候一个电子邮件地址以纯文本的形式出现在你的网站上，即使它隐藏在一个表单字段中，你也有可能让那个电子邮件地址被捕获。

为了应对这种威胁，我开发了一套 JavaScript 代码片段，可以满足你向公众显示电子邮件地址的几乎所有需求，而不会让垃圾邮件发送者看到。

##### 大战:保护你的网站免受垃圾邮件攻击

几乎每个网站运营商都希望搜索引擎蜘蛛访问。毕竟，搜索引擎是网络免费流量的最佳来源。如果你不想让他们访问，可以用一个格式正确的“robots.txt”文件轻松地阻止他们。

不幸的是，有另一群蜘蛛带着完全不同的目的在网上爬行。这些蜘蛛访问一个又一个网站来收集电子邮件地址。你可能知道他们为垃圾邮件，电子邮件收割机，或任何其他不公开的名字。

当涉及到控制这些流氓蜘蛛时，robots.txt 文件根本无法完成任务。事实上，大多数垃圾邮件机器人会忽略 robots.txt。但这并不意味着你必须放弃，让他们为所欲为。这里有一些技巧可以阻止这些蜘蛛的踪迹！

***技巧 1:用 JavaScript 屏蔽邮件地址***

各种蜘蛛的弱点之一是无法处理脚本。添加一小段 JavaScript 代码来代替电子邮件地址，可以有效地使蜘蛛看不到该地址，而访问者除了使用最原始的 Web 浏览器之外，都可以访问该地址。

在下面的三个示例中，只需替换您的用户名(电子邮件地址的前半部分，所有@符号之前的内容)和主机名(所有@符号之后的内容)。要使用这些脚本，只需将它们插入到页面的 HTML 中需要显示它们的地方。

**创建防垃圾邮件的 Mailto 链接**

这个 JavaScript 片段创建了一个可点击的链接，启动访问者的电子邮件应用程序，假设他们的系统配置为使用“mailto:”超链接。您可以用自己的消息替换链接文本，但是如果您想将您的电子邮件地址显示为链接文本，请参见示例 2。

```
<script language=javascript>  

<!--  

var username = "username";  

var hostname = "yourdomain.com";  

var linktext = "Click Here To Send Me Email";  

document.write("<a href=" + "mail" + "to:" + username +  

"@" + hostname + ">" + linktext + "</a>")  

//-->  

</script>
```

**显示您电子邮件地址的防垃圾邮件 Mailto 链接**

一些访问者将无法使用 mailto 链接。此代码片段在链接中显示您的电子邮件地址，以便他们可以复制和粘贴，或者手动键入:

```
<script language=javascript>  

<!--  

var username = "username";  

var hostname = "yourdomain.com";  

var linktext = username + "@" + hostname;  

document.write("<a href=" + "mail" + "to:" + username +  

"@" + hostname + ">" + linktext + "</a>")  

//-->  

</script>
```

**显示您的电子邮件地址，但不显示 Mailto 链接**

以下代码片段显示了您的电子邮件地址，但没有可点击的链接:

```
<script language=javascript>  

<!--  

var username = "username";  

var hostname = "yourdomain.com";  

var linktext = username + "@" + hostname;  

document.write(username + "@" + hostname)  

//-->  

</script>
```

***手法二:使用联系方式***

有时候，来自真实访客的大量合法邮件会成为一种负担。在这种情况下，一个简单的解决办法是从你的网站上完全删除你的电子邮件地址，并使用联系方式。网上有许多免费的 ASP、Perl 和 PHP 脚本，可以让你的用户填写表格，并给你发电子邮件。大多数主机提供商现在向他们的客户免费提供这项服务。

通过允许您对不同类型的邮件进行预分类，联系人表单可以让您处理更多的邮件。这可以通过创建包含不同选项的下拉菜单轻松实现(例如，客户服务、账单、技术支持等。)将填充电子邮件消息的主题行，和/或更改表单发送到的电子邮件地址。

由于许多垃圾邮件发送者只是简单地阅读页面的整个 HTML 源代码来搜索任何看起来像电子邮件地址的内容，如果您在表单的 HTML 中包含您的电子邮件地址(例如，作为隐藏字段)，您的联系人表单可能不会保护您。您可以使用 JavaScript 来屏蔽地址，如下例所示，或者如果您有这方面的技能，您可以将电子邮件地址嵌入到表单处理脚本中，这样就没有人能找到它。

**在表单字段中屏蔽电子邮件地址**

不要简单地在表单字段中列出您的电子邮件地址，而是使用下面的代码片段来替换包含您的电子邮件地址的表单字段。

```
<script language=javascript>  

<!--  

var username = "username";  

var hostname = "yourdomain.com";  

var linktext = username + "@" + hostname;  

document.write("<input type=hidden name=email   

value=" +username + "@" + hostname" + ">";  

document.write(username + "@" + hostname);  

//-->  

</script>
```

##### 高级技术:URL 重写

Apache 和 IIS Web 服务器都有插件 URL 重写模块，可用于为您的站点提供额外的保护，将查询从已知的垃圾邮件重定向到空白页或另一个网站。这些技术超出了本文的范围，使用它们会降低服务器的速度，哪怕只是一点点。

我希望这篇教程已经让你清楚地了解了如何保护你的网站和你的电子邮件地址免受垃圾邮件发送者的侵害。

## 分享这篇文章