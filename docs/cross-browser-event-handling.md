# 跨浏览器事件处理

> 原文：<https://www.sitepoint.com/cross-browser-event-handling/>

现代不引人注目的 DHTML 的一个最重要的方面是处理事件。每当用户以某种方式与页面交互时，就会发生一个事件，例如，通过点击链接、移动鼠标或在键盘上打字。JavaScript 程序员编写代码来响应这些事件，并使用事件处理程序将它们附加到页面的特定部分。

可悲的是，虽然现代的浏览器都为 W3C 的 DOM 规范的大部分提供了出色的支持，但事件处理是 Internet Explorer 和 Mozilla/Safari/Opera 之间仍然存在显著差异的地方。这不能全怪微软:在 IE 5 发布的时候，针对 DOM 事件的 W3C 规范还没有最终确定。希望 IE 7 除了保持与 IE 以前版本的向后兼容性之外，还将完全支持该规范。

与此同时，需要一些变通办法。幸运的是，有大量的文档可以填补这个空白。托德·迪格恩多夫最近发表了两篇关于这个主题的文章:[平衡 IE 和 EOMB(其他现代浏览器)之间的 JavaScript 事件引用第 1 部分:DOM 级事件处理程序](http://www.ditchnet.org/wp/?p=5)和[第 2 部分:以编程方式注册事件处理程序](http://www.ditchnet.org/wp/?p=6)。与此同时，自 2001 年以来，Scott Andrew 经过测试的 [addEvent 函数](http://www.scottandrew.com/weblog/articles/cbs-events)一直在帮助解决这个问题。PPK 的 [QuirksMode](http://www.quirksmode.org/) 提供了现有的各种浏览器事件模型的广泛覆盖。

这个话题在 Stuart 的[即将出版的书](http://www.oreilly.com/catalog/0957921896/)中也有详细介绍。

## 分享这篇文章