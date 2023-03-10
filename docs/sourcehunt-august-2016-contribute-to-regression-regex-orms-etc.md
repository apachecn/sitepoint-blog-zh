# source hunt 2016.8–为回归、正则表达式、ORM 等做出贡献

> 原文：<https://www.sitepoint.com/sourcehunt-august-2016-contribute-to-regression-regex-orms-etc/>

# Sourcehunt August 2016

假期结束了，是时候再次寻找一些开源贡献了！

![Sourcehunt logo](img/407bcf1bded70a8c020759619779150b.png)

* * *

## [austintoddj/Canvas](https://github.com/austintoddj/Canvas)[561★]

Canvas 是一个新的迷你 CMS，面向需要个人博客的开发者。它的目标是既美观又实用。我在寻找一个[个人博客引擎](https://www.sitepoint.com/8-must-have-grav-plugins-to-round-off-your-blogs-installation/)的过程中试用过它，虽然当时它有点不足，但所有者对拉请求和想法非常敏感，而且今天的工具与我一个月前试用的工具非常不同。

如果您正在寻找一个易于使用、易于设置和易于部署的 CMS，我鼓励您尝试一下 Canvas，提交扩展和请求来扩展它已经非常活跃的社区。

* * *

## 智囊团/爆炸形态 [64 ★]

疾风 ORM 是“又一个 ORM”。如果人们在 Reddit 上对它的抱怨被认真对待，它还有一段路要走，看起来和感觉起来都很专业。

然而，和 Canvas 的 Todd 一样，Blast 的作者也非常乐于接受反馈，并且正在积极地(公开地)学习，一路改进这个包。这个 ORM 是否会持续一段时间还不清楚，但它是一个年轻而雄心勃勃的项目，可能需要(也应该得到)帮助。在一个生态系统中，几乎每个 ORM 要么是框架特定的，要么是教条的，引入一些新的参与者无疑是令人耳目一新的。

* * *

## [【talesoft/tale-jade】](https://github.com/Talesoft/tale-jade)【66】

Jade 是 HTML 的一种“方言”，就像 CoffeeScript 对于 JavaScript 一样。Tale-jade 是一个“PHP 的 jade 模板语言的完整和全功能的实现”。

"但是不是已经有 PHP 的 Jade 端口了吗？"你可能想知道。是也不是。正如作者在 reddit 介绍文章中解释的那样，目前发布的 PHP Jade 包是不完整的，有很多错误——因为尝试了直接移植，所以缺少了 JS 版本的特性。这是一个完整的重写，添加了[一些用于循环的语法糖](https://www.reddit.com/r/PHP/comments/4zcaug/jadepug_in_the_php_world/d6ur9kw)——缺省的 Jade 所缺少的东西。这确实偏离了规范一点，是的，但是作者也声明了他的目标是创建一个“如玉般的模板引擎”，而不是像以前的尝试那样一个完整的端口。这是个好主意还是坏主意还有待观察。

杰德的人气正在回升，但还有很多工作要做。为什么不加入进来帮忙呢？

* * *

## galvao/comph part[5★]

简洁明了:“通过 PHP 和 Guzzle 访问 Pocket 的 API 的组件”。这与类似于 [this](https://github.com/djchen/pocket-api-php) 的东西有什么不同？首先，compact part 是 alpha，非常非常不完整。因此，如果你想要稳定，[其他包](https://github.com/djchen/pocket-api-php)是一条路要走。第二，它使用了 Guzzle——这是另一个包所没有的(它专门使用了 Curl)。

一些人喜欢使用 Guzzle 来获得一些抽象，另一些人喜欢直接、快速的 Curl 访问。其他人用`file_get_contents`(我知道……\_(ツ)_/)获取他们的 API 调用，其他人仍然从包中[抽象甚至“大吃大喝”](https://www.sitepoint.com/breaking-free-from-guzzle5-with-php-http-and-httplug/)。这是个人喜好的问题，目前，康普公司没有提供(或似乎不打算提供)任何特别有价值的东西。然而，它是一个年轻的包，利用了现代 API 通信实践和健壮的包，缺少一些简单但重要的特性。因此，这是**完美的**候选人，让人们来湿湿他们的开源贡献口哨！

* * *

## [带宽节流/令牌桶](https://github.com/bandwidth-throttle/token-bucket) [174 ★]

token-bucket 是 PHP 中令牌桶算法的线程安全实现。您可以使用令牌桶来限制资源的使用率(例如，流带宽或 API 使用)。”

线程安全意味着多个线程可以同时执行同一件事情，而不会有任何负面影响。应该注意的是，线程安全只适用于包本身，而不是它的代码 *bucketing* 。

该包可用于限制对 API 或站点某些部分的访问(例如，减慢下载速度)，而无需在内部进行任何调整，也无需处理队列、数据库、日志或任何超出安装和配置该包的复杂内容。

最近关于这个包的源代码的活动又开始了，所以现在是一个绝佳的机会在那里获得一两个 PR，或者为我们写一个关于这个包的教程。

* * *

## [mcordingley/回归](https://github.com/mcordingley/Regression) [55 ★]

数学和统计学不是 PHP 的领域，而是 R 之类的[领域，但这并不意味着常见的数据分析算法不能用我们最喜欢的语言再现。](https://www.sitepoint.com/introduction-r-rstudio/)

简而言之，*“…它试图找到描述数据中关系的最佳拟合线。它接受一系列训练观察，每个观察都由特征和结果组成，并发现每个特征对结果的贡献有多大。”*

正如文件所说，回归计算量很大，这让我认为它可能是用 Zephir 重写[的完美候选。如果你对这个包不感兴趣，为什么不尝试重写呢？可以学到很多知识，我们会给你丰厚的报酬！](https://www.sitepoint.com/up-and-running-with-the-fastest-php-framework-on-php7-in-5-mins/)

* * *

## spatie/regex [223 ★]

最后但同样重要的是，我们有一个软件包，旨在使编写正则表达式成为一种没有威胁的体验。虽然它只不过是 PHP 的`preg_`函数的面向对象包装器，但它为用户提供了一个详细的、可用的、可读的公共 API。请注意，这本身并没有提供任何*新的*功能——它只是让`preg`功能更容易访问，因为它们的 API 完全不同。

这个库的一个可能的缺点是它的许可证。虽然这可能是一个笑话，但 Github 对回购中的许可证非常重视，从理论上讲，这可能会给某些人带来麻烦。虽然没有多少人真正遵守许可证，甚至阅读许可证，但我可以肯定地看到这吓跑了一家公司或另一个正式/合法的实体。也许这个包的第一次公关应该是更新他们的许可证？:)

* * *

八月份到此为止——一如既往，请用 [#sourcehunt](https://twitter.com/search?q=#sourcehunt) 标签将你的链接发给我们！

编码快乐！

## 分享这篇文章