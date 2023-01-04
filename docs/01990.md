# 开源 Bootstrap 如何让它变得巨大

> 原文：<https://www.sitepoint.com/how-open-sourcing-bootstrap-made-it-huge/>

![Open Source Week](img/31a22f613e5e7d0c22f753b2e51b5549.png)

SitePoint 的开源周到了！整整一周我们都在发布关于开源、自由软件和社区的文章，所以请继续查看 ***[OSW 标签](https://www.sitepoint.com/blog/)*** 获取最新更新。

![Open Sourcing Bootstrap](img/d87aa8d872655e66878749e4d13a904e.png)

由[马克·奥托](https://twitter.com/mdo)和[雅各布·桑顿](https://twitter.com/fat)创建的流行的 HTML/CSS/JS 框架 Bootstrap ，最初是一个专有工具，旨在培养 Twitter 产品的代码一致性和可维护性。2010 年 8 月底，开源 Bootstrap 这个重大决定被采纳。

## 开源 Bootstrap 是一个伟大的决定吗？

![Bootstrap 4 logo](img/c362960f528dae1973c3ed2c8df75496.png)

在[从开源 Bootstrap](http://markdotto.com/2012/04/02/learnings-from-open-sourcing-bootstrap/) 中，Mark Otto 分享了成为前端生态系统中最成功的开源项目之一的核心意味着什么的最佳证据之一:

> 开源 Bootstrap 是我个人和职业生涯中遇到的最好的事情之一。我已经能够帮助成千上万的人更好更快地完成他们的工作——同时帮助他们在工作中多学一点东西。… Bootstrap 值得我投入的每一秒钟，我希望在未来很长一段时间内保持这种状态。

那是 2012 年，现在呢？快进到 2016 年:Bootstrap 4 alpha 5 已经发布，对于我最近在专门的 [Bootstrap Slack 频道](https://bootstrap-slack.herokuapp.com/)上提出的关于开源项目对框架的发展和成功有多大贡献的问题，Otto 很快回答道:

> 如果它不是开源的，它根本不会发展:)如果我们当时没有对外分享，它仍然是 twitter 的一个内部项目。

[开源方式](https://opensource.com/open-source-way)是 Bootstrap 今天如此庞大的核心:GitHub 上第二个[最受瞩目的项目，并为超过](https://github.com/search?l=&o=desc&q=stars%3A%3E1&ref=advsearch&s=stars&type=Repositories)[一千万个在线网站](https://trends.builtwith.com/docinfo/Twitter-Bootstrap)提供前端基础设施。

其核心是设计师和开发人员之间的**沟通**和**协作**。Bootstrap 本身被它的创建者称为一个由它自己驱动的 *[活文档](http://alistapart.com/article/building-twitter-bootstrap)* ，因为它包含了它所记录的模板和组件。作为一个生活方式指南，Bootstrap 为所有关于框架演变的讨论提供了指南，并为尽可能多的人提供了关于如何使用它来构建网站和应用程序的指导。

最后但同样重要的是，作为一个适合所有技能水平的框架和最佳前端开发实践的实例，Bootstrap 可以作为一个优秀的[学习工具](https://www.sitepoint.com/less-beyond-basics-bootstrap-mixins-library/)使用。

事实上，Bootstrap 的座右铭不是偶然的:*帮助优秀的人做优秀的事情*。

## GitHub 上的 Bootstrap 是如何管理的？

不属于 Bootstrap 背后的[开发团队](https://github.com/orgs/twbs/people)并不意味着完全不知道这个项目是如何在 GitHub 上维护和开发的。

让我们不要忘记沟通在为整个项目奠定基础的过程中所扮演的核心角色。事实证明，Mark Otto 不仅擅长记录代码，而且在分享他的愿景、项目管理以及他对许多 web 开发主题的看法时也相当开放。你们当中最好奇的人可以前往[询问@mdo 任何事情！](https://github.com/mdo/ama)在 GitHub 上浏览 Otto 的回复，或者提出你自己的问题。

在[管理引导库](http://markdotto.com/2015/09/28/bootstrap-features/)中，Otto 概述了他和他的团队在引导库上遵循的一些指导原则和实践。

这里有一个总结:

*   Bootstrap 是一个**产品**，有着明确的使命:*让设计和开发更容易*。如果您请求一个特定的特性，Bootstrap 团队将根据这个目标与团队的兴趣和经验之间的平衡来评估是否追求它。
*   功能管理的主要工具是[引导问题跟踪器](https://github.com/twbs/bootstrap/issues)。如果您想建议一个新功能或扩展现有功能，只需打开一个新问题或发送一个拉请求。团队在拒绝与他们的框架目标不匹配的新建议时是非常精明的，但是如果他们接受，您的请求将保持开放，并被分配一个发布的里程碑。
*   在测试之前，主要版本的开发都是保密的。团队乐于在每个主要版本中对框架进行重大修改。哪些功能将会出现，哪些功能将会被废弃，这是由两个私有的 GitHub repos 管理的。一旦团队准备好测试，项目就向社区敞开大门，寻求帮助和反馈。

## 引导版本 4 Alpha 5

随着每个主要版本的发布，Bootstrap 都有了彻底的改变。撰写本文时的最新版本版本 4 也不例外。尽管该框架仍处于 alpha 阶段，这意味着各种变化仍会发生，但你可以通过阅读 GitHub 上的[文档](https://v4-alpha.getbootstrap.com/)和问题追踪器，大致了解一旦稳定版本发布后你会发现什么。

下面是 Bootstrap 版本 4 alpha 中新增功能的鸟瞰图:

*   从少到多。
*   flexbox 的可选使用。
*   用`rem`和`em`单位替换像素，以调整字体、边距和填充。
*   为超大响应断点增加一个网格层。
*   引入重新启动作为 HTML 重置的新模块。
*   使用 ES6 重写所有 JavaScript 插件。
*   使用[系绳库](http://tether.io/)改进工具提示和弹出窗口。
*   卡片组件的废弃孔、缩略图和面板。
*   词缀成分的去除。
*   停止对 Internet Explorer 8 及以下版本的支持。

你可以在 [Bootstrap 4 alpha:赢得你的超级智能特性](https://www.sitepoint.com/bootstrap-super-smart-features-to-win-you-over/)中找到更多关于 Bootstrap 4 Alpha 的广泛变化。

在这里，我不打算包括更多涉及类名、组件的 HTML 结构、Sass 变量的重组和重命名等更细粒度的更改。，因为在该框架的第一个稳定版本发布之前，它们可能会有很大的变化。

如果你好奇，阿尔法文档和 T2 博客是寻找更多细节的最好地方。

## 结论

互教互学，通过开放交流和协作来构建令人惊叹的东西，这是开源哲学的核心。Bootstrap 无疑是开源方法中最成功的例子之一，正是它成就了今天的成就。

为什么不从[开始玩自举](https://www.sitepoint.com/bootstrap-carousel-with-css3-animations/)，从中学习，用你的[贡献](https://github.com/twbs/bootstrap/graphs/contributors)让它变得更棒呢？

## 分享这篇文章