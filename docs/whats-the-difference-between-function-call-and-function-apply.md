# function.call 和 function.apply 有什么区别？

> 原文：<https://www.sitepoint.com/whats-the-difference-between-function-call-and-function-apply/>

![Butterfly](img/ce10557a553518bd34c9a6cc1338ed46.png "Butterfly")今天我读了 Mark Needham 的一篇博客，标题是 *[ JavaScript:混淆‘调用’和‘应用’](http://www.markhneedham.com/blog/2010/02/28/javascript-confusing-call-and-apply/)*。不久前，我为我们的 [*科技时报*时事通讯](https://www.sitepoint.com/newsletter/)写了一篇关于 JavaScript `arguments`对象的文章。它被称为`arguments`:一种奇怪的 JavaScript。因为它有各种有趣的行为，了解这些行为很有用。我意识到尽管我在那篇文章中同时使用了 [`call`](https://developer.mozilla.org/en/Core_JavaScript_1.5_Reference/Objects/Function/call) 和 [`apply`](https://developer.mozilla.org/en/Core_JavaScript_1.5_Reference/Objects/Function/apply) ，但我并没有谈到它们的区别。

这其实很简单。首先，两种方法都期望 a `thisArg`作为第一个参数。这是为函数提供上下文的参数；它确定被调用或应用的函数中 JavaScript 关键字`this`的值。唯一的区别是，`call`方法要求单独指定参数；`apply`方法将它们作为一个数组。如果你看到语法就更清楚了:

```
function.call(thisArg[, argument1[, argument2[, ...]]]);
```

```
function.apply(thisArg[, argumentArray]);
```

因此，如果您在 JavaScript 中使用`arguments`对象，您可以通过使用 apply 方法调用任何函数，并简单地将现有的`arguments`对象作为数组参数传入。

希望这对你有用！

*[由 Sudhamshu](http://www.flickr.com/photos/sudhamshu/3344816160/) 拍摄的特写图像。你能理解其中的意义吗？*

## 分享这篇文章