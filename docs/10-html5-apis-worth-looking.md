# 10 个值得研究的 HTML5 APIs

> 原文：<https://www.sitepoint.com/10-html5-apis-worth-looking/>

可用于在 web 平台上创建强大应用程序的工具一年比一年好。HTML5 规范在新的 API 中添加了许多有用的特性，您可能还没有深入研究，可能是因为缺乏浏览器支持。

在这篇文章中，我们将看看 **10 个 html 5 API**，它们涵盖了一系列功能和特性，可以帮助你创建交互式网站、测试代码性能、与用户设备交互等等。

正如您将看到的，对这些特性的支持可能比您想象的要好很多。

## 1.高分辨率时间 API

[高分辨率时间 API](https://www.w3.org/TR/hr-time/) 以亚毫秒级分辨率提供当前时间，因此它不受系统时钟偏差或调整的影响。

它只公开了一个方法，属于名为`now()`的`window.performance`对象。它返回一个以毫秒为单位表示当前时间的`DOMHighResTimeStamp`。时间戳非常准确，精确到千分之一毫秒，允许对我们代码的性能进行准确的测试。

这种方法最重要的特点就是`performance.now()`是[单调递增](https://www.w3.org/TR/hr-time/#sec-monotonic-clock)(即一致递增)，所以两次调用的差值永远不会是负的。

支持 API 的浏览器有 IE10、Opera 15 和不带前缀的 Firefox 15+。Chrome 从版本 20 开始支持这个 API，带有“webkit”前缀，从版本 24 开始没有前缀。更多关于兼容性的信息请点击。

调用此方法的基本示例如下所示:

```
var time = performance.now();
```

下面是一个演示供您试验:

通过 [CodePen](http://codepen.io) 上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [HuDAx](http://codepen.io/SitePoint/pen/HuDAx) 。