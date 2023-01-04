# jQuery 1.8 测试版——你需要知道的

> 原文：<https://www.sitepoint.com/jquery-1-8-beta-1-whats-coming-and-going/>

![jquery 1.8 beta](img/814d75569e2fca9ccdff3e65e3c5d975.png "jquery 1.8 beta")

jQuery 核心团队一直在埋头开发 jQuery 1.8，我们的第一个测试版现在已经发布了！您可以从 jQuery CDN 获得代码。

### 查看代码

[https://code . jquery . com/jquery-1.8 B1 . js](https://code.jquery.com/jquery-1.8b1.js "https://code.jquery.com/jquery-1.8b1.js")

### 重要的东西

**定制版本**

> 从 jQuery 1.8 开始，如果您不需要一个或多个模块的功能，您可以构建一个定制版本的 jQuery 来排除它们。

**改进的浏览器特定 CSS**

> jQuery 1.8 稍微减轻了痛苦。我们自动获取无前缀的属性名，并生成适合当前浏览器的前缀，所以您不必这样做。比如 Chrome 上的 jQuery 调用$(“# my scroll”)。css("marquee-direction "，" backward ")将 css 设置为-WebKit-marquee-direction:backwards。

**新美元。动画功能**

> 如果您需要支持没有内置动画的旧浏览器，新的$。动画提供了一个坚实的基础，并修复了以前版本的许多错误。

**改进的 SIZZLE CSS 选择器引擎**

> Sizzle 处理了更多的边缘情况和错误，包括改进了对多个组合符(~ > +)的支持，以及在 querySelectorAll 中更好地检测浏览器错误。

**新美元。parseHTML()函数**

> jQuery 1.8 引入了一个新方法:$.parseHTML。它允许您将字符串指定为 HTML，并知道它们将被解析为 HTML，这是$()无法做到的，因为它也将字符串解释为选择器。

```
// data: string of html
// context (optional): If specified, the fragment will be created in this context, defaults to document
// scripts (optional): If true, will include scripts passed in the html string
parseHTML: function( data, context, scripts ) {
```

**$。浏览器还在 1.8**

> 从 jQuery 1.9 开始，我们将完全删除它，你将需要使用 1.9 compat 插件。

**异步选项仍然存在**
关于从 1.9 版本中移除它的讨论，因为它会挂起 JavaScript 的加载。[http://bugs.jquery.com/ticket/11013](http://bugs.jquery.com/ticket/11013)它仍然默认为真；

> #11013:从$中弃用/删除异步选项。创建交互式、快速动态网页应用的网页开发技术

**$。大小不赞成**
[【http://bugs.jquery.com/ticket/10657】](http://bugs.jquery.com/ticket/10657)的说法。size()方法在功能上等效于。长度属性；然而。属性是首选的，因为它没有函数调用的开销。使用。长度()。$.size()是$的别名。长度()。

> #10657:反对/删除 jQuery#size()，支持 jQuery#length

[阅读官方博文](http://blog.jquery.com/2012/06/22/jquery-1-8-beta-1-see-whats-coming-and-going/ "JQUERY 1.8 BETA 1: SEE WHAT’S COMING (AND GOING!)")

## 分享这篇文章