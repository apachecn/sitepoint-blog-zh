# source hunt # 4——反射、授权、Crons 等等

> 原文：<https://www.sitepoint.com/sourcehunt-4-reflection-authorization-crons-and-more/>

我们跳过了一月份的 [Sourcehunt](https://www.sitepoint.com/sourcehunt-hacktoberfest-2015-edition-hack-to-glory) ，但是我们现在回来了，准备好提升更多项目的明星地位！

![Sourcehunt logo](img/407bcf1bded70a8c020759619779150b.png)

## 多月经理 [125 ★]

multi 1 mate/cron-manager 是一个灵活的任务管理器，专为 MVC 类型的应用程序而设计。它是用来代替 crontab 的，其目的是提供一种简单的方法来操作重复的任务。

在某些方面，它类似于 [Laravel 的调度程序](https://laravel.com/docs/master/scheduling)，但它是框架无关的，并带有额外的交互功能，你可以在[的演示中探索这些功能](https://cron.multimate.ru/)。

## 阿耶阿耶皮 [16 ★]

ayeayeapi 宣称自己是“*一个构建 api 的微框架[……]旨在易于使用、快速开发，并能从微小的微服务扩展到庞大的世界。*

AyeAye 只提供请求解析和响应，以及路由——它非常简单，可以用你认为需要的任何第三方包来扩充。需要认证？加入一个 Oauth2 客户机/服务器组合。需要更好的日志记录或希望请求在空闲时间显示？只需插入一个 PSR-3 兼容的记录器，你就可以了。

诶诶，虽然在这一点上是概念验证，但有潜力成为流行的东西——所以继续贡献或使用它来构建一些东西并给作者反馈。如果你用它做了一些有趣的事情，请联系我们——我们会付钱让你写下你的经历！

## [彼特森/派克](https://github.com/peternijssen/packy)【0∝)

由我们自己的[彼得·奈森](https://www.sitepoint.com/author/pnijssen/)开发的 packy ，是一个检查你的依赖状态的工具——也就是说，它们是否是最新的。

packy 与从命令行定期运行的类似工具或像 [VersionEye](https://www.versioneye.com/) 这样的工具的不同之处在于，它是开源的和自托管的，并且它支持几个包管理器和开箱即用的相关遥控器。

看看吧，给彼得一些反馈！

## [蜘蛛/直接上传](https://github.com/eddturtle/direct-upload)【1∝)

[直接上传](https://github.com/eddturtle/direct-upload)是一个漂亮的“快捷”包，用于将文件直接上传到 AWS S3 桶中。你可能认为这没什么大不了的，但是当你考虑到代码官僚主义(codeaucracy？)把一个 PHP 应用程序和 S3 上传系统连接起来，你肯定会开始欣赏这样的小欺骗。

用项目介绍的话来说:“*这个包是为了在直接向亚马逊的 S3 服务发送文件时构建必要的签名(v4)、策略和表单输入而设计的。*

在[这篇文章](https://www.designedbyaturtle.co.uk/2013/direct-upload-to-s3-with-a-little-help-from-jquery/)中解释了该项目的目的和开发过程。

该项目附带了示例使用、指南、单元测试以及您可能需要在不超过几分钟内开始的一切。

## [站点点/rauh](https://github.com/sitepoint/Rauth)【4】

我们让 [sitepoint/rauth](https://github.com/sitepoint/Rauth) 允许对类和方法进行访问控制，而不是路由。因此，您可以禁止`AdminController`和它的一些(或全部)方法，而不是禁止没有“admin”组的任何人使用路由`myapp.com/admin`。为什么？因为这可以让您:

1.  随心所欲地切换路线，不用担心权限混乱
2.  在命令行或浏览器中使用该应用程序，它的工作方式完全一样——不需要任何路线
3.  对应用程序的某些部分有更细粒度的控制

当然，如果这更合你的胃口，这可以加上传统的基于路线的限制，但是我们的[演示无框架框架](https://github.com/Swader/skeleton-sample)需要这种精确的方法。

有几个悬而未决的问题等待着 PRs 或只是评论，所以如果你有一分钟，我们将感谢一些投入，如果不是代码！

## [go AOP/解析器反射](https://github.com/goaop/parser-reflection) [4 ★]

最后但同样重要的是，还有 [goaop/parser-reflection](https://github.com/goaop/parser-reflection) 或“类固醇上的反射”。这远不是唯一的[新的基于 PHP 解析器的反射库升级](https://github.com/Roave/BetterReflection)，所以为了看到关于替代方案的差异和讨论，[这一期](https://github.com/goaop/parser-reflection/issues/1)是一个有趣的阅读。

在任何情况下，随着 PHP7 漂亮的内部升级和速度的提高，反射变得越来越便宜，所以如果您需要从 PHP 文件中读取任何类型的元数据，比如上面的 Rauth 包，或者是[文档生成](https://www.sitepoint.com/fast-multi-language-docs-with-sitepoints-rtdsphinx-php/)，这比以往任何时候都容易！

* * *

这就是二月份的内容——一如既往，请用标签 [#sourcehunt](https://twitter.com/search?q=#sourcehunt) 和 [#php](https://twitter.com/search?q=#php) 把你的链接扔给我们——这是组合的[链接。当然，如果你想寻找用另一种语言编写的项目，就要做相应的改变。](https://twitter.com/search?q=#sourcehunt%20AND%20#php)

编码快乐！

## 分享这篇文章