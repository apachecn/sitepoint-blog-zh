# 什么？为什么会有人黑我的小企业网站？

> 原文：<https://www.sitepoint.com/someone-hack-small-business-website/>

*这篇文章最初是由 [Oliver Sild 在媒体](https://medium.com/@oliversild/why-the-hell-would-anyone-hack-my-small-website-275bf164bfdc)上发表的，经其许可，在此转载。如果你喜欢，为什么不去那里给他一些❤️？*

* * *

![The traditional media representation of a hacker](img/c39606c12b6357c8ba60df16ded1979a.png)

你是一名企业家、经理或营销专家，负责一家公司的网站，该网站只是一张介绍公司、公司服务和最新消息的电子名片。不包括任何国家机密，对吧..那他们为什么还在这么做？

![But then again.. once in a while you might encounter this kind of messages.](img/bb5cd8397852a587b93c1555db1a3728.png)

## 赚钱(很多钱)。

是的，即使是一个小的网站黑客也能产生大量的金钱。网络犯罪分子可以通过散布恶意软件、SEO 垃圾邮件，甚至建立电子邮件垃圾邮件服务器和钓鱼网站，利用您受损的网站赚钱。金钱显然是这些攻击背后最常见的动机。

### SEO 垃圾邮件

![Screenshot of a pharma-scam from an infected website.](img/1e7b9008182b24d943ba84e9215f96ff.png)
*来自受感染网站的制药骗局截图。*

这种垃圾邮件赚了很多钱。向合法网站注入反向链接和垃圾邮件仍然是最有利可图和最流行的网站攻击类型之一。

在网站被入侵后，一个恶意的后门将被上传到网站，这使得攻击者能够在任何时候将你的访问者无形地重定向到他们的诈骗网站。

除了为黑客赚钱，你的网站还会受到搜索引擎的惩罚，这会毁了你的搜索引擎优化。

> 这个骗局已经追溯到有组织的犯罪集团，据估计这个市场价值**4310 亿美元，并且还在增长。假药的规模及其对公众健康造成的危害促使[食品药品管理局、国际刑警组织等机构再次采取行动](https://www.interpol.int/Crime-areas/Pharmaceutical-crime/Operations/Operation-Pangea)。— [封装](https://www.incapsula.com/blog/viagra-spam-botnet.html)**

### 恶意软件

![Malware sample from a hacked website.](img/b5dcb2d4a2cc3ac670c81d2b2774ee09.png) *来自被黑网站的恶意软件样本。*

> 只要访问一个不安全的网站，您的操作系统、浏览器、插件和应用程序就有可能暴露在寻找漏洞的攻击之下。SophosLabs 每天都会看到成千上万个新的网址，其中包含了被下载的内容。— [Sophoslabs](https://www.sophos.com/en-us/security-news-trends/security-trends/money-behind-malware-threats.aspx)

是的，这是最糟糕的情况，但你的网站可以用来感染访问者勒索病毒。从 2014 年到 2016 年，超过 100，00 0 个 WordPress 和 Joomla！网站将访问者重定向到中微子漏洞工具包，该工具包试图渗透访问者计算机上的浏览器，当成功时，就会用 CryptXXX 勒索软件感染操作系统。

**同样是十亿美元的市场:**[http://www . NBC news . com/tech/security/ransomware-now-billion-dollar-year-crime-growing-n 704646](http://www.nbcnews.com/tech/security/ransomware-now-billion-dollar-year-crime-growing-n704646)

它也在增长:根据最新一期[互联网安全威胁报告](https://www.symantec.com/content/dam/symantec/docs/reports/istr-22-2017-en.pdf):

*   1077 美元= 2016 年人均货币需求量
*   294 美元= 2015 年人均货币需求量

利用恶意软件赚钱还有很多其他方式。例如，被黑客攻击的网站可以连接到一个大型僵尸网络，然后可以用来提供 DDoS 服务，以攻击其他网站和网络服务。

## 然后还有这些家伙…

![Angry penguin on a Russian website.](img/03ad31ba3e80c2a40224e01a3e816f3b.png) *俄罗斯某网站上的愤怒企鹅。*

破坏者、脚本小子、*丑化者*他们测试自己的技能，喜欢在黑客论坛上炫耀，并与最花哨的丑化进行竞争。幸运的是，这类攻击通常是最容易检测和修复的。

你可以在[镜像网站](https://www.zone-h.org)上找到被丑化的网站，在那里，丑化者会主动张贴他们的新受害者。

## 现实是这样的！你知道他们都有什么共同点吗？

![A robot waving its arms about](img/9e24b1ca3c0c320d9bba29c4f4d6dfe8.png)

**攻击是自动化的！**这是一个关键因素，因为对于攻击是如何执行的有一个常见的误解。

### 这里有一个你的网站如何被黑的例子:

1.  心怀不轨的黑客从按国家和特殊指纹制作目标列表开始(Google Dorking)。他可以使用(可用的自动化工具)谷歌找到捷克共和国的每个网站，默认的 WordPress 页面为“Hello World”，如下所示: **`site:.cz inurl:/hello-world/`** 。[自己去看](https://www.google.hr/search?q=site%3A.cz+inurl%3A%2Fhello-world%2F)。

2.  现在，有了超过 5000 个 WordPress 站点的列表，有很多可能性。他可以开始对特定易受攻击的软件进行指纹识别(自动化)，并尝试使用不同的组合(也是自动化的)来攻击管理员帐户。这一步他已经可以访问很多网站了(大部分网站更新不频繁，缺乏安全措施)。

3.  作为最后一步，这是所有关于感染和使用网站的攻击者希望(khm..也是自动化的)。

### 一切都是自动化的——攻击者可能在没有访问网站或亲眼看到网站的情况下就入侵了您的网站。

是的，你应该担心！请记住，与那些在黑暗角落被涂鸦和贴标签的废弃建筑类似，如果你没有基本的维护、安全措施和适当的监控，你的网站被污损和感染恶意软件只是时间问题。

## 好的，好的..但是问题有多大呢？

要找到答案，谁比谷歌更适合说网络上发生了什么:

**以下是 2017 年 3 月底谷歌在他们博客上发布的内容:**

> 与 2015 年相比，2016 年被黑网站的数量增加了约 32%。我们预计这一趋势不会放缓。— [谷歌](https://webmasters.googleblog.com/2017/03/nohacked-year-in-review.html)

既然你的网站几乎有 1/3 的几率是运行在 WordPress 上的，你应该已经知道 2017 [甚至没有以一个积极的语气](https://threatpost.com/1-5m-unpatched-wordpress-sites-hacked-following-vulnerability-disclosure/123691/)开始。[甚至 WiFi 路由器都能黑掉你的网站](https://www.bleepingcomputer.com/news/security/home-routers-used-to-hack-wordpress-sites/)。

我不会在这里进行大量的统计，但是如果你有一个 WordPress 站点，你可以从我之前的文章中得到一些提示。

你的网站是你公司在网络上的形象，保护好它！

喜欢这篇文章吗？通过分享和前往 [Medium original](https://medium.com/@oliversild/why-the-hell-would-anyone-hack-my-small-website-275bf164bfdc) 给它一些爱，帮助我们传播这种意识——那里甚至有一个免费的网站监测试用在等着你！

注意安全！

## 分享这篇文章