# jQuery 获取元素填充/边距

> 原文：<https://www.sitepoint.com/jquery-element-paddingmargin/>

今天我研究了如何获取和设置 DOM 元素的填充/边距值。我在调查外围边缘和衬垫的具体位置。您可能知道，可以使用 CSS 设置元素的填充和边距，但是获取正确的值可能会有问题。

```
//sets
$("div.header").css("margin","10px");
$("div.header").css("padding","10px");
```

根据常识，你会认为这行得通，但似乎行不通。

```
//gets
$("div.header").css("margin");
$("div.header").css("padding");
```

自己试试(将下面的代码粘贴到 Firebug 中)。您将看到为边距和填充返回的空值。也许我漏掉了什么？

```
(function($){

function logMarginPadding(elem)
{
//gets
var margin = elem.css("margin"),
padding = elem.css("padding");
console.log("margin="+margin+" padding="+padding);
}

var elem = $("div.header"); //set the element to inspect

logMarginPadding(elem);
//sets
elem.css("margin","10px");
elem.css("padding","10px");
logMarginPadding(elem);

})(jQuery);
```

这似乎行得通。

```
//get top margin of element
alert($("div.header").css("margin-top"));

//get top margin of element as integer value
alert($("a").css("margin-top").replace("px", ""));

//if using it with a calculation you must convert it to an integer
parseInt($("a").css('padding-left').replace("px", ""));
```

不管怎样，这就是我如何得到元素的填充。不理想，仅在填充均匀(每侧相同)的情况下有效，只需将宽度和[外部宽度](http://api.jquery.com/outerWidth/)除以 2。

```
var item = $('div.header');
width = item.width(),
padding = (item.outerWidth()-width)/2;
```

有一个非常流行的插件叫做 [jsizes](http://www.bramstein.com/projects/jsizes/) ，它可以很好地解决获取/设置元素的边距/填充值的问题。

> JSizes 是 jQuery JavaScript 库的一个小插件，它增加了对查询和设置 CSS 最小宽度、最小高度、最大宽度、最大高度、边框*-宽度、边距和填充属性的支持。

**进一步阅读**
jQuery 有函数 outerWidth 和 outerHeight，缺省情况下包含边框和填充，如果函数的第一个参数为真，还有边距
【http://api.jquery.com/outerHeight/】
[http://api.jquery.com/outerWidth/](http://api.jquery.com/outerWidth/)

## 分享这篇文章