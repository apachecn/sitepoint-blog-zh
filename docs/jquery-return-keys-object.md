# jQuery 返回对象的键

> 原文：<https://www.sitepoint.com/jquery-return-keys-object/>

返回对象键的简单 jQuery 代码片段。当您希望从对象的键创建动态变量并直接引用这些值时，这可能很有用。

```
$.extend({
    keys:    function(obj){
        var a = [];
        $.each(obj, function(k){ a.push(k) });
        return a;
    }
})

// Usage:
var obj = {a: 1, b: 2, c: 3, d: 4, jquery: 'noob'}
alert($.keys(obj));    // a,b,c,d,jquery
```

## 分享这篇文章