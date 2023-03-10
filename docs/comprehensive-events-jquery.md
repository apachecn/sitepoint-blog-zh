# 全面了解 jQuery 中的事件

> 原文：<https://www.sitepoint.com/comprehensive-events-jquery/>

*这篇文章由 [Wern Ancheta](https://www.sitepoint.com/author/wancheta) 和 [Camilo Reyes](https://www.sitepoint.com/author/creyes/) 进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

几乎所有网页中的用户交互都可以在 jQuery 中作为事件捕获。事件之所以重要，是因为它们允许您根据用户的操作做出适当的响应。例如，您可以编写代码，根据按钮单击或滚动事件来更改网页的背景颜色。

jQuery 有很多快捷的方法，比如`contextmenu()`、`hover()`和`keyup()`，来处理不同的事件。除了专用方法之外，jQuery 还提供了一个通用的`on`方法，允许您为任何事件附加一个处理程序:`on('eventName', handler)`。请记住，这些方法只是标准 DOM 事件的包装器，您可以用普通的 JavaScript 为这些事件添加处理程序。

在本教程中，我们将快速浏览所有这些事件方法，分为五大类，并讨论使用它们时应遵循的最佳实践。

## 浏览器事件

这个类别中有三个事件。这些是`error`、`resize`和`scroll`。当图像等元素没有正确加载时，触发`error`事件。从 jQuery 版开始，它的快捷方式就被弃用了，所以现在应该使用`on('error', handler)`来代替。

### Resize 事件

每当浏览器窗口的大小发生变化时，就会触发此事件。不同的浏览器可以根据实现不同地调用 resize 处理程序。Internet Explorer 和基于 WebKit 的浏览器持续调用处理程序，而 Opera 等浏览器只在 resize 事件结束时调用它。

下面的代码片段根据窗口宽度交换图像`src`。

```
$(window).resize(function() {
  var windowWidth = $(window).width();
  if (windowWidth <= 600) {
    $("img").attr("src", "image-src-here.jpg");
    // Image src changed at this point.
  }
}); 
```

这个 CodePen 演示展示了实际发生的事件:

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看到笔[的 resize 事件](http://codepen.io/SitePoint/pen/wGedMp/)。