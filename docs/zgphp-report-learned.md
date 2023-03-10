# ZgPHP 报告–我们学到了什么

> 原文：<https://www.sitepoint.com/zgphp-report-learned/>

上周是克罗地亚萨格勒布的网络夏令营。这是一个致力于网络技术的免费参加的大型会议。正如我们不久前在[的 zphp 帖子](https://www.sitepoint.com/zgphp-conference-2014-free-entry/)中宣布的那样，zphp 是其中的一部分。为期一天的面向 PHP 的分会议在另一个稍小的地方举行。

![](img/dde2bc6d422f830aca01805071290d15.png)

## 开始

我很早就到了那里，想和大家打成一片。在喝了一些黑咖啡并向罗斯·塔克请教之后，我们被迎进了演讲厅。由于看不到任何技术上的困难，时间表也得到充分遵守，会议宣布开幕。

> 介绍完毕，开始 [#zgphp](https://twitter.com/hashtag/zgphp?src=hash) confs 第一讲！pic.twitter.com/3b1vBmcd1B
> 
> —zgphp(@ zgphp)[2014 年 10 月 2 日](https://twitter.com/zgphp/status/517601152368914432)

## 会谈

讲座内容非常广泛，从理论和经验总结到实际的代码实现和设计模式。

### 超高速开发，24 小时内启动

来自[德戈尔迪安](http://www.degordian.com/)的托尼·姆拉科维奇第一个登台。他重点讲述了让一个即兴团队在 24 小时内从零开始尝试建立一家初创公司的积极和消极经历，包括名称、域名、设计、SEO 和其他元内容，以及开发。奇怪的是，他们选择了过时的 Yii 作为底层框架。

> Degordian 的主角[#开发者](https://twitter.com/hashtag/developer?src=hash) [@jojothebandit](https://twitter.com/jojothebandit) 在 [@zgphp](https://twitter.com/zgphp) 受到关注。:)[pic.twitter.com/MNIoTUFxDg](http://t.co/MNIoTUFxDg)
> 
> —德戈尔迪安(@德戈尔迪安)[2014 年 10 月 2 日](https://twitter.com/degordian/status/517603706721017856)

他谈到了混乱、疲劳、版本控制问题(在一个相当规模的团队中，似乎超快速的开发意味着很多人有时在同一个文件上工作)，前端和后端人力之间的不平衡以及这种不平衡造成的差异，等等。他用一个简短的艺术氛围视频结束了这一切，这个视频是在这 24 小时内拍摄的。

[//player.vimeo.com/video/79022510?color=ffffff](//player.vimeo.com/video/79022510?color=ffffff)

[HIVEPEEK 在](https://vimeo.com/79022510)[德戈尔迪安](https://vimeo.com/degordian)的电影后于 [Vimeo](https://vimeo.com) 上映。

考虑到这一切都是在一天之内完成的，最终产品 [HivePeek](http://hivepeek.com/) 相当不错，但整个努力仍然不能称为产品的成功——尽管它看起来确实是团队的成功:他们意识到这不仅是团队建设的好方法，也是观察人们在压力下表现如何的好方法。

### HHVM 和哈克:这些可能是我们要找的机器人吗？

这是一个非常有趣的话题，我们已经在 SitePoint 上进行了相当程度的探索，发布了类似[这些](https://www.sitepoint.com/hhvm-and-wordpress/)和[这些](https://www.sitepoint.com/premium/books/hacking-the-hacker/read/1)的帖子，我急切地等待着[尼古拉·普莱吉奇](http://twitter.com/nikolaplejic)的讲话，希望我能对我们尚未涉及的问题有更多的见解。我没有失望——虽然演讲的主旨很简单，但在简单回顾了 HHVM 的历史之后，Nikola 确实有一些有趣的深度。我们看到了使用 HHVM 时从 PHP 函数生成的 HH 字节代码，介绍了黑客操作模式和它的高级功能(如类型别名)的一些用例，展示了它如何正确工作，等等。

> pic.twitter.com/yY09iIvpt1[@尼古拉普利奇](https://twitter.com/nikolaplejic) [#zgphp](https://twitter.com/hashtag/zgphp?src=hash) [的 HHVM 与黑客对话时间](http://t.co/yY09iIvpt1)
> 
> —zgphp(@ zgphp)[2014 年 10 月 2 日](https://twitter.com/zgphp/status/517617321691856896)

Nikola 通过链接到他的的一个存储库来结束演讲，这个存储库包含

> …一个非常简单的 Docker 容器，可以用来玩脸书的 HHVM / Hack。

他演讲的幻灯片可以在这里找到。

### 让我们做活动

来自 [Trikoder](http://www.trikoder.hr/) 的 Robert sorn 向观众介绍了事件驱动编程，用实际代码和图表展示了他的公司建立的一个网店的一切。我从未在 PHP 中做过任何事件驱动的编程，不是真正意义上的，所以从他的经验和视角中学习是一种乐趣。

> 让我们做事件[# zgphp](https://twitter.com/hashtag/zgphp?src=hash)[# web campzg](https://twitter.com/hashtag/webcampZG?src=hash)[pic.twitter.com/z3G8wlUROa](http://t.co/z3G8wlUROa)
> 
> — Ivan Zvonimir Horvat (@ivanhorvat82) [October 2, 2014](https://twitter.com/ivanhorvat82/status/517634819376046080)

不幸的是，他的幻灯片目前还不可用(*编辑:他的幻灯片现在在这里*)，但我们确实了解到，如果你的目标是应用程序中的事件，Symfony 的 [EventDispatcher](http://symfony.com/doc/current/components/event_dispatcher/introduction.html) 是物有所值的。

部分跑题了，如果你想看一个有趣的事件驱动框架，现在刚刚取得成果，检查一下 [Webiny 框架](https://github.com/Webiny/framework)，即将到来的 CMS 的基础，我们很快会有更多的说。

### 用新技术赚钱

在这个简短的演讲中，我描述了 SitePoint 的工作流程，并呼吁人们以读者或作者的身份加入我们。我解释了作为一名作者可以获得的各种额外津贴，并试图在谈话中加入一些社区数据。在这里重复它没有太多意义 SitePoint 的普通读者会熟悉我们的双月[行动号召](https://www.sitepoint.com/welcoming-new-authors-july-august-2014/)，并且会知道 [IDE 调查结果](https://www.sitepoint.com/best-php-ide-2014-survey-results/)提供了一些有趣的社区见解。

> 写科技文章并由 pic.twitter.com/xDstGeP9gg[@比特福尔斯](https://twitter.com/bitfalls)[# zphp](https://twitter.com/hashtag/zgphp?src=hash)[支付报酬](http://t.co/xDstGeP9gg)
> 
> —zgphp(@ zgphp)[2014 年 10 月 2 日](https://twitter.com/zgphp/status/517645413915316225)

如果你感兴趣，这些幻灯片在这里。

### Symfony2 + AngularJS

来自 PHPSummerCamp 的在一个有点老套的演讲中， [Locastic](http://www.locastic.com/) 的[Antonio peri](https://twitter.com/antonioperic)描述了构建一个 web 应用程序的过程。他们没有完全模仿 Symfony 应用程序，而是选择了一种混合方式，既没有取消 Twig 引擎，也没有刷新整个页面来加载新数据。

> SF2+Angular[# zgphp](https://twitter.com/hashtag/zgphp?src=hash)[# web campzg](https://twitter.com/hashtag/webcampZG?src=hash)[pic.twitter.com/Cn0zR8WxrN](http://t.co/Cn0zR8WxrN)
> 
> — Ivan Zvonimir Horvat (@ivanhorvat82) [October 2, 2014](https://twitter.com/ivanhorvat82/status/517670169595113472)

我个人觉得这是一种有趣的方法，尽管我从来不会采用它——在我看来，SPA 应该将前端与后端完全分离，并通过 API 进行通信，或者将两者绑定为一个可维护的整体。我必须更深入地了解代码，但现在我对这个概念不感兴趣。虽然我喜欢 Symfony 和 Angular，但我不喜欢他们的孩子。

### 用 Gearman 换挡

sran Vranac 通过 Gearman 与我们交流，这个话题我们[在](https://www.sitepoint.com/introduction-gearman-multi-tasking-php/)之前已经谈过了，尽管没有明显的深度。在每张幻灯片后，如果没有坚定的论点或幽默的讽刺，他令人生畏的舞台表现就什么都不是，他很好地证明了这一点。

> code4hire“开发者”[# zgphp](https://twitter.com/hashtag/zgphp?src=hash)[# web campzg](https://twitter.com/hashtag/webcampZG?src=hash)【pic.twitter.com/A74EL2w2Jo】T4
> 
> — Ivan Zvonimir Horvat (@ivanhorvat82)

在思考上限定理、订单可靠性和工作队列的间隙，他还告诉我们，Gearman 是经理的变位词，它做大致相同的工作:授权，仅此而已。

他的幻灯片可以在网上看到。

### ESI 和 HTTP 缓存对高性能页面交付的强大功能

[Netgen](https://twitter.com/ilukac)的 Ivo luka 和 [PHPSummerCamp](https://www.sitepoint.com/phpsummercamp-review/) fame 进来谈论 eZ Publish 及其对 Symfony 的 [HTTPCache](http://symfony.com/doc/current/book/http_cache.html) 和 [ESI](http://symfony.com/doc/current/book/http_cache.html#edge-side-includes) 的实现，以减少使整个缓存页面无效的开销。在即将到来的(也是目前最新的)版本中，eZ 中的缓存达到了一个新的水平。鉴于它是基于 Symfony 组件的，如果你通读了前面提到的链接，你会更好地理解这些概念。

> 科菲结束了，最后一轮与边缘方的会谈还在继续，包括由 pic.twitter.com/UUI652gvoL[@伊卢卡奇](https://twitter.com/ilukac)[# zphp](https://twitter.com/hashtag/zgphp?src=hash)[主持的](http://t.co/UUI652gvoL)
> 
> —zgphp(@ zgphp)[2014 年 10 月 2 日](https://twitter.com/zgphp/status/517691587296788480)

如果你是一个普通读者，你可能还记得他关于[为什么你应该为你的下一个基于 CMS 的项目](https://www.sitepoint.com/9-reasons-consider-ez-publish-cms-next-web-project/)选择 eZ 的帖子。

### HTTP &你的愤怒的狗

最后但同样重要的是，罗斯·塔克成名乐队的罗斯·塔克登上了舞台。除了是一个非常有趣的人之外，罗斯似乎也知道一些关于 etags 或类似的东西…

> Http 和 tour 怒狗 by[@ Ross tuck](https://twitter.com/rosstuck)[# zgphp](https://twitter.com/hashtag/zgphp?src=hash)[pic.twitter.com/28GahXyQPl](http://t.co/28GahXyQPl)
> 
> —Vranac SRD Jan(@ Vranac)[2014 年 10 月 2 日](https://twitter.com/vranac/status/517700475698032640)

玩笑归玩笑，他对 HTTP 头世界的介绍确实具有启发性。我从来没有认真考虑过它们，但是当 Ross 向我们展示一些我们通常认为理所当然的标签的用例时，我注意到了大量使用它们的潜在好处，特别是在缓存方面。他谈到了在 API 端点中包含文件扩展名和查询参数支持的负面影响，如何避免 Accept 头和内容类型的诱惑，如何告诉服务器特定内容类型的首选级别，Vary 头和其他魔法。

由于这是他最受欢迎的演讲之一，幻灯片可在此处获得。

## 总结

随着抽奖活动在最后向随机的观众成员分发 PhpStorm 许可证和 Github 通行证，为期一天的 ZgPHP 会议接近尾声。成为这个由开发人员和爱好者组成的紧密结合的社区的一部分是有趣和令人耳目一新的，特别是在下班后，当地的一家啤酒厂带来了更有趣的讨论，我期待着明年加入他们的整个网络营活动。

## 分享这篇文章