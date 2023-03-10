# jQuery 1.4 发布

> 原文：<https://www.sitepoint.com/jquery-14-released/>

虽然已经过了一段时间，但 jQuery 1.4 还是在上周发布了。注意，它还不能从主要的 jQuery 网站获得，但是你可以在 jquery14.com 的[找到它。如果你渴望得到它，以下是主要的链接:](http://jquery14.com/day-01)

*   jQuery 1.4 缩小版 (23kb)
*   [jQuery 1.4 常规](https://code.jquery.com/jquery-1.4.js) (154kb)
*   Google-hosted:[https://Ajax . Google APIs . com/Ajax/libs/jquery/1.4/jquery . min . js](https://ajax.googleapis.com/ajax/libs/jquery/1.4/jquery.min.js)
*   [jQuery 1.4 文档](http://api.jquery.com/category/version/1.4/)

## jQuery 1.4 有什么新特性？

jQuery 可能是 web 上最流行的 JavaScript 框架。许多开发人员习惯性地插入脚本(是的[W3C.org](https://www.w3.org/)，我指的是你！)，所以 jQuery 团队在创建新版本时需要极其谨慎。

为了降低复杂性和提高性能，对代码库进行了大量的重组。在[jquery14.com](http://jquery14.com/day-01)上有一些令人印象深刻的条形图，突出了优化有多好。

已经修复了 207 个错误，该框架现在通过了 IE6、IE7、IE8、Firefox 2、Firefox 3、Firefox 3.5、Safari 3.2、Safari 4、Opera 10.10 和 Chrome 的所有测试。

有大量的新方法和两个新事件:`focusin`和`focusout`。这些等同于聚焦和模糊，但是它们实现了类似事件冒泡的技术(聚焦和模糊通常不会冒泡)。例如，您可以为所有窗体附加一个处理程序，当任何内部控件获得焦点时，该处理程序将被触发，例如

```
 $("form").focusin(function(event) {
	alert("event fired!");
}); 
```

有用的东西。

## 升级了会坏什么？

jQuery 团队已经提供了一个最有可能出现问题的列表。我怀疑以下问题会引起最大的混乱:

**1。jQuery()返回以前版本库中的空集**
，运行`jQuery()`(无参数)返回`jQuery(document)`。现在它返回一个空集，尽管`jQuery().ready()`事件仍然会像预期的那样触发。

**2。Ajax 请求必须使用有效的 JSON**
如果你在 Ajax 调用中传递 JSON 数据，你必须确保它不是格式错误的。

**3。jQuery.browser 返回引擎版本**
如果你在嗅探浏览器，jQuery.browser 现在返回渲染引擎版本，而不是特定的浏览器类型，例如“webkit”而不是“chrome”。

我在之前的文章中提到过用户代理解析变得多么困难。jQuery 开发人员通过简化返回的信息降低了复杂性并提高了速度。

尽管 jQuery 仍然支持类似浏览器的检测，但我强烈建议您避免使用它，而使用特征/对象检测。

## jQuery 1.4 适合你吗？

如果您想要一个可靠的 JavaScript 库，jQuery 仍然是显而易见的选择之一。它是轻量级的、功能性的，并且支持大多数现代浏览器。对于从以前版本升级的用户，建议等待几周，以确保没有重大问题。

你试过 jQuery 1.4 吗？欢迎评论。

## 分享这篇文章