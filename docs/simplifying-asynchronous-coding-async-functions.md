# 用异步函数简化异步编码

> 原文：<https://www.sitepoint.com/simplifying-asynchronous-coding-async-functions/>

![An hourglass with sand pouring through](img/3f1a3e2d751a054d4c55a77cf8ba304b.png)

JavaScript 中[承诺](https://www.sitepoint.com/overview-javascript-promises/)的首次亮相点燃了互联网——它们帮助开发人员打破[回调地狱](http://callbackhell.com/),解决了许多困扰各地 JavaScript 程序员异步代码的问题。然而，承诺远非完美无缺。它们仍然需要回调，在复杂的情况下仍然会很混乱，而且非常冗长。

> 【2017 年 3 月 22 日:本文已经更新，以反映对规范和当前运行时支持的更改。

随着 ES6(从这里开始称为 ES2015)的出现，它不仅做出了语言原生的承诺，而不需要无数可用的库之一，我们还得到了[生成器](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function%2a)。生成器能够在函数中暂停执行，这意味着通过[将它们包装在一个实用函数](http://blog.alexmaccaw.com/how-yield-will-transform-node)中，我们能够在进入下一行代码之前等待异步操作完成。突然之间，您的异步代码看起来像是同步的了！

但这只是第一步。[异步功能](https://tc39.github.io/ecmascript-asyncawait/)将于今年作为 ES2017 规范的一部分进行标准化，原生支持已经在增长。异步函数采用了使用生成器进行异步编程的思想，并为它们提供了自己简单的语义语法。因此，您不必使用库来获得包装实用程序函数，因为这是在后台处理的。

要运行本文中的 async/await 代码示例，您需要一个兼容的浏览器。

## 运行时兼容性

在客户端，Chrome、Firefox 和 Opera 现在支持开箱即用的异步功能。

我可以使用异步功能吗？caniuse.com 主要浏览器对异步功能支持的数据。