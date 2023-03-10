# BBC Glow–一个新的 JavaScript 库

> 原文：<https://www.sitepoint.com/bbc-glow-javascript-library/>

英国广播公司发布了 Glow，一个新的开源 JavaScript 库。自 2007 年末以来，该库已在 BBC.co.uk 网站上被广泛使用，并提供了常见的功能，例如:

*   简化的 DOM 操作和事件处理
*   自定义用户界面小部件，如滑块和覆盖框
*   动画和效果
*   有用的跨浏览器 JavaScript 函数

问题是:他们为什么要费心？BBC 正在使用 jQuery，还有许多其他的 JavaScript 框架也可以达到同样的目的。BBC 回顾了几个比较受欢迎的图书馆，但没有发现一个符合他们所有的严格准则。可访问性和浏览器支持是主要问题 BBC 要求 1 级 IE6 支持，并在 IE5.5 中测试他们的网站

那么 Glow 和其他相比怎么样呢？Glow 使用类似于 [Yahoo！用户界面库](http://developer.yahoo.com/yui/)。它通常是经过深思熟虑的，合乎逻辑的，尽管它可能会导致稍微冗长的语句。

```
 // fade out an element in 1 second
glow.anim.fadeOut("#myelement", 1); 
```

一个主要的优点是 [Glow 文档](http://www.bbc.co.uk/glow/docs/)——它非常优秀，包含大量示例代码片段和演示。

但是 Glow 提供了任何令人信服的理由来改变你当前的图书馆吗？不太可能；除非你遇到了严重的问题，或者需要 BBC 提供的同等水平的浏览器支持。

总的来说，我喜欢 Glow。它有很好的文档记录，经过了广泛的测试，可能比 jQuery 更容易理解。这是一件好事，因为作为一名英国居民，我的电视许可费已经支付了 Glow。因此，我可以接受你为图书馆的进一步发展所做的任何捐赠！

更多信息，请参考 [BBC Glow 主页](http://www.bbc.co.uk/glow/)。

你试过 Glow 吗？你觉得怎么样？我们真的需要另一个 JavaScript 库吗？

## 分享这篇文章