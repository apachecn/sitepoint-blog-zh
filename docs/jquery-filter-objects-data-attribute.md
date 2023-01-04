# jQuery 根据数据属性值过滤对象

> 原文：<https://www.sitepoint.com/jquery-filter-objects-data-attribute/>

如何使用 jQuery 根据数据属性值过滤元素。该代码片段获取 id 以“proto_”开头并且数据属性为“state”且值为“open”的所有 div。

```
var $el = $('div[id^=proto_]').filter(function()
{
     return ($(this).data("state") == "open")
});
console.log($el);
```

我在 jsfiddle 中做了一个快速演示来演示。[https://jsfiddle.net/qgwnY/](https://jsfiddle.net/qgwnY/)

## 优化版本

谢谢弗拉德。

```
var $el = $('div[id^=proto_]').filter('div[data-state=open]').css('color','red');​
```

请在[js dild](https://jsfiddle.net/qgwnY/7/)上查看。

## 分享这篇文章