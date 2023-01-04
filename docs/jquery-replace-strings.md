# jQuery 替换页面上的所有文本

> 原文：<https://www.sitepoint.com/jquery-replace-strings/>

用任何给定的字符串替换所有文本的简单 jQuery 代码片段。可能有助于根据可能变化的动态变量来更改公司名称。

**重要提示:**不要忘记 replace()函数返回值，并且不在页面上进行实时更新。为了做到这一点，您只需将。替换()。

```
//ie to replace all dots on page with hyphens
var replace_str = $('body').html().replace(/./g,'-');
$('body').html(replace_str);

//if that doesn't work try this
var strNewString = $('body').html().replace(/./g,'---');
$('body').html(strNewString);
```

## 分享这篇文章