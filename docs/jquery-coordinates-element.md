# jQuery 获取元素的坐标

> 原文：<https://www.sitepoint.com/jquery-coordinates-element/>

获取元素坐标的 jQuery 函数。

## jQuery 方法

。position()方法允许我们检索元素相对于偏移父元素的当前位置

```
var pos = $('#wrapper').position();
console.dir(pos);
//output: left: 0, top: 20
```

。offset()，它检索相对于文档的当前位置。

```
var offset = $('#wrapper').offset();
console.dir(offset);
//output: left: 70, top: 40
```

坐标转换:

```
var elem = $("#wrapper");
var x = $("#wrapper").offset().left;
var y = $("#wrapper").offset().top;
console.log('x: ' + x + ' y: ' + y);
//output: x: 70 y: 40
```

## jQuery getCoord()函数

```
jQuery.fn.getCoord = function()
{
  var elem = $(this);
  var x = elem.offset().left;
  var y = elem.offset().top;
  console.log('x: ' + x + ' y: ' + y);
  //output: x: 70 y: 40

  return {
      "x" : x,
      "y" : y
  };

  //note that it is not efficient as it breaks the jQuery chain
  //return elem;
};

$('#wrapper').getCoord();
//output: Object { x=70, y=40 }
```

## 分享这篇文章