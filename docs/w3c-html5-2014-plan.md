# W3C 宣布 HTML5 2014 交付计划

> 原文：<https://www.sitepoint.com/w3c-html5-2014-plan/>

如果有什么阻碍了 HTML5 的采用，那就是关于 W3C 规范状态的混乱。考虑[最新文件](https://www.w3.org/TR/html5/)；这是一份“工作草案”，上面有红色的“工作进行中”的警告。

许多开发人员声称，当指导方针处于不断变化的状态时，坚持标准是不可能的。W3C 声明最终的 HTML5 推荐标准要到 2022 年才会出台，这更是火上浇油。对许多人来说，这意味着继续使用 HTML4 或 XHTML1.0 十年。

## 2014 —新的 HTML5 完成日期

W3C 有一个新计划:HTML5.0 将在 2014 年底达到推荐状态。所有在多种浏览器中稳定实现的特性都将被最终确定并包含在规范中。

HTML5.1 将考虑新的和不稳定的功能，该版本将于 2016 年底达到推荐状态。我们可以推测，这个循环将在 2018 年延续到 HTML5.2，在 2020 年延续到 HTML5.3，以此类推。

此外，W3C 将通过模块化标准削减规范的规模和复杂性。canvas、Web Sockets 和 web storage 等技术将成为不连续的项目，不一定遵循 HTML5.x 的时间表。

## 这对我有什么影响？

在开始一个项目之前，你会参考 HTML5 规范吗？为什么？

尽管名字如此，W3C 规范并不等同于软件规范。要达到“推荐”级别，一项技术必须在两个或更多浏览器中一致实现。如果你正在构建一个新的浏览器，你应该遵循这些原则，但是对于我们其他人来说，这仅仅意味着一个特性有一个合理的支持级别。

然而，W3C 推荐标准中的特性并不意味着在每个浏览器中实现的一致性。这种说法可能会让一些人感到震惊，但是在你的项目中， **W3C 规范是不相关的**。在使用功能之前，您必须确定:

1.  所有浏览器都支持它
2.  它得到了一致的实施
3.  对于没有支持的浏览器，有一些填补或变通方法
4.  将来很可能会改变或被放弃
5.  替代技术提供了更好的选择。

考虑一些简单的东西，比如`header`、`footer`、`article`和`nav`元素。所有现代浏览器都支持这些功能。IE8 及以下版本无法识别它们，但是 [HTML5 shims](http://code.google.com/p/html5shim/) 可以帮你解决这个问题。

你真的会在 2014 年(或者 2022 年，如果你遵循旧的时间表)之前放弃`nav`元素吗？它现在还能用，虽然可能会被废弃，但同样的情况也适用于任何元素。甚至`div`有一天也会被废弃。

让我们看另一个例子:你在 JavaScript 中使用的是`innerHTML`方法吗？如果您使用的是 jQuery 或另一个库，那么您就是。尽管得到了广泛的支持，但它从未被认为是 W3C 的标准，直到 T2 的 HTML5。你会把它从你的 HTML4/XHTML1.0 项目中剥离出来吗？

HTML 在不断发展。这是一个不断变化的目标——但其他技术也是如此。W3C 规范没有提供对浏览器支持的保证，文档在发布时就已经过时了。

【2014 年计划缩短 W3C 批准时间。它正在向 WHATWG 的“T2”活 HTML 标准“T3”靠拢，但提供了人们喜欢的时间表和截止日期。所以这一切都很好——但对任何开发 HTML5 应用程序的人来说意义不大。

## 分享这篇文章