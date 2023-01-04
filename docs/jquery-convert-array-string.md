# jQuery 将数组转换为字符串

> 原文：<https://www.sitepoint.com/jquery-convert-array-string/>

将数组转换为字符串的 jQuery 代码片段。

```
var blkstr = $.map(value, function(val,index) {
     var str = index + ":" + val;
     return str;
}).join(", ");
```

## 分享这篇文章