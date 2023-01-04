# 为什么选择 PHP 而不是替代品？

> 原文：<https://www.sitepoint.com/why-choose-php/>

*本文由 [AppDynamics](http://www.appdynamics.com/solutions/php-monitoring-solution) 赞助。感谢您对使 SitePoint 成为可能的赞助商的支持！*

这是一个流行的问题。为什么人们真的应该选择 PHP 而不是其他选择呢？毕竟 PHP 曾经[经常](http://me.veekun.com/blog/2012/04/09/php-a-fractal-of-bad-design/) [被](http://alokmenghrajani.github.com/wtf/php.html) [斥](http://phpsadness.com/)为[hacky](http://www.phpwtf.org/)[不可用](http://www.codinghorror.com/blog/2008/05/php-sucks-but-it-doesnt-matter.html)[烂](https://wiki.theory.org/YourLanguageSucks#PHP_sucks_because)的设计语言。当从零开始一个项目时，为什么有人会选择它呢？

不要列出人们选择它的原因(大多数是广泛的可用性)，而是让我们关注人们为什么 T2 应该选择它。然而，我们不能谈论这个，除非首先提到为什么他们*不应该*。

## 当你不应该选择 PHP 时

### 当考虑命令行应用程序时

如果你正在构建一个命令行应用，PHP 是*而不是*正确的选择。当然，用 PHP 构建 CLA 有一些不错的方法,但是它并不打算这样做。PHP 首先也是最重要的是一种 web 语言，一个纯命令行应用程序最好内置在其他东西中。这并不是说[不可能](http://www.bitfalls.com/2013/08/autofight-php-job-interview-task-part-1.html)——但用另一种语言来做肯定更快更容易——更不用说更高的性能了。用 PHP 构建 CLI 应用程序符合人们将 PHP 比作双爪锤子时使用的比喻。它只是不是为它设计的工具。例如——Python 预装在大多数*nix 发行版上，因此您无需`php.ini`黑客就可以立即访问它，并且可以立即开始工作。

### 当它看起来最容易的时候

如果 PHP 看起来是最简单的，并且是你唯一的动力，那你的心就错了。PHP 可以很快变得复杂，虽然入门路径确实比其他语言容易，但专业级别的复杂性与其他语言相同。做严肃的工作在任何语言中都同样困难。

### 仅仅因为你的共享主机提供商支持它

如果你正在使用一个共享主机，并且对它支持 PHP(甚至可能是一个不完全过时的版本，比如 5.3)感到高兴，停止吧。如果你自称是开发人员，你应该**永远不要**使用共享主机做**任何事情**，除了像定制网络邮件、域管理或非常简单的演示之类的琐事。DigitalOcean 等替代产品以每月 5 美元的价格提供虚拟专用服务器，选择共享主机毫无意义——尤其是在专用实例给你更多控制权的情况下。如果你对 DevOps 有意见，并且不喜欢建立自己的服务器([你真的应该学会这样做，尽管](https://www.sitepoint.com/becoming-php-professional-missing-link/))，许多许多云托管提供商提供为你预先配置的免费层。远离共享主机。

### 计算密集型软件

当你期望处理大量的数学、繁重的计算、统计等等时，有更好的选择——特别是如果应用程序主要做*这些事情，而很少做其他事情。特别是，像 [Scala](http://www.scala-lang.org/) 或新人 [Dart](http://dartlang.org) 这样的函数式语言在这个任务上比 PHP 表现得更好[，这可能需要更多的努力，而不值得让它做你想做的事情。在处理这些任务时，它比专门为它们设计的语言要慢得多。](http://stackoverflow.com/questions/6171807/does-php-optimize-tail-recursion)*

有时候，用你在当前应用程序中已经使用的语言来编写计算量大的应用程序更简单，但更多时候这是一种懒惰的捷径。不得不安装辅助语言或虚拟机，并将应用程序的一部分作为单独的应用程序来构建，这种不适通常会被长期收益所抵消，尤其是当流量开始需要它时。这就是为什么 Twitter 的后端计算现在都在 Scala 中进行，尽管他们的前端[仍然是 RoR](http://blog.redfin.com/devblog/2010/05/how_and_why_twitter_uses_scala.html#.UvjkFPldVK8) 。

## PHP 是非常好学的

上面列出的问题是知识和经验不足的问题，自然，当有人出于这样的目的使用 PHP 时，最终结果是次优的——给 PHP 已经受损的声誉增加了负面影响。PHP 擅长的是任何需要快速开发的面向 web 的场景，并且需要执行得非常快。

PHP 拥有一个庞大的社区。没错，绝大多数人都是新手，但是一旦你剔除了糟粕，剩下的就是一小撮敬业的专业人士，比如 T2 的菲尔·斯特金、T4 的乔希·洛克哈特、迈克·范·瑞尔、安东尼·费拉拉等等。十几个高度敬业的专业人士承认 PHP 的错误，但也努力教育公众并解决这些问题，他们可以提供丰富的知识。再加上像 [SitePoint 的论坛](https://forums.sitepoint.com)、 [StackOverflow](http://stackoverflow.com) 和 [PTRW](https://www.sitepoint.com/php-right-way-book/) 这样的优秀资源，PHP 中遇到的问题很容易解决，而且进步通常既快又有很高的教育性。除非你问的问题是彻头彻尾的愚蠢(是的，确实有*愚蠢的问题)，这个社区是非精英的，并且总是乐于提供帮助。*

可以学习的[开源 PHP 项目](https://github.com/search?l=PHP&q=php&ref=cmdform&type=Repositories)以及[书籍和课程](https://learnable.com/topics/php)非常多，有了耐心和指导，你可以在相对较短的时间内精通这门语言。

## 消除负面新闻

自从上次人们集体抨击 PHP 以来，它已经成熟了很多，并且从其他语言继承了越来越多的现代特性。偏见是普遍的，一个人应该学会认识它。正如这篇来自 2010 年的[优秀 MailChimp 博客文章所说，问题不在于工具，而在于你如何使用它。](https://blog.mailchimp.com/ewww-you-use-php/)

PHP 允许对所有类型的应用程序进行快速原型开发，并且可以让您立即启动并运行。这是一种快速而健壮的语言，在复杂的 web 应用程序中,[远远胜过 RoR](http://blog.bensigelman.org/post/57543889435/why-nobody-should-use-rails-for-anything-ever) 和 Python，加上像 [HHVM](https://www.sitepoint.com/hhvm-revisited/) 和[费尔康](https://www.sitepoint.com/up-and-running-with-the-fastest-php-framework-on-php7-in-5-mins/)这样的项目带来的额外好处，PHP 的性能在动态语言领域是无可匹敌的。打字至少以某种形式出现在普通 PHP 和 HHVM 的 Hack 中，随着像脸书这样的巨人继续直接从事这项工作，PHP 将会得到越来越多的重视。

现在，在加入潮流并大喊“使用 NodeJS，这是最好的！！11”或“MailChimp 从一开始就使用 NodeJS 会更好”，请首先考虑 JavaScript 世界在目前的形式下是多么的混乱和分散。它比 PHP 年轻得多，但它提供的框架已经超过了 PHP——这是 PHP 憎恨者经常嘲笑的一点。NodeJS 已经有 5 年的历史了，在撰写本文时，它的最新版本是 v0.10.25。开发人员似乎对它非常不确定，他们拒绝让它在 5 年后超过 0.1，更不用说进入 1.x 领域了。各种解决方案之间也存在不一致，每一个都声称是“做 X 的更简单/更好/更快的方法”。这个社区很大，但却非常分散。这条推特听起来特别真实:

> web 开发人员的喝酒游戏:
> (1)想出一个名词
> (2) Google " <名词>。(3)如果以那个名字命名的图书馆存在——喝
> 
> —谢伊·弗里德曼(@ iron Shay)[2013 年 8 月 22 日](https://twitter.com/ironshay/statuses/370525864523743232)

我无意抨击其他语言——其他人在抨击 JS 方面比我做得更好——我想指出的是，人们可以对任何语言的缺点吹毛求疵。就像在生活中，如果你只关注不好的东西，你将永远不会体验到好的东西——PHP 的缺点在很大程度上已经被库和框架解决了，虽然有人可能会说“真正的语言”不需要补丁或大公司的帮助就能工作，但你可以通过问 Java、NPM 或 PUB 库和包到底是什么来反驳，如果不是对语言的补丁和改进，添加别人好心构建的缺失功能。

因此，让我们停止关注 X 语言有多糟糕，开始致力于展示我们选择的优越性质的项目。是的，有一些项目拖了它的后腿——WordPress 就是 PHP 的一个这样的项目——但是如果我们坚持不懈，并牢记[最佳实践](https://www.sitepoint.com/php-right-way-book/),我们就能消除这个坏名声，开始重建我们热爱的 PHP 世界。

## 结论

不管复杂程度如何，PHP 都是新项目的绝佳选择。在过去的几年中，PHP 世界一直在以真正快速的步伐发展，一些革命性的新特性甚至在今天也可以投入使用(参考上面的 HHVM 和法尔孔链接)。我并不是说 PHP 应该是你工具箱中的唯一工具——精通多种语言在编程语言中与在自然语言中一样有用——但掌握它肯定不会有什么坏处。PHP 将赋予你在几天内从想法、原型到产品的能力——这是没有多少语言能做到的。像内置 OpCache 和 HHVM 这样的虚拟机这样的最新进展进一步发展了这种语言，允许你大大减少云托管费用，像 [AppDynamics](http://www.appdynamics.com/solutions/php-monitoring-solution) 这样的性能监视器将让你充分利用它的极限。

当考虑你的下一个项目时，不要问自己“谁讨厌 PHP？”或者“人们会怎么想？”。这些都是虚荣和不安全感的问题。你认为有人会认为 MailChimp，这个每天处理数百万封电子邮件的系统，是在他们“坦白”之前基于 PHP 开发的吗？相反，尽你所能去建造。

## 分享这篇文章