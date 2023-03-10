# Source hunt——开源周版

> 原文：<https://www.sitepoint.com/sourcehunt-open-source-week-edition/>

![Open Source Week](img/31a22f613e5e7d0c22f753b2e51b5549.png)

SitePoint 的开源周到了！整整一周我们都在发布关于开源、自由软件和社区的文章，所以请继续查看 ***[OSW 标签](https://www.sitepoint.com/blog/)*** 获取最新更新。

* * *

本周是 SitePoint.com 的开源周。虽然我们通常在这里讨论开放源码，但是对于令人惊奇的新库、包和框架的所有教程和介绍，本周将有一点“理论上”的改变。

我们将从总体上讨论开源，思考它如何影响我们的编程语言的前景，我们可以做些什么来帮助它传播，涵盖一些有趣的开源项目，等等。一定要看看其他渠道——我们在整个网站上用[这个标签](https://www.sitepoint.com/blog/)传播开源周，在 Twitter 上用[#开源周](https://twitter.com/hashtag/opensourceweek)标签传播开源周。

现在，让我们看看一些急需贡献者和 Github 明星的开源项目。

![Sourcehunt logo](img/407bcf1bded70a8c020759619779150b.png)

* * *

### [paragonie/gpgmailer](https://github.com/paragonie/gpg-mailer)[33★]

GPGMailer 是一个软件包，可以让你发送 GPG 加密的电子邮件(使用 zend-mail 和 Crypt_GPG)。

电子邮件安全通常是事后才想到的，或者根本就没有想过——所以看到一个专注于优先发送安全电子邮件的包是一种新鲜空气。如果你正在构建自己的电子邮件客户端，或者你正在做一些类似于[从你的收件箱中过滤招聘人员的垃圾邮件并自动回复他们](https://www.sitepoint.com/fighting-recruiter-spam-with-php-proof-of-concept/)的事情，GPGMailer 可以派上用场。

在 33 stars，该项目可以使用一些爱——无论是在测试中，还是在解决一个棘手的问题冻结特拉维斯管道。看看吧！

* * *

### [皇家财务报表/html 文件](https://github.com/royallthefourth/html-document)【3】

曾经尝试过用 PHP 制作一个有效的 HTML 文档吗？它通常需要将 HTML 写成一个字符串并回显它，这虽然有效，但不是很有效——而且众所周知很难自动化。输入`html-document`。

> 是一个面向对象的工具，用于构建 HTML 文档

就这么简单——一个用于构建 HTML 文档的流畅的面向对象接口。这个项目非常年轻，所以请投入进去探索它——看看它能做什么和不能做什么，它做对了什么，做错了什么。我们也很想看到关于这个话题的教程，所以如果你愿意的话，给我们一个欢呼吧！

* * *

### [algb12/GraphDS](https://github.com/algb12/GraphDS) [5 ★]

用作者自己的话说，GraphDS 是“PHP 中图形数据结构的快速实现”。

该项目刚刚开始，但它很快就投入了运行——作者最初开发它是为了个人使用，在意识到它比 PHP 在图形方面更直观后，他决定将它变成一个可扩展的开源包。

如果你曾经需要 PHP 中的图形，这是一个可以查阅的库——如果你已经有了在特性组合中添加什么的想法，现在你的机会来了，因为这个项目还处于初级阶段！

* * *

### [剧本融合/波特](https://github.com/ScriptFUSION/Porter)【145】

porter 是一个数据导入器抽象库，因为它可以很容易地将任何类型的数据导入到任何类型的系统中，只要您告诉 Porter 有关数据的信息。例如，这是为各种各样的 CMSes 开发定制导入程序的天赐良机；有没有想过，当一些企业集团购买了介质，所有人突然都必须将数据转移到自托管方法时，会发生什么？

自述文件比我在这一小段文字中给出的介绍要全面得多，所以我鼓励你看一看。

Porter 已经获得了一些关注(在撰写本文时已经获得了 145 颗星)，它需要更多的帮助来解决它所面临的一些问题，而不是接受测试，所以如果您需要增加您的贡献，就开始吧！

* * *

### tomphp/php-context-logger

这是一个新生的项目，它允许你在 PSR 7 应用程序中使用中间件将上下文添加到你的日志中。具体地说，这意味着你可以“建立”你的日志的上下文。如果你不熟悉日志中的上下文，这个[问题](https://github.com/Seldaek/monolog/issues/249)相当简洁地解释了它，还提到了使用它的“最佳方式”。

简而言之，上下文是与您记录的消息相关的一组数据。因此，记录的消息可能是“用户登录”，而它的上下文可能是`['user_id' => 123]`。这个包使添加的上下文“可包装”——参见 README 中的用法示例。

这个包非常年轻，它肯定需要一些帮助，不仅仅是在特性和问题上(没有太多帮助)，还有文档，使用示例，以及这个包在真实场景中的应用。你能胜任这项任务吗？

* * *

### [sidkshatriya/dontbug](https://github.com/sidkshatriya/dontbug)【532】

一个用 Go+C 驱动的 PHP 调试器——除了让你一步一步地调试 PHP 脚本的执行外，它还让你调试过去——沿着执行链向后。可以把它想象成在过去设置断点，并且能够在脚本状态中倒带和快进。

虽然对于只使用 PHP 的开发人员来说，要做到这一点可能有点困难，但它通常会附带一些 C 语言，这是一个非常好的工具，可以用来为列表中的其他项目做贡献，或者只是作为新的事实上的调试器。

有一些问题可能是可以解决的——处理 docs 和 Docker——但是总的来说，代码已经相当成熟了，而且明星效应已经出现了。

* * *

### ingowalther/image-minify-API[14★]

Image-minify-api 是一个 api 应用程序，可以安装在您自己的服务器上，然后用作图像缩小服务。在某种程度上，它像是 [Glide](https://www.sitepoint.com/easy-dynamic-on-demand-image-resizing-with-glide/) 的一部分，但处于自我维持的应用程序模式。

由于该项目缺乏人气，它还没有在现实生活场景中彻底测试过，所以它肯定会在这方面得到帮助。此外，它的一个问题(实施配额的请求)已经开发了一段时间，因此他们可能也需要帮助。

除了用一个 Github 明星或一两个问题评论来帮助他们，为什么不顺便赚点钱，为我们写一个关于这个应用的教程呢？这将有额外的好处，为他们提供急需的曝光率！

* * *

### [hawbit PHP/hawbit](https://github.com/HawkBitPhp/hawkbit)【37】

还记得[质子](https://github.com/alexbilbie/Proton)吗？它是现在看起来被放弃的框架项目，使用了[联盟](http://thephpleague.com/)组件，非官方的“联盟框架”。hawkbit——以前被称为 Turbine——是 Proton 的扩展，它被积极地维护，并在混合中添加了一两个组件，以使框架更加“完整”,例如用于配置的 [zend-config](https://docs.zendframework.com/zend-config/) 和用于错误处理的[ouws](https://github.com/filp/whoops),同时仍然保持“微”。

在 PHP 框架似乎永无止境的洪流中，很难说为什么有人会选择 Hawkbit，但也很难反驳它——它不仅使用了一些经过实战检验和专业开发的组件，而且它的组件也得到 PHP 世界中过去或现在的一些知名人士的认可。由于零问题和零 PRs，不清楚一个人能贡献什么，但我们 SitePoint 的 PHP 频道总是对新教程开放。感兴趣吗？

* * *

这就是我们这个月要与你分享的所有项目——它们中有任何一个吸引了你的注意力吗？有什么要分享的吗？请把你的带有 [#sourcehunt](https://twitter.com/search?q=#sourcehunt) 标签的链接扔给我们！

## 结论

开源已经存在。它每天都在改变我们的生活，而大多数人都没有意识到这一点，我们有责任帮助它进一步发展、壮大、强大。当软件开放时，我们有责任让它更好、更安全、更经得起考验——而不是作者。我们都从中受益，我们都以此建立自己的事业和生活。请务必在整个网站上用[这个标签](https://www.sitepoint.com/blog/)查看开源周的剩余部分，并在 Twitter 上用 [#OpenSourceWeek](https://twitter.com/hashtag/opensourceweek) 标签查看。

## 分享这篇文章