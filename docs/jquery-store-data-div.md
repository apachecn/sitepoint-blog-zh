# jQuery 将数据存储在 Div 中

> 原文：<https://www.sitepoint.com/jquery-store-data-div/>

jQuery 代码片段将数据存储在一个 div 中，供以后引用。数据没有显示在 div 中，而是存储在元素中。请注意，如果 jQuery 集合引用多个元素，则返回值将引用第一个元素。

这是你怎么做的。

```
//set the value to be stored
$("div").data("valuename", "hello");

//get the stored value
var value = $("div").data("valuename");
//outputs "hello"
alert(value);

//you can also change the value
$("div").data("valuename", 101);

//get the new stored value
var value = $("div").data("valuename");
//outputs "101"
alert(value);
```

您可以使用 removeData(valuename)来删除存储在 div 中的数据项。

## 分享这篇文章