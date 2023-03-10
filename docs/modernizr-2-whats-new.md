# Modernizr 2 的新特性

> 原文：<https://www.sitepoint.com/modernizr-2-whats-new/>

Modernizr 的第二个版本已经发布。Modernizr 可能是最著名和使用最广泛的特征检测库。除了 40 个浏览器测试和[更新的文档](http://www.modernizr.com/docs/)，还有几个新特性让 HTML5、CSS3 和 JavaScript 开发者兴奋不已…

## 条件资源加载

yepnope.js 现已整合到 Modernizr 中。它允许页面根据对一个或多个浏览器功能的支持，有条件地加载 JavaScript 或 CSS 文件。例如:

```
 Modernizr.load({
	test: Modernizr.geolocation,
	yep : "geo.js",
	nope: "geo-polyfill.js"
}); 
```

在这种情况下，支持 HTML5 地理定位的浏览器将加载 geo.js，而不支持的浏览器将加载 geo-polyfill.js，其中可能包含实现类似功能的额外代码。

更多信息，请参考[使用 Modernizr 和 Yepnope 的回归增强](https://www.sitepoint.com/regressive-enhancement-with-modernizr-and-yepnope/)。

## 媒体查询测试

响应式网页设计或[缩放布局](https://www.sitepoint.com/the-ever-increasing-uses-of-a-zoom-layout-part-2/)已经成为越来越流行的技术。本质上，网页设计使用 CSS 媒体查询来响应浏览器视窗的尺寸。例如，在移动设备上可能会显示一个单独的列，但在分辨率更高的桌面浏览器上，内容会重新分为两列。

Modernizr 2 允许您用 JavaScript 测试媒体查询，例如

```
 if (Modernizr.mq("screen and (min-width:641px)")) // do something 
```

当加载不适合在较小屏幕上显示的更多资源时，例如大横幅广告，这可能会很有用。

## 新插件 API

现在，当 Modernizr 不支持某个特性时，您可以编写自己的测试方法。基本语法是:

```
 Modernizr.addTest(str, function); 
```

让我们创建一个简单的例子来检测当前窗口是否是弹出窗口:

```
 Modernizr.addTest("popup", function(){
	return !!window.opener;
}); 
```

如果我们的页面在弹出窗口中运行，一个. popup 类被应用到 HTML 元素，Modernizr.popup 将返回 true。否则，HTML 元素将拥有类。无弹出窗口。

## 供应商前缀检测

的。prefixed()方法为浏览器支持的属性返回适当的带前缀或不带前缀的名称变量，例如

```
 var t = Modernizr.prefixed("transform"); 
```

t 的值在 webkit 浏览器中是“WebkitTransform ”,在 Firefox 4 中是“MozTransform”。有用的东西。

## 自定义构建工具

虽然这不是一个新东西，但值得一提的是 Modernizr 的[定制构建工具](http://www.modernizr.com/download/)，它允许你选择你需要的浏览器测试。这是一个很棒的特性，可以显著减小最终脚本的大小。如果您正在开发自己的 JavaScript 库，我推荐类似的模块化工具。

你用 Modernizr 吗？你现在就开始用吗？

## 分享这篇文章