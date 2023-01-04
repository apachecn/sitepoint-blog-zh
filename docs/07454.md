# jQuery 选择器特定类的前 x 项

> 原文：<https://www.sitepoint.com/jquery-selector-10-items-specific-class/>

我一直在玩这个。基于指定的类获取特定数量的 DOM 元素。

**2012 年 11 月 27 日更新:**优化版本如下。

首先，像这样得到第一个和最后一个元素的基础:

```
var firstSpan = $('span.class:first'),
    lastSpan = $('span.class:last');
```

要获取与指定类匹配的所有元素，如下所示:

```
var allSpans = $('span.class').get();
```

或者像这样的第 n/x 个元素:

```
var firstSpan = $('span.class').get(0),
    secondSpan = $('span.class').get(1);
    //etc...
```

但是如何得到前 10 个元素或 10-20 个元素呢？

最好是这样:

```
var mySpans = $('span.class').get(0,10);
```

不幸的是。get()函数不允许传递一个范围，而只允许传递一个索引。所以这里尝试使用 jQuery。get()函数来包含一系列元素。

```
(function($)
{
  //function that gets a range of dom elements against a jQuery selector
  //returns an array of dom elements
  $.fn.getRange = function(start,end)
  {
    var elems = [];
    for ( var i = start; i < = end; i++ )
    {
      elems.push(this.get(i));
    }
    return elems;
  };

  //testing
  console.log($('div').getRange(1,10));
  console.log($('div').getRange(10,20));

})(jQuery);
[/js]

Does anyone know of a better way to achieve this? `Optimised versions
Thanks to Vlad, Redky and Daniel.

Using slice. 
[js]
var $el = $('div').get().slice(0,10);
console.log($el);​`
```

使用:gt :lt

```
console.log($('div:gt(3):lt(6)'));​
```

## 分享这篇文章