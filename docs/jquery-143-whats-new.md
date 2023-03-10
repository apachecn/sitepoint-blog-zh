# jQuery 1.4.3 的新特性

> 原文：<https://www.sitepoint.com/jquery-143-whats-new/>

jQuery 1.4 的第三个次要版本现已发布。流行的 JavaScript 库获得了许多额外的方法、错误修复和速度改进——以下是吸引我眼球的新特性…

## jQuery.type

在 JavaScript 中确定类型可能很棘手。一切都是对象，所以你需要特别小心标准类型的函数。jQuery.type 将使开发者的生活变得更加轻松…

```
 $.type(true) === "boolean"
$.type(3) === "number"
$.type("test") === "string"
$.type(function(){}) === "function"
$.type([]) === "array"
$.type(new Date()) === "date"
$.type(/test/) === "regexp" 
```

## HTML5 数据属性

jQuery 现在支持 HTML5 数据属性，例如

```
 <div id="info" data-site="SitePoint" data-registered="true" data-options="{'name':'user'}" /> 
```

jQuery 将值转换成它们的本地 JavaScript 类型，以便可以访问和更新数据，例如

```
 $("#info").data("site") === "SitePoint";
$("#info").data("registered") === true;
$("#info").data("options").name === "user"; 
```

## 埃阿斯

jQuery.support.ajax 是一个新属性，在支持 XMLHttpRequest 的浏览器中返回 true。这在桌面上很少出现问题，但是 XHR 在移动设备上的可用性更差。

还添加了 jQuery.readyWait 属性。这会延迟就绪事件的执行，以便您可以在它被触发之前加载依赖项或执行其他操作。

## 事件

现在可以防止任何元素上的默认动作和冒泡，方法是:

```
 $("a#link").bind("click", false); 
```

同样，。unbind("click "，false)将删除该操作。

## 动画和效果

新的 jQuery.fx.interval 属性设置或获取动画帧速率。默认值为 13 毫秒，但为了让动画更流畅，可以降低该值(假设您的浏览器能够跟上)。

## DOM 遍历

jQuery 更多地使用了本地的 querySelectorAll 和 matchesSelector 方法(如果它们可用的话)。一些函数现在比 1.4.2 版快 8 倍。

## CSS 模块

CSS 模块已经被重写，因此可以编写扩展的自定义插件。css()和。动画()。

## 链接

从以下网址获取 jQuery 1.4.3:

*   未压缩版本(179 kb)–[https://code.jquery.com/jquery-1.4.3.js](https://code.jquery.com/jquery-1.4.3.js)
*   缩小版(26kB)–[https://code.jquery.com/jquery-1.4.3.min.js](https://code.jquery.com/jquery-1.4.3.min.js)
*   Google CDN-[https://Ajax . Google APIs . com/Ajax/libs/jquery/1 . 4 . 3/jquery . min . js](https://ajax.googleapis.com/ajax/libs/jquery/1.4.3/jquery.min.js)
*   jQuery 1.4.3 文档—[http://api.jquery.com/category/version/1.4.3/](http://api.jquery.com/category/version/1.4.3/)

令人印象深刻的是，jQuery 保持了良好的向后兼容性。核心 API 是稳定的，不太可能破坏您现有的应用程序。John Resig 的团队不断完善他们的代码，使其更快、更精简、更灵活。

## 刚刚收到的消息…

[jQuery Mobile 1.0 Alpha 1 已经发布](http://jquerymobile.com/)！请继续阅读 SitePoint 了解更多信息…

## 分享这篇文章