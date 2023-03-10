# 使用 JavaScript 进行演示

> 原文：<https://www.sitepoint.com/using-javascript-for-presentation/>

我最近的一篇文章，[用 CSS 和 JavaScript](https://www.sitepoint.com/article/rounded-corners-css-javascript) 圆角，刚刚发表。它介绍了实现圆角的各种方法，包括一种老式的表格技巧、两种 CSS 方法和一种使用 JavaScript 修改 DOM 并提供额外 CSS 挂钩的新技术。

这种表示性的 JavaScript 还没有引起太多的讨论。因为 JavaScript 可以在事后对文档结构进行任意修改，所以可以实现各种各样的表示调整。显而易见的问题是这是否是一个好主意；毕竟，JavaScript 传统上是用来给 web 文档添加行为的，而 CSS 则用于表现方面。问题是，虽然 CSS 是一项强大的技术，但它也有自己的局限性——比如我在文章中讨论的不能对一个元素应用多种背景。JavaScript 让我们避免了这些限制，而不必向底层标记添加额外的代码。

在我看来，这种表示的 JavaScript 很好，只要它不会导致没有 JavaScript 支持的用户代理无法访问内容。圆角技术是一个理想的例子:没有 JavaScript 的浏览器只是错过了一个小的、不必要的设计元素，实际上甚至不可能意识到页面中缺少了一些东西。

Bobby van der Sluis 也有类似的想法。

题外话:我在弄清楚我的 [addLoadEvent 函数](http://simon.incutio.com/archive/2004/05/26/addLoadEvent)的细节之前就写了圆角文章。我推荐使用本文中使用的简单的 window.onload 赋值。

## 分享这篇文章