# SitePoint 时事通讯列表的更新

> 原文：<https://www.sitepoint.com/an-update-on-the-sitepoint-newsletter-lists/>

你们中的一些人可能在几周前听说过 Campaign Monitor，SitePoint 用来发送其受欢迎的电子邮件简讯的电子邮件营销服务，被黑客攻击，几个账户被侵入。

不幸的是，其中一个账户是 SitePoint 的。如果你是我们时事通讯的订阅者，这篇文章试图回答你可能有的一些问题。

## 什么是活动监视器？

[Campaign Monitor](http://campaignmonitor.com/) 是一个第三方电子邮件营销服务，我们用它来发送我们的电子邮件简讯——[科技时报](https://www.sitepoint.com/the-second-coming-of-the-productivity-tool/)、[论坛报](https://www.sitepoint.com/the-second-coming-of-the-productivity-tool/)、[设计视图](https://www.sitepoint.com/the-second-coming-of-the-productivity-tool/)和[社区宣传员](https://www.sitepoint.com/the-second-coming-of-the-productivity-tool/)。我们在 Campaign Monitor 服务上托管这些简讯的所有电子邮件列表，任何时候有人请求从简讯中添加或删除，我们都会利用 Campaign Monitor API 自动更新这些列表。

## 发生了什么事？

Campaign Monitor 服务器遭到了一个或多个经验丰富的黑客精心策划和组织的攻击，包括我们在内的几个账户遭到了破坏。

在袭击发生后不久，竞选监督团队在让他们的客户了解情况方面做得非常出色。如果你对直接来自马嘴的详细报道感兴趣，你可以阅读竞选监督博客上的相关帖子:

*   [竞选监控被黑客攻击，部分账号被攻破](http://www.campaignmonitor.com/blog/post/2852/campaign-monitor-attacked-by-hackers-some-accounts-compromised/)
*   [黑客问题更新](http://www.campaignmonitor.com/blog/post/2853/update-on-the-hacking-issue/)
*   [安全更新，非常感谢](http://www.campaignmonitor.com/blog/post/2855/security-update-and-a-big-thanks/)
*   [从今天开始需要更改账户密码](http://www.campaignmonitor.com/blog/post/2854/required-password-changes-starting-today/)

可能是因为我们的时事通讯有如此多的订阅者，我们的时事通讯订阅者名单被黑客特别锁定。

根据攻击发生后立即与 Campaign Monitor 团队进行的讨论，以及他们在过去几周内委托进行的安全审计，我们的理解是，黑客打算利用 Campaign Monitor 良好的发送记录向 SitePoint 的时事通讯订户发送垃圾邮件。幸运的是，竞选监督人员似乎已经发现了这个安全漏洞，并在邮件发出之前阻止了它。

据我们所知，黑客至少执行了两项影响 SitePoint 用户的操作:

1.  黑客导入了他们自己的电子邮件地址列表，准备从 Campaign Monitor 上的 SitePoint 帐户发送垃圾邮件。幸运的是，入侵在此活动发送之前就被检测到并阻止了。
2.  黑客们在亚历克斯的[设计视图](https://www.sitepoint.com/the-second-coming-of-the-productivity-tool)时事通讯的订阅者列表中添加了一些电子邮件地址。不幸的是，这些地址被添加到这个时事通讯列表中的事实直到几个星期后才被发现——在下一期的设计视图被发送之后。结果，最近一期的*设计视图*很遗憾地被发送到了一些没有合法订阅的电子邮件地址。这份名单已经被竞选监督小组清理过了，所以它再次只包含那些专门注册接收这份时事通讯的订阅者。

## 我的电子邮件地址现在在垃圾邮件发送者手中吗？

我们不知道黑客是否导出了 Campaign Monitor 上的任何列表。不幸的是，黑客完全有可能确实导出了 SitePoint 的部分或全部时事通讯邮件列表，目的是用它们来发送垃圾邮件。我们已经收到了几份垃圾邮件被发送到专门为这些简讯创建的地址的报告，但我们可以告诉你这些是一次性的报告，似乎不是一个持续的问题。也就是说，如果您确实认为自上个月起，您用来订阅我们的时事通讯的电子邮件地址收到了越来越多的垃圾邮件，请[联系我们。](https://www.sitepoint.com/contact-us)

## 你的其他名单呢？

我们的时事通讯电子邮件列表是我们使用 Campaign Monitor 托管的唯一列表。我们的所有其他客户和订户信息，包括我们的图书俱乐部、论坛会员、客户数据库(包括订单历史等)以及我们向其发送图书公告和其他选择加入电子邮件营销通信的所有其他电子邮件列表，都存储在其他地方。这些列表都没有因此次攻击而暴露的风险。

## 采取了什么措施来防止这种情况再次发生？

这显然是一个可怕的消息，不得不向那些信任我们的读者报告他们的电子邮件地址。然而，根据竞选监督团队为防止此类事件再次发生而采取的措施，我相信您会同意竞选监督团队处理这种情况的方式堪称典范。我们也相信采取类似的透明政策——因此有了这篇博文。就我们未来的列表而言，我们完全相信团队有能力加强电子邮件营销服务的安全性，我们计划继续使用活动监控服务来发送您的简讯。

报道这样的新闻总是很困难，但我们希望通过阅读这篇文章以及竞选监督人员为解决这一不幸事件所做的详细工作，您会支持这一决定，并继续订阅您最喜欢的 SitePoint 时事通讯。

如果您对 SitePoint 关于访问者个人信息的安全性和隐私的政策有任何疑问，您可以阅读我们的[隐私政策](https://www.sitepoint.com/privacy/)，在评论中留下问题，或者[联系我们的客户支持团队](https://www.sitepoint.com/contact-us)。

## 分享这篇文章