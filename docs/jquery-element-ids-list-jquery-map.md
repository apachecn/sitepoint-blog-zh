# jQuery 使用 jQuery.map()获取元素 id 列表

> 原文：<https://www.sitepoint.com/jquery-element-ids-list-jquery-map/>

这是 jQuery 的一个简单示例。map()函数。比方说，你想得到一个表单中所有复选框 id 的逗号分隔列表，你可以这样做！在[https://jsfiddle.net/CEcgL/](https://jsfiddle.net/CEcgL/)观看快速演示。

```
$(':checkbox').map(function() {
  return this.id;
}).get().join(',');
```

[官方 jQuery.map() Doco](http://api.jquery.com/map/)

## 分享这篇文章