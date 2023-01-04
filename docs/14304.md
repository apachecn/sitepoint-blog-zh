# 6 万行 Javascript 代码

> 原文：<https://www.sitepoint.com/60000-lines-of-javascript/>

[Koranteng Ofosu-Amaah](http://www.blogger.com/profile/3914642) 对[应用程序与 W3C DOM](http://groups.yahoo.com/group/wdf-dom/message/4136&threaded=1) 做了一些有趣而有见地的评论(这要追溯到 2003 年 7 月，顺便说一句——跳过广告)。暂时撇开富 web 应用程序的讨论，触动心弦的是 point 8。“Javascript 太灵活”；

> 在我处理的 60，000 行 Javascript 中，我有时会找到多达 7 种方法来解决代码中的同一个问题；有些方法非常强大，“聪明”和独特，但归根结底，从长远来看是不可理解的。

这个问题的一部分是 Javascript 特有的——试着在谷歌上搜索 Javascript 和继承，并比较你得到的建议——但我认为这里有一个适用于所有动态语言的普遍问题。Koranteng 呼吁开发人员努力编纂最佳实践。

在过去的一年里，我看到越来越多的人谈论 PHP 的最佳实践，这是一件非常好的事情，但同时，仍然看到新的开发人员犯 PHP 允许他们犯的“错误”。在天平的另一端，有些开发人员非常了解一门语言，并且无法抗拒添加“智能”特性。这需要成为动态语言的一条金科玉律——不要扩展语言(至少不要使用语言本身)。

在 PHP 中，这可能意味着“不要定义类字符串”。在 Javscript 中，也许是添加了一个属性“超类”来使继承成为可能。与此同时，Python 默认有一个很棒的设计，画出了类似于 [this](http://aspn.activestate.com/ASPN/Cookbook/Python/Recipe/325917) 的东西。

试图理解设计者的意图应该是使用动态语言变得“严肃”的第一步。在缺乏明确意图的情况下(PHP 有许多灰色区域)，是时候与其他开发者展开讨论了。

最佳实践列表中的下一条可能是“您必须编写单元测试”。Perl、PHP、Python 和 Javascript(我用过的)都有优秀的单元测试框架。

还有什么？

**站台分界**

回到柯然腾；

> 我不想卷入语言战争，但我会悬赏谁能解决这个问题。我想被宠坏，就像 J2EE 或。Net 程序员被宠坏了。

这是一个挑战。遗憾的是，大型软件公司仍然错过了这条船。这种态度可以用[瘦到让我懊恼](http://blogs.msdn.com/ericlippert/archive/2003/11/18/53388.aspx)(2003 年 11 月)来概括；

> 但是我能给你的最好的建议就是简单地为正确的工作使用正确的工具。脚本语言对于它们的预期目的来说是极好的工具。C#和 C++也是。但是他们真的很不擅长做彼此的工作！

个人认为这是一个自我实现的预言。这是因为大供应商持有这种观点，即通过开源，动态语言的状态只是在逐渐改善(这可能是件好事)。不过，微软似乎确实在两面下注，奇怪的[智能招聘](http://www.microsoft-watch.com/article2/0,1995,1654275,00.asp) …

支持动态语言的计数器以强输入对强测试开始，以类似于 [wxPython](http://wxpython.sourceforge.net) 、Gmail 和 A9 的结果结束。

与令我懊恼的不同的是 [Thin，Koranteng 在](http://blogs.msdn.com/ericlippert/archive/2003/11/18/53388.aspx)[对富网络应用、AlphaBlox 和 Oddpost](http://koranteng.blogspot.com/2004/07/on-rich-web-applications-alphablox-and.html) 做了进一步的精彩评论(后续[在此](http://www.looselycoupled.com/blog/lc00aa00046.html)——正常运行时间不同)。

## 分享这篇文章