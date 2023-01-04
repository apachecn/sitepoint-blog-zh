# Opera 反击:宣布下一代 JavaScript 引擎

> 原文：<https://www.sitepoint.com/opera-fights-back-announces-next-gen-javascript-engine/>

争夺网络浏览器霸主地位的最重要战线之一是 JavaScript 引擎速度之战。这种胜人一筹的游戏已经持续了一段时间，要保持谁拥有目前最快的 JavaScript 引擎并不容易——尤其是因为不同的基准测试往往会产生截然不同的结果。然而，无论目前最快的引擎是谷歌的 V8、Mozilla 的 TraceMonkey、苹果的 SquirrelFish、微软的 JScript 还是 Opera 的 Futhark，这场战斗都很重要。

原因与向基于云的网络应用的缓慢过渡有关。我们上个月报道过，2009 年全球超过一半的开发人员将从事软件即服务项目，所以趋势肯定是朝着这个方向发展的。为了模拟更加“类似桌面”的用户体验，许多 web 应用程序使用了大量的 JavaScript。所以浏览器渲染 JavaScript(或者 ECMAScript)的速度变得越来越重要。

9 月份，当谷歌首次发布 Chrome 时，jQuery 的创始人 John Resig 在他的博客上发布了一个很好的 JavaScript 引擎性能概要。虽然他的基准测试现在可能已经过时了，但当时有一个主题是明确的:Opera 的 Futhark 引擎几乎是垫底的，与 Internet Explorer 的 JScript 引擎为伴。

本周 Opera 宣布了他们的下一代 JavaScript 和 ECMAScript 引擎，名为 [Carakan](http://my.opera.com/core/blog/2009/02/04/carakan) 。根据 Opera 的说法，当他们首次发布 Futhark 时，它是市场上最快的 ECMAScript 引擎。这种情况显然不再存在，Opera 现在打算再次提高标准。

Opera 核心团队成员 Jens Lindströ表示:“然而，网络是一个不断变化的环境，未来的高级网络应用将需要更快的 ECMAScript 执行速度，因此我们现在接受挑战，再次开发市场上最快的 ECMAScript 引擎。”

Carakan 在三个方面做出了改进。首先，切换到基于寄存器的字节码指令集，而不是基于堆栈的，Lindströ说这样可以加快速度，因为“需要执行的指令更少，需要复制的数据也更少。”第二，Carakan 将包含一些 ECMAScript 程序和函数的本地代码生成。最后，新引擎将进行自动对象分类。

尽管 Opera 还没有准备好发布任何基准来显示 Carakan 在竞争中的表现，但 Lindströ表示，根据 SunSpider 基准，在没有任何生成的本机代码的情况下，Carakan 比 Opera 10 Alpha 中的当前引擎快大约 2.5 倍。本机代码生成还不能用于广泛的基准测试，但是在一些单独的测试中，它要快 5 到 50 倍。不算太寒酸。

当然，人们也不得不怀疑，对于歌剧来说，这是不是太少，太晚了。根据 Net Applications 的数据，Opera 仅占浏览器市场的 0.71%，这一数字在过去一年中基本持平。虽然 Opera 几乎没有增长，但 Firefox、Safari 和 Chrome 都有显著增长(IE 失去了相当多的份额，正如我们最近提到的)。话说回来，Opera 在移动设备上的数字要高得多，在移动设备上，快速的 JavaScript 性能可能更加重要。

## 分享这篇文章