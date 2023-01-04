# 2015 年 Ruby 回顾展

> 原文：<https://www.sitepoint.com/a-retrospective-on-ruby-in-2015/>

![Progress Bar Uninstalling with the text: 2015](img/142eb89d4bb6b37476cf94e1929ea47d.png)

*这篇文章由[托姆·帕金](https://www.sitepoint.com/author/tparkin)和[弗雷德·希斯](https://www.sitepoint.com/author/fheath)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

我们已经来到了 2015 年的尾声，这是 Ruby 的重要一年。我觉得回头看看当年的一些史诗(主要主题)和组成它们的故事会很有趣。有点像敏捷过程回顾，我把 2015 年看作一系列冲刺，看看我们做得怎么样。

为了一路贯彻这种人为的想法，我需要定义史诗。我要说的是，几乎每年，Ruby 都有以下主要领域/史诗/无论什么，社区需要在这些领域/史诗/无论什么地方有所作为，才能认为 sprint/year 是成功的:

*   提高语言水平
*   壮大社区
*   跟上编程世界的步伐

这些是我定义的史诗。为了衡量 Ruby 社区的表现，我将重温那些触及这些史诗子主题的博客文章、文章和视频。我肯定我会错过一些，但这就是为什么评论，对不对？

最后，任何有价值的回顾都需要看看哪些没有成功。我将做那件事来结束我们今天的时间。我猜一些精明的读者会有很多东西加到我的清单上。

## 提高语言水平

### 红宝石

Ruby 2.2 大约在一年前发布，但是我打算把它包含在今年的待办事项中。2.2 给语言增加了许多东西，下面的故事证明了这一点:

*   超级方法元数据的[添加使得调试更加容易。](http://engineering.heroku.com/blogs/2015-01-14-debugging-support-methods-ruby22)
*   符号 GC 给了 Ruby 垃圾收集器巨大的性能提升，这可能是多年来对该语言最好的补充之一。
*   哦，对了…另一个 GC 变化是[增量 GC](https://engineering.heroku.com/blogs/2015-02-04-incremental-gc/) 在垃圾收集时产生了更一致的性能。
*   语言也有一些不应该被忽视的小变化。

[Ruby 在圣诞节](https://www.ruby-lang.org/en/news/2015/12/25/ruby-2-3-0-released/)发布了 2.3.0，其中包括以下礼物:

*   你可以知道[用一个 CLI 选项冻结你所有的字符串](https://wyeworks.com/blog/2015/12/1/immutable-strings-in-ruby-2-dot-3/?utm_source=rubyweekly&utm_medium=email)，允许你看到你要做多少工作来修正你在代码中处理字符串的方式。
*   如果你还没有听说过安全导航操作员，那你是生活在岩石下。
*   所有版本都包括错误修复，但这个版本听起来很糟糕。这篇文章详细描述了如何修复这个错误，是一本非常好的读物。

有时候提高一门语言意味着告别旧版本。Ruby 在结束对 1.9.3 版本的支持时就是这么做的。*打拍子*晚安，1.9.3，你会被记住。

任何 Ruby 解释器中最令人期待的(也是最大的)改进之一是 JRuby 9000:

*   [这里的](http://blog.jruby.org/2015/07/jruby_9000/)是实际发布的公告，列举了一些主要的变化。
*   JRuby EU 主题视频值得一看，以了解 JRuby 9000 到底有多大。查尔斯·纳特和他的团队应该得到一些巨大的支持。

另一种提高语言的方法是学习新的有用的东西。有很多这样的例子:

*   这个 Reddit 问[你最喜欢的 Ruby 戏法是什么](https://www.reddit.com/r/ruby/comments/29hr4x/whats_youre_favorite_ruby_trick_or_quirk_that)，得到了大量的回复。
*   如何真正学习如何使用 rack-mini profiler 进行分析？是的，请吧。
*   这是一个致力于令人敬畏的红宝石的网站。
*   你永远无法真正了解足够多的正则表达式，对吗？

就这一点而言，我认为 2015 年是 Ruby 中“与性能相关的深度文章之年”:

*   Richard Scheeman(他必须是今年 Ruby 博客 MVP 之一)写了一个关于 Ruby 堆转储的精彩的两篇系列文章。[一个](http://blog.codeship.com/the-definitive-guide-to-ruby-heap-dumps-part-i)和[两个](http://blog.codeship.com/the-definitive-guide-to-ruby-heap-dumps-part-ii)
*   这里有一篇关于如何在 Ruby 中调试内存泄漏的帖子。
*   你曾经弄乱过你的 Ruby 垃圾收集器的设置吗？我也是，但是也许我们应该。

### 年度战役

最后，当一种语言向它的社区提供选择时，它就升级了。这方面的一个主要例子是 RSpec 和 Minitest 之间的斗争:

*   如果你的默认设置是 RSpec，也许下次[可以试试 Minitest](https://mattbrictson.com/minitest-and-rails) 。你可能会感到惊喜。
*   如果你决定永远离开，这将会对你有所帮助。

好吧，好吧，也许“年度之战”是一个小小的点击诱饵，但我确实觉得今年有很多关于 Minitest vs RSpec 的讨论。

### 轨道

就像 Ruby 2.2 一样，Rails 4.2 也是在去年 12 月底左右发布的。同样，我将把它归入 2015 年的待办事项中，因为直到今年才真正感受到发布的反响。这是它带来的结果:

*   当然，查看变化的最好地方是发布说明，在那里你会看到诸如 ActiveJob、异步邮件和充分记录之类的东西。
*   ActiveJob 易于使用，使使用排队系统变得轻而易举。
*   发送异步邮件的常见任务变得更容易测试和实现。

Rails 随着后端 web 环境的变化而变化。以下是一些有助于过渡到未来的帖子:

*   Rails 越来越多地用于创建 API，这里有一些正确使用的技巧。哦，这里还有一些。
*   有没有很牛逼的 Rails 宝石收藏？毫无疑问，你的宝贝红宝石在那里！。

Rails 的一个主要问题来源一直是安全性。那里也有改进:

*   以下是如何确保你安全地配置 Rails 的方法。
*   了解最常见的攻击，如 [CSRF 和 Rails](https://rorsecurity.info/portfolio/cross-site-request-forgery-and-rails) ，对改进您的工作大有帮助。
*   显然，4.2 有几个点版本，主要是关于安全性和 bug 修复。详情请查看[版本](http://weblog.rubyonrails.org/releases/)。

当然，有许多与性能相关的 Rails 帖子:

*   Collective Idea 博客发布了一个关于优化 Rails 内存使用的 4 部分系列。
*   不过，在这之前，你难道不应该[了解你的 Rails 性能](http://www.justinweiss.com/articles/a-new-way-to-understand-your-rails-apps-performance/)吗？
*   一旦你理解了性能，你很可能会最终调整 ActiveRecord。这是另一位 MVP 候选人贾斯汀·维斯的两个帖子。

Rails 4.2.x 并没有像 core Ruby 那样有重大的改变，但是 4.2 为 5.0 奠定了基础，我认为 5.0 将会有重大的改变。

### 混乱的

不言而喻，提供新的基于 Ruby 的非 Rails 框架和库改进了这种语言。以下是 2015 年的一些新东西:

*   Lotus 经历了辉煌的一年，如果你想要一个不是 Rails 的可靠的 OO web 框架，它绝对值得一试。
*   或许是在 Opal 的基础上， [Volt](https://www.sitepoint.com/real-time-web-apps-volt-ruby/) 是一个 web 框架，它使用 Opal 让开发者为前端和后端工作编写 Ruby。
*   Pakyow 是一个新的网络框架，旨在使实时应用变得轻而易举。当然，值得一看。

## 壮大社区

任何一种语言想要繁荣都需要能够培养懂这种语言的人。咄。对于任何不熟悉 Ruby 的人来说，这里有一些帖子可以帮助你:

*   我们自己的金斯利·塞拉斯为新的 ruby 爱好者写了一条道路。
*   同样在 SitePoint 上，Nihal Sahu [向新接触 Ruby 语言的人介绍了 Ruby 社区。](https://www.sitepoint.com/ruby-community-introduction/)

技术多样性已经成为一个非常热门的话题，这是有充分理由的。像 [Rails Girls](http://railsgirls.com/) 和 [RailsBridge](http://www.railsbridge.org/) 这样的项目直接致力于鼓励 Ruby 的多样性。以下是今年关于多元化的一些故事:

*   斯蒂芬妮·伯恩斯[描述了她在 Ruby 代码训练营](http://www.forbes.com/sites/chicceo/2015/01/29/why-women-are-choosing-coding-bootcamps/)的经历。代码营越来越多，这种格式似乎吸引了我们社区中需要的人。
*   像创客学院这样的组织为女性教授的课程提供奖学金。
*   我真的很喜欢《Hello Ruby》的发行，这是一本让所有孩子都对编程感兴趣的儿童读物。说真的，如果你还没买过这本书的话，就给别人买吧。

总而言之，对于 Ruby 的多样性来说，这是很好的一年(但不是很好)。希望这个主题在 2016 年得到更多积极的支持。

## 赶上别人

如今，任何语言的另一个生存策略是确保它可以随着语言本身之外的技术变化并与之集成。Ruby 的核心就是这个，默认情况下，有两个大型解释器(MRI 和 JRuby)接入外部运行时。以下是 2015 年的一些重大科技话题，以及 Ruby 如何融入其中。

### 码头工人

容器化在 2015 年底风靡一时，所以这里有一些关于 Ruby 和 Docker 的帖子:

*   用 Travis Reeder 的这篇文章为你的 Ruby 应用程序创建最小的 Docker 图像。
*   用 Docker 测试 Rails 很容易，只需问一下 [Marko Locker](http://blog.codeship.com/testing-rails-application-docker/) 。
*   Nick Gauthier 演示了如何使用 Docker 对这些 Rails 测试进行并行化。

真的，你不可能在 2015 年不点击 10 个关于 Docker 的帖子就摆平一只死猫。如果你还没学过 Docker，它值得大肆宣传。

### 松弛的

你在用 Slack 吗？你当然是，因为大家都在用 Slack。它很棒，很有用，相当于大多数发达国家的 GDP。Ruby 和 Slack 在一起很棒，看看吧:

*   在 Rails 中创建一个 [Slack 服务，因为它很有趣。](https://viget.com/extend/slack-on-rails)
*   或者，[在辛纳特拉](https://www.sitepoint.com/building-a-slackbot-with-ruby-and-sinatra)做。Slack 爱所有的框架。
*   这里有一个 gem，它为你完成了从 Rails 发布到 Slack 的所有工作。

### 其他语言

ruby 爱好者总是在寻找其他语言来利用或学习，以使开发者的生活变得更好。以下是一些关于观察编程环境的故事:

*   Parse [从 Ruby 转移到 Go](http://blog.parse.com/learn/how-we-moved-our-api-from-ruby-to-go-and-saved-our-sanity) 并获得了一些理智。
*   Rust 今年受到了大量的关注，Robert Qualls 的这篇文章展示了如何在 Ruby 中使用 Rust。
*   然而，Rubyists 的新宠必须是长生不老药。阅读这篇文章,看看为什么有些人把长生不老药作为下一件大事。

最后，在 Ruby 中，书呆子圈子里的其他一些事情也引起了相当大的争议:

*   React 是当前首选的 javascript 框架。点击了解如何使用 Rails [。](https://www.airpair.com/reactjs/posts/reactjs-a-guide-for-rails-developers)
*   如果你用 Ruby 写 web 应用，而不是 DHH，那么微服务是闪亮的新东西。[这篇文章](http://blog.codeship.com/architecting-rails-apps-as-microservices)向你展示了如何将你的 Rails 应用设计成微服务。

## 什么没起作用

所有的回顾都应该花一点时间来回顾那些没有成功的事情。人们很容易产生消极情绪，因此这一部分可能会很长，但我只会提到几项:

*   将 Spring 添加到 Rails 开发工作流中既有褒奖也有批评。许多人都觉得这个作者完全放弃了春天。
*   Adam Hawkins 提到了社区需要改进的许多事情，并给出了一个高水平的路线图。
*   我之前提到过微服务，对它们有相当多的反对意见。尼克·萨特勒(许多许多宝石和[开拓者框架](http://trailblazer.to/)的作者)[写了这篇关于在你疯狂使用微服务之前使用良好设计的文章](http://nicksda.apotomo.de/2015/09/the-only-alternative-to-a-rails-monolith-are-micro-services-bullshit/)。
*   最后，改进发生了什么？有人用吗？MVP 候选人斯塔尔·霍恩(Starr Horne)有，但我觉得很多人都没有。我们应该吗？
*   2015 年是我们了解到[超时是纯恶](http://www.mikeperham.com/2015/05/08/timeout-rubys-most-dangerous-api/)的一年。我的意思是，[这是可怕的](http://jvns.ca/blog/2015/11/27/why-rubys-timeout-is-dangerous-and-thread-dot-raise-is-terrifying/)。

我确信我还错过了一些在 Rubyland 没有实现的东西。在评论里说说我错过了什么？

## 还有…场景！

2015 年再过几天就要结束了。对 Ruby 来说，这是重要的一年，在我看来，这种语言仍然有着光明的未来。2016 年未来会是怎样的？你有意见吗？嗯，放在评论里或者论坛上[。让我们让 Ruby 和我们的社区在 2016 年更加美好。新年快乐](https://www.sitepoint.com/community/t/what-should-be-in-rubys-2016-backlog/210430)

## 分享这篇文章