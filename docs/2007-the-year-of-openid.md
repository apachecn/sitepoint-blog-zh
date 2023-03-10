# 2007 年:OpenID(？)

> 原文：<https://www.sitepoint.com/2007-the-year-of-openid/>

一个迟到但快乐的新年——最近的沉默与在 [local.ch](http://local.ch) 开始新工作有关——下次再谈。

因此，让我们称这一年为 [OpenID](http://en.wikipedia.org/wiki/OpenID) 之年——或者至少是截止到三月份的那一年。OpenID 作为对 T4 一些(并非全部)在线认证问题的解决方案，开始获得 T2 的关注。

虽然一些好的资源开始出现解释 OpenID 如何工作，但有一种趋势是“这是如何添加到你的博客中——不要问困难的问题”或重要的[假设知识](http://openid.net/specs.bml)。因此，如果你身边有人可以直接解释它就好了，这就是上个[网络星期二](http://webtuesday.ch/meetings/20070109)，[塞德里克的](http://www.keepthebyte.ch/)提供了 [OpenID 概述](http://www.keepthebyte.ch/2007/01/openid-speech-at-webtuesday-zurich.html)所发生的事情。除了很好地解释了 OpenID *是*和[不是](http://simonwillison.net/2007/Jan/10/account/)的关键点之外，他还很好地尝试了从相关主机之间的通信角度来说明 OpenID 事务，这似乎是目前网上记录得很少的东西——图片[在这里](http://www.flickr.com/photos/keepthebyte/351494842/)。

演讲中提到的一个特别的关注点是关于 OpenID 交易的最终重定向，当在依赖方(您想要注册和登录的站点)上设置一个新帐户时——这里是步骤 6。据我所知，OpenID 服务器可以选择性地向依赖方提供您的个人信息，以减少您填写注册表的工作。但是这似乎是通过你被重定向到的最终 GET 请求传递的，GET 有[限制](http://support.microsoft.com/kb/208427)并且倾向于存在于访问日志中…

演讲幻灯片在线[这里](http://keepthebyte.ch/openid/openid-authentication-webtuesday01-2007.pdf) (PDF)。也可能[很快会以视频](http://www.flickr.com/photos/cocaman/352573134/)的形式出现在某个地方…

与此同时，Dokuwiki 似乎也获得了 OpenID 支持

**更新:**–现在在谷歌视频上谈论–非常感谢 Corsin 的辛勤工作。

## 分享这篇文章