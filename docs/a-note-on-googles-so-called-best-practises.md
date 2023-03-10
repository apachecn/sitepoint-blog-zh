# 关于谷歌所谓的最佳实践的说明

> 原文：<https://www.sitepoint.com/a-note-on-googles-so-called-best-practises/>

本周早些时候，谷歌发起了一项让网络更快的活动。这似乎是在呼吁提高网站的总体质量，但重点是性能。

该项目包括一篇关于[“PHP 性能提示”](http://code.google.com/speed/articles/optimizing-php.html)的文章，近乎荒谬。当然，网络上有很多类似的胡说八道的博客帖子，但由于这是由谷歌认可的，所以可以合理地假设人们可能会信以为真。这很不幸，因为**几乎没有一个是真的。**PHP 团队回复了[这条消息](http://groups.google.com/group/make-the-web-faster/browse_thread/thread/ddfbe82dd80408cc)，其中他们驳斥了毫无根据的胡说八道。

然而，更麻烦的是，性能优化的整个方法是一个“知道秘密握手”的问题。优化远比这复杂，花时间在这些“优化技巧”上很不值得，如果应用得太早，那么[可能经常导致不可维护的代码。这是一条细线，因为有*当然有*你不应该做的某些事情(比如在一个紧循环中点击数据库，如果你可以发送一个查询来代替)，但是同样有很多事情完全是在浪费每个人的时间。遗憾的是，这篇文章未能将这两个问题分开。](http://en.wikipedia.org/wiki/Optimization_(computer_science)#cite_note-0)

性能优化不是魔术。处理它的真正方法是测量和分析。几乎不可能——即使对于一个技术高超的程序员来说——准确预测程序中哪里会有瓶颈。使用一个分析器给你真实的数字，而不是胡乱猜测和迷信。

对于 PHP 脚本，有免费提供的 [Xdebug 扩展](http://www.xdebug.org/docs/profiler)，它提供了每个函数的内存使用和执行时间的详细信息。它很容易安装，有了简单的 [WebGrind](http://code.google.com/p/webgrind/) 作为前端，您很快就可以开始运行了。

## 分享这篇文章