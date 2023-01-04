# 提高 JavaScript 性能

> 原文：<https://www.sitepoint.com/improve-javascript-performance/>

嗯，有些人说避免 DOM 操作是提高 javascript 性能的关键。他们的理解相当偏颇。实际上，操作元素真的很快，直到它被添加到 DOM 中。没有必要抛开精彩的前置或附加 jQuery API，使用一些 html 技巧来获得有希望的性能。只要确保在将元素片段添加到 DOM 之前对它们进行操作。

这两个代码块有细微的差别，这是非常重要的。

```
//adds element to DOM and *then* does the manipulation
$('&amp;lt;div /&amp;gt;').appendTo(someElement).doSomeManipulation();

//manipulates the element fragment *before* adding to the DOM
$('&amp;lt;div /&amp;gt;').doSomeManipulation().appendTo(someElement);
```

知道元素何时被添加到 DOM 中是非常重要的，但是一旦它们被放置，就要知道如何操作它们。

在将元素添加到 DOM 之前，通过执行几乎所有的工作，您可以对 javascript 性能做出惊人的调整。只需对链调用进行重新排序，就能看到这一技术对 javascript 性能的惊人改进。

最后，这并不是解决所有 javascript 性能问题的必要全局解决方案。在用 jQuery 操作元素之前，有时可能确实需要将元素插入 DOM，但是值得一试，看看这是否适用于您的应用程序。

## 分享这篇文章