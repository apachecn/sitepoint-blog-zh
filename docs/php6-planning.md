# PHP6 规划

> 原文：<https://www.sitepoint.com/php6-planning/>

这在 [Planet PHP](http://www.planet-php.net) 上到处都是，但是如果你错过了，
公布了一个核心开发者在巴黎关于 PHP 6 未来[的会议纪要。](http://www.php.net/~derick/meeting-notes.html)

杰夫发表了一些非常积极的感受。同样地，我认为会有一些很棒的事情发生。

现在没有时间进行详细的回顾？)所以有些短裤。计划中的 Unicode / i18n 支持是一个大项目——如果你想知道 ICU 是什么，看看这里的—这不是一个玩具项目。注册全局变量已死，万岁[输入过滤器](http://www.procata.com/blog/archives/2005/03/31/the-usability-of-input-filtering/)。PHP6 将意味着更多的 BC 中断，但原因是正确的。名称空间又出现了，但带有一个挥之不去的“？”闲荡(所以不要抱太大希望)。

当然，像每个没有为核心做出贡献的人一样，我有一个关于被拒绝的变更的“观点”，我需要大声疾呼:[delegates](http://www.php.net/~derick/meeting-notes.html#delegates)——被这个评论“我们没有看到任何真实世界的代码示例”所煽动。请允许我提供…

很久以前被覆盖的代表[这里](http://www.phppatterns.com/docs/design/the_decorator_pattern_redecorated)(称他们为装饰者)参考现实世界中使用他们的项目，像[这个](http://pear.php.net/manual/en/package.datetime.calendar.intro-decorators.php)。今天用 PHP 实现它们的方式可能是数学化的，原因有两个——首先，你已经编写了大量“愚蠢”的代码[,就像这个](http://cvs.php.net/co.php/pear/Calendar/Decorator.php?r=1.3);其次，你正在注入大量“毫无价值”的方法调用，它们带来了巨大的性能开销。如果有可能消除后者，后者可以(也许)在发动机级以某种聪明的方式消除。一些指定委托/装饰的智能语法会很酷，但是更酷的是一些智能引擎级的冗余调用的消除。

咆哮完毕——感谢聆听。

## 分享这篇文章