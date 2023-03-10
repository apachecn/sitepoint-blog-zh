# 本周我们关注的:节点、流星和网站表现

> 原文：<https://www.sitepoint.com/radar-week-node-meteor-website-performance/>

欢迎来到《在我们的雷达上》,这是一个来自 web 开发世界的新闻、趋势和其他酷东西的每周综述。

本周头条新闻是，在瑞典警方突袭后，海盗湾已经下线。上次发生在[的事情](http://www.wired.com/2011/05/0531swedish-police-raid-pirate-bay/)很多人都很愤怒，随之而来的是很多抗议。然而这一次，[似乎没有人真的在乎](http://blog.brokep.com/2014/12/09/the-pirate-bay-down-forever/)，即使有报道称[他们可能会永远下线](http://www.extremetech.com/gaming/195647-the-pirate-bay-raided-shut-down-by-swedish-police-and-it-may-never-return)。

继续从事非法活动，索尼黑客事件的余波仍在继续，各种各样的东西被泄露到互联网上。这包括但不限于:[新片计划](http://www.theverge.com/2014/12/10/7368369/sony-is-planning-a-crossover-movie-with-men-in-black-and-jump-street)、[名人电话号码](http://www.geek.com/apps/sony-pictures-hackers-leak-scripts-celebrity-phone-numbers-and-aliases-1611258/)、[名人化名](http://www.avclub.com/article/sony-hackers-reveal-silly-fake-names-celebrities-u-212800)、[令人尴尬的公司邮件](http://defamer.gawker.com/leaked-the-nightmare-email-drama-behind-sonys-steve-jo-1668882936)、[高管薪水](http://www.bloomberg.com/news/2014-12-10/sony-hackers-expose-salaries-e-mail-as-no-end-to-leaks-in-sight.html)。索尼也受到了安全公司的尖锐批评，因为它让这次攻击发生了，这次攻击看起来可能是朝鲜发动的。

最后宣布[购买比特币在美国不需要缴纳销售税](http://pando.com/2014/12/09/ny-clarifies-bitcoin-taxation-affirming-intangible-property-classification-and-likening-bitcoin-transactions-to-bartering/)，这对一家公司来说是个好消息，[今年早些时候成功竞拍了从丝绸之路缴获的 48000 枚比特币](http://dealbook.nytimes.com/2014/12/09/secondmarket-nearly-sweeps-latest-bitcoin-auction/)。[比特币黑色星期五被誉为成功](http://blog.bitpay.com/2014/12/09/bitcoin-black-friday-2014-recap.html)，微软宣布[他们现在接受比特币](http://www.theverge.com/2014/12/11/7375771/microsoft-supports-bitcoin-payments)，面对越来越多的采用，我们被提醒[使用加密货币](http://www.cnbc.com/id/102253282)有非常切实的好处。额外链接:这里有一张信息图，突出了比特币商业的好、坏和未来。信息图表万岁！

## Node.js 是完全分叉的

![iojs Logo](img/13bdefa3e89e85e1bc0dc58c1561d8f6.png)

本周来自 Node.js land 的重大消息是,[该项目已经被一个由 Node 的一些主要开发者领导的小组分了手。分叉被称为](http://www.wired.com/2014/12/io-js/) [iojs](https://github.com/iojs/) ，发生在该组织对 Node 的官方赞助商云计算公司 Joyent 的管理不满的时候。

人们对这种分化的反应不一，一些人声称 Node 的未来存在严重问题，而另一些人则敦促人们保持冷静，从大局着眼。

npm (Node 的软件包管理器)发布了一系列视频，详细介绍了如何使用 npm，如何安装和管理软件包，修复权限等等。

这里有一篇关于如何用 Node.js 和 Restify 设计 RESTful API 的有见地的文章。

流是 Node 更强大的特性之一。下面是对它们是什么以及如何使用它们的介绍。

如果所有这些关于 Node 的讨论让你很想尝试一下，那么这里有一个(非常)深入的指南，指导你如何开始使用服务器端框架，还有一个由同一个人列出的[节点开发者犯的十大错误](https://www.airpair.com/node.js/posts/top-10-mistakes-node-developers-make)。

## 让我带你去看星星

Meteor(用纯 JavaScript 构建现代 web 应用的开源平台)最近发布了里程碑式的 1.0 版本。SitePoint 的作者希亚拉·伯克特已经对它进行了测试，他想向你展示一下[带你离开这个世界的九种方式](https://www.sitepoint.com/9-ways-meteor-1-0-will-take-world/)。我猜她被打动了，然后…

一位流星开发者决定搜集所有他能在网上找到的流星应用程序，并提取一些统计数据。他的发现是有趣的读物。

在前一组链接的提示中，[meteor 播客分析了 Node.js](http://www.meteorpodcast.com/e/episode-42-december-5th-2014/) 的分叉以及这对 meteor 社区意味着什么(可以说 Meteor 之于 Node 就像 Rails 之于 Ruby)。

如果你想给你的流星应用添加评论，[下面是你如何做的](http://joshowens.me/adding-comments-to-your-meteor-js-app/)。

这里有一个很好的[列表，列出了在编写流星应用](https://dweldon.silvrback.com/common-mistakes)时要避免的陷阱，以及[一个方便的提示和变通方法列表](https://medium.com/@Dominus/meteor-tips-and-workarounds-b791151ce870)。

和一些帮助你度过周五的东西:[一个用《流星》写的微型鼓机](http://loopy.meteor.com/)(我发现它令人惊讶地上瘾)。

## 本周流行语:页面膨胀

![Page size graph depicting years 2010 (702kb), 2012 (1024k) and 2015 (2344kb)](img/6ee2ef9f0705e60ff509d25c3331e810.png)

在过去的十五年中，平均网页的大小已经膨胀到超过一兆字节。这种现象被称为“页面膨胀”，主要是由网站所有者随意包含图像和第三方脚本引起的。页面膨胀尤其伤害移动用户和网速慢的用户。

对抗页面膨胀的一个方法是[为你的网站](http://danielmall.com/articles/how-to-make-a-performance-budget/)建立一个性能预算。做起来并不复杂，事实上，[绩效规划可以带来各种好处](http://alistapart.com/article/planning-for-performance)。

Planet Performance 在圣诞节前夕每天发布[一篇文章](http://calendar.perfplanet.com/2014/),详细说明如何提高网站的性能。这篇关于优化图像的[文章](http://calendar.perfplanet.com/2014/images-are-king-an-image-optimization-checklist-for-everyone-in-your-organization/)是我迄今为止最喜欢的。

文章中提到的技术之一是 spriting(在一幅大图中将多幅图像组合成一个直线网格)。下面是如何用 Sass 和 Compass 实现 CSS 精灵的[。](https://www.sitepoint.com/css-sprites-sass-compass/)

如果 JavaScript 更适合你，你可能想看看下面的[快速 JavaScript 变化，你今天可以做出更好的 web 性能](http://www.yottaa.com/blog/quick-javascript-changes-you-can-make-today-for-better-web-performance)。

最后，这里有一篇写得很好的文章，提供了关于如何优化你的网站速度的进一步建议。

## CMS 优度

WordPress 4.1 就要发布了(发布日期目前是 12 月 16 日)，似乎有很多令人兴奋的事情。[这篇关于网页设计师仓库](http://www.webdesignerdepot.com/2014/12/whats-new-and-exciting-in-wordpress-4-1/)的文章告诉你真相。

我相信你们都听说过[谷歌的 reCAPTCHA](https://www.google.com/recaptcha/intro/index.html) ，现在[涌现出几个插件来帮助你将它添加到你的 WP 网站](http://wptavern.com/new-plugin-adds-googles-no-captcha-recaptcha-to-wordpress-login-comment-and-registration-forms)中，只需最少的麻烦和配置。

当我们谈论垃圾邮件发送者时，他们使用的技巧之一是用一次性电子邮件地址注册帐户。SitePoint 的作者 Agbonghama Collins 将带您看看如何阻止这种情况。

WordPress 4.1 带来了一个新的默认主题:2015。如果这不适合你，这里有一篇关于[选择下一个主题](http://www.smashingmagazine.com/2014/12/04/what-to-consider-when-choosing-a-wordpress-theme/)时还需要考虑什么的文章。

WordPress 无处不在(或者看起来如此)。下面[来看看需要什么才能废黜 CMSs 之王](http://www.cmscritic.com/what-it-will-take-to-dethrone-wordpress/)(剧透:很多)。

WordPress 的一个弱点是性能。这就是为什么越来越多的人转向静态博客生成器。下面来看看六个不是 Jekyll 的静态博客生成器。

最后， [Storytime 是 Rails 4+ CMS 和博客引擎](https://github.com/FlyoverWorks/storytime/)。安装它很简单，只需将`gem "storytime"`添加到您的 gem 文件中(嗯，差不多……)

* * *

这就是这周的全部内容。感谢加入我们。

我要告诉你一个消息，随着他的第三首重金属圣诞单曲的发布，著名演员克里斯托弗·李(曾在《指环王》中扮演萨鲁曼)将“摇滚之神”加入他的简历。在其他地方，巴拉克·欧巴马已经成为[第一位编写计算机程序的美国总统](http://qz.com/308904/heres-the-first-line-of-code-ever-written-by-a-us-president/)，并且有一个严酷的事实被揭露出来[猎户座飞船并不比你的手机](http://www.computerworld.com/article/2855604/the-orion-spacecraft-is-no-smarter-than-your-phone.html)更智能。

此外，我们 SitePoint 总部仁慈的主人最近询问了一群 SitePoint 作者他们想要什么样的开发者玩具作为圣诞礼物。然后他们设法找到他们——不依靠圣诞老人。作者要求的每个项目都可以通过各自帖子中的最佳评论获得。这是我向 T1 要求的事情之一。你可以在主页上看到其他作者想要什么。

那么哪些链接引起了你的注意呢？你对 Node land 发生的事情有什么看法？你担心页面膨胀吗？你有使用 Storytime 的经验吗？不管怎样，我们都想听听你的想法。

## 分享这篇文章