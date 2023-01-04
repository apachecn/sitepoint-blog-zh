# 使用 jQuery 的 Pub 子模式。在()和上。关闭()

> 原文：<https://www.sitepoint.com/pub-pattern-jquery-on-off/>

快速浏览如何使用 jQuery 的 on 和 off 函数实现 pub sub 技术。

相关帖子:

*   [**jQuery 1.7+。上()vs .live()回顾**](http://www.jquery4u.com/jquery-functions/on-vs-live-review/)
*   [**Fascade JavaScript 设计模式**](http://www.jquery4u.com/javascript/fascade-javascript-design-pattern/)

```
/* jQuery Tiny Pub/Sub - v0.7 - 10/27/2011
 * http://benalman.com/
 * Copyright (c) 2011 "Cowboy" Ben Alman; Licensed MIT, GPL */

(function($) {

  var o = $({});

  $.subscribe = function() {
    o.on.apply(o, arguments);
  };

  $.unsubscribe = function() {
    o.off.apply(o, arguments);
  };

  $.publish = function() {
    o.trigger.apply(o, arguments);
  };

}(jQuery));
```

来源:https://gist.github.com/661855

它的工作原理和使用方法？工作实例:[https://jsfiddle.net/cowboy/HvAJf/](https://jsfiddle.net/cowboy/HvAJf/)

## 分享这篇文章