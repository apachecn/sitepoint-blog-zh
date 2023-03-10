# 用 JavaScript 实现简单的日期和时间本地化

> 原文：<https://www.sitepoint.com/simple-date-and-time-localization-with-javascript/>

我们在 [SitePoint 竞赛](https://www.sitepoint.com)和 Marketplace 的工作中遇到的众多挑战之一是决定向用户呈现日期和时间的最佳方式。

这听起来很简单，但是我们需要记住一些注意事项。

**易于人类阅读**

“2 小时前开始”和“2 天内结束”比“2007 年 6 月 4 日星期一上午 10:04+1000 开始”和“2007 年 6 月 6 日星期三上午 9:28+1000 结束”更容易理解。

**可被搜索引擎缓存**

“2 小时前开始”或“2 天内结束”在查看搜索引擎 2 天前缓存的页面片段或完整副本时毫无意义。同样，“开始于 2007 年 6 月 4 日星期一，上午 10:04+1000”对于一个完全不同时区的人来说是很难理解的。

**可缓存性能**

我们希望能够将缓存生存期分配给尽可能多的页面部分。文本“Started Mon，June，2007，10:04am +1000”不需要缓存过期，而“Started not one before”只能可靠地缓存 1 秒。

**当地时间**

虽然大多数人都能理解“开始于 2007 年 6 月 4 日星期一，10:04am GMT”在他们当地的时区是什么意思，但如果我们能为他们计算出总数，那将是最有价值的，这样他们就不需要花 30 秒来理解拍卖在 1 秒前就结束了。

**陈旧的页面**

因为生活还不够复杂，所以标签式浏览被发明出来，这样我们可以一次向大脑中输入更多的信息。Tabs 还可以让你在回到 SitePoint Marketplace 列表之前轻松地被跟踪几个小时。但是目前没有办法知道拍卖说“28 分钟后结束”实际上早就结束了。

**微解**

为了一举解决所有这些问题，我们创建了一个非常简单的内部微格式和一些聪明的 JavaScript。总之，它们让我们以一种对所有用户都有用、有意义和可访问的方式呈现日期和时间。

我们从一些基本的 HTML 标记开始:

 `<span class="sitepoint-datetime">Mon, 28 May 2007 01:30:49 GMT</span>` 

没有 JavaScript 的用户和一些辅助技术的用户会看到这一点。它可能没有“7 天前”漂亮，但当我们不知道页面实际上是如何、何时或在何处被查看时，它是最正确、最有意义和最容易被接受的格式。我们在类名前加了前缀“sitepoint-”，以表明这不是标准的微格式。

我们对日期/时间使用了 [RFC 2822](http://www.faqs.org/rfcs/rfc2822) 格式，这很容易被人阅读，也可以直接传递给 [JavaScript 日期对象](https://www.sitepoint.com/beginners-guide-to-javascript-date-and-time/)的构造函数。

我们的 JavaScript 找到这种微格式的所有实例，并将它们转换为用户的当地时间，以友好的格式显示，而不在末尾挂一个难看的时区标识符。

然后，我们可以向 span 的 class 属性添加附加信息。JavaScript 代码使用这些来决定显示信息的最佳方式。例如，使用额外的“endtime”类，JavaScript 会将时间转换为倒计时，以类似“1 天 3 小时”的格式显示。剩余时间每 30 秒重新计算一次，因此在左侧打开的浏览器选项卡上不再有过时的信息。一旦倒计时到零，这个脚本甚至会直观地标记比赛和拍卖已经结束。

我们期待着在设计竞赛和市场上获得这些和许多其他在线改进。

同时，我确信对于微格式和 JavaScript 的这种使用，甚至对于术语“微格式”是否适用于这种标记模式，有很多观点。请让他们来吧。

## 分享这篇文章