# jQuery toUpperCase/toLowerCase 示例

> 原文:# t0]https://www . sitepoint . com/jquery-touppercase-tolowercase/

将文本转换为大写和小写的 JavaScript 代码段。为您的娱乐提供的简单示例–显示表单值表单输入的大小写变化。

参见:[jQuery title caps 函数](http://www.jquery4u.com/jquery-functions/jquery-titlecaps-function/)

```
jQuery('form').submit(function() {
    jQuery('input#value').val(function(i, val) {
          return val.toUpperCase();
           return val.toLowerCase();
    });
 });
```

## 分享这篇文章