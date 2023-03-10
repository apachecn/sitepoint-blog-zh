# 简单来说就是 JavaScript:核心库

> 原文：<https://www.sitepoint.com/simply-javascript-the-core-library/>

[![Simply JavaScript product shot](img/36317755f22aa78c8f0add4ee6453bec.png)](https://www.sitepoint.com/store/simply-javascript/)

随着 [Simply JavaScript](https://www.sitepoint.com/store/simply-javascript/) 的第一次印刷几乎售罄，第二次印刷正在进行中，是时候做一些我和 Cameron 第一次构思这本书时就一直期待的事情了:**将核心库开源。**

**下载:**[core . js](https://github.com/spbooks/javascript1/raw/master/ajax/core.js)(1.0 版本，2007-08-29)

正如我之前在[Simple JavaScript:有多简单才算太简单？](/blogs/2007/07/11/javascript-how-simple-is-too-simple/)这本书采取了一种独特的方法，从第一页就开始教授**不唐突的脚本**。它通过使用自己的 JavaScript 库(核心库)来隐藏 JavaScript 事件处理和其他繁琐任务的一些本质细节，直到读者跟上速度。现在，我很高兴让这个图书馆对所有人开放——不仅仅是这本书的所有者。

## 另一个图书馆？

你会问，为什么要这样做？毕竟，还有很多其他好的 JavaScript 库，从苗条的( [base2](http://code.google.com/p/base2/) ， [jQuery](http://jquery.com/) )到臃肿的( [YUI](http://developer.yahoo.com/yui/) ， [Dojo](http://dojotoolkit.org/) )。真的需要另一个吗？

所有这些库都出色地将 JavaScript 变成了一种更强大、更有特色的语言。它们已经被调整了一次又一次，以获得最佳的性能和最小的开销，这就是为什么我们在书中对它们进行了如此多的介绍。

但这不是核心库的目的。更确切地说，核心库的设计是为了**使 JavaScript 成为一种更容易学习的语言**，它消除了初学者通常会犯的错误。通过在极其宽松的[麻省理工学院许可](http://en.wikipedia.org/wiki/MIT_License)下发布核心库，我们希望帮助初学者开始学习 JavaScript——不管他们是否购买我们的书来学习。

## 我如何使用它？

核心库的最佳用户指南(当然)是我们的书，[简单来说就是 JavaScript](https://www.sitepoint.com/store/simply-javascript/) 。它不仅塞满了使用这个库的例子，还包含了一个详细的附录，一行一行地解释了这个库是如何工作的。也就是说，这里有一个核心库及其使用的快速浏览。

要使用 Core 库，您必须下载 core.js 文件并将其放在您的站点上，然后在任何页面上使用一个`<script>`标签加载它，该标签位于将使用它的 JavaScript 代码之前:

```
<head>
  ...
  <script type="text/javascript" src="core.js"></script>
  <script type="text/javascript" src="yourscript.js"></script>
</head>
```

加载核心库后，您可以使用以下模式编写不引人注目的 JavaScript 代码:

```
var YourScript =
{
  init: function()
  {
    // Your start-up code here
  },
  // Additional methods here
};

Core.start(YourScript);
```

最后一行的`Core.start`方法将处理所有细节，以确保一旦包含脚本的页面完成加载，就调用脚本的`init`方法。

核心库已经过当前最流行的浏览器测试，包括:

*   火狐 2.0
*   Internet Explorer 6.0 和 7.0
*   Safari 2.0
*   歌剧 9.23

预计它还能与 Internet Explorer 5.5 和 Firefox 1.5 兼容。

## 它还能做什么？

同样，在[简单 JavaScript](https://www.sitepoint.com/store/simply-javascript/) 的附录 A 中可以找到很多细节，但是这里有一个核心库提供的功能的快速运行:

**事件监听器**

`Core.start(ScriptObject)`

As shown above, calls `ScriptObject.init` when the page has loaded.

`Core.addEventListener(target, type, listener)`

Registers the function `listener` to be called when a `type` (e.g. `"click"`) event occurs on `target` or one of its descendants. Equivalent to `target.addEventListener(type, listener, false)` in browsers that support DOM2 Events.

`Core.removeEventListener(target, type, listener)`

Unregisters the function `listener` so that it will not be called when a `type` (e.g. `"click"`) event occurs on `target` or one of its descendants. Equivalent to `target.removeEventListener(type, listener, false)` in browsers that support DOM2 Events.

`Core.preventDefault(event)`

Cancels the default action associated with the given `event` object.

`Core.stopPropagation(event)`

Prevents ancestors of the current element from receiving notification of the given `event`.

**CSS 类管理**

`Core.addClass(target, className)`

Adds the specified class to the existing classes (if any) applied to the `target` element.

`Core.getElementsByClass(className)`

Returns an array of all elements in the document that have the specified class applied to them.

`Core.hasClass(target, className)`

Returns `true` if the specified class has been applied to the `target` element, `false` if not.

`Core.removeClass(target, className)`

Removes the specified class from the list of classes applied to the `target` element.

**计算样式**

`Core.getComputedStyle(target, styleProperty)`

Retrieves the effective value of the specified CSS property once all the various sources of CSS styles have been applied to the `target` element.

## 等一下…真糟糕！

如果您在核心库的代码中看到一些不太符合标准的东西，或者如果您觉得库中缺少一个重要的特性，我会很乐意考虑您的任何改进建议。要么在这里发表评论，要么给我写封短信到凯文·sitepoint.com。

但是，请记住，这个库的目的只是为了消除让初学者难以学习的 JavaScript 的粗糙部分。例如，我对向核心库添加 CSS 选择器 API 不感兴趣，因为有很多好的库可以向 JavaScript 添加功能。同样，性能优化对我来说并不特别有趣，除非它们不会使代码更难理解。毕竟，在核心库的辅助下学习 JavaScript 的最后一步是能够阅读核心库并理解所有代码的工作原理！

## 我能用这个做什么？

麻省理工学院的许可证允许你对这个库做任何你想做的事情，包括用它出版一本与之竞争的 JavaScript 书(顺便说一句，祝你好运)。当您在自己的项目中使用本库时，本库要求您通过留下包含在本库中的许可声明来给予我们信任。

我们绝不想独享这个库，而是希望它成为向初学者教授 JavaScript 的实际起点。如果您以任何方式使用核心库，请留下评论让我们知道！

## 分享这篇文章