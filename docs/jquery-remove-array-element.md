# jQuery 删除第一个数组元素

> 原文：<https://www.sitepoint.com/jquery-remove-array-element/>

移除数组第一个元素的简单 JavaScript 代码片段。您也可以轻松地删除数组的最后一个元素。只需使用 JavaScript array.shift()和 array.pop()方法。这些方法改变了数组的长度！

```
var myarray = ["item 1", "item 2", "item 3", "item 4"];

//removes the first element of the array, and returns that element.
alert(myarray.shift());
//alerts "item 1"

//removes the last element of the array, and returns that element.
alert(myarray.pop());
//alerts "item 4"
```

**不要忘记**将 jQuery 代码放在文档就绪函数中。参见 [4 个不同的 jQuery 文档就绪示例](http://www.jquery4u.com/dom-modification/types-document-ready/)了解更多信息。

## 分享这篇文章