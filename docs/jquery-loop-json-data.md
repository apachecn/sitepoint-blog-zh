# jQuery 遍历 JSON 数据

> 原文：<https://www.sitepoint.com/jquery-loop-json-data/>

遍历 JSON 数据属性的 jQuery 代码片段。你有一个对象/贴图的数组，所以外部循环遍历这些对象/贴图。内部循环遍历每个对象元素的属性。

**更新:**查看此贴中关于 jQuery.each() 的 [5 个深度例子。](http://www.jquery4u.com/jquery-functions/jquery-each-examples/)

```
$.each(data, function() {
  $.each(this, function(k, v) {
    /// do stuff
  });
});
```

## 分享这篇文章