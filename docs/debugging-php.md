# 调试 PHP

> 原文：<https://www.sitepoint.com/debugging-php/>

我刚刚登记了一个小项目，我已经做了几个星期了。

它真正开始于上次的哥本哈根 php-meetup。[约阿金·尼加德](http://jokke.dk/)和[雅各布·厄廷格](http://oettinger.dk/)展示了他们的项目 [WebCacheGrind](http://code.google.com/p/webgrind/) 。我不经意地提到了 dbgp-protocol(Xdebug[Xdebug](http://www.xdebug.org/)的调试部分)的一些事情，并且为它编写一个 fontend 应该足够简单。这引起了后排一些人的窃笑。显然，有些人在他们的工作场所有这样一条规则，那就是无论谁说某件事情应该容易做，他自己就必须去做。我想这不是一个不合理的规则。显然，我不能放任不管，所以我试了一下。

[![Screenshot of Spectator](img/f91c3094ee947ba565a4c4b6ae2eac5a.png)](https://www.sitepoint.com/wp-content/uploads/2008/06/spectator-small1.png)

旁观者是一个 XUL 应用程序，这应该使其跨平台。我以前对 XUL 进行过一些修补，但不是一个完整的应用程序。如果你想知道什么是 [XUL](http://www.mozilla.org/projects/xul/) ，它是 GUI 工具包，Firefox 和 Thunderbird 的前端就是在其中编写的。它是一种标记语言——很像 HTML，可以用 Javascript 编写脚本。这使得它非常容易使用。唯一的问题似乎是相当缺乏文档，但一点侦查工作让我通过。

那么旁观者能做什么呢？请记住，这是第一个版本，我真的只是想证明它的概念。我想我比那更进一步，但是它可能仍然有一些错误。不过，在当前版本中，你可以单步调试程序，设置断点并检查堆栈。这就是你对调试器的所有期望。

当然，正如你们中的一些人可能会指出的，已经有了其他可用的实现。最引人注目的是科莫多，这也是与 XUL 一起实现的。但是，这些实现是专有的，并且与 IDE 集成在一起，您可能不想使用它们。Spectator 是一个开源的选择，是 IDE/编辑器不可知的。

## 分享这篇文章