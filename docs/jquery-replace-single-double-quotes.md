# jQuery 替换单引号或双引号

> 原文：<https://www.sitepoint.com/jquery-replace-single-double-quotes/>

使用带有两个参数的 jQuery.replace 函数替换单引号和双引号的简单 jQuery 代码片段。首先对所有引号(单引号或双引号)进行全局搜索，然后用一个空格替换它们(如果您希望用其他内容替换它们，请更改第二个参数——这段代码只是删除它们)。

```
//replace all single quotes
var myStr = myStr.replace(/'/g, '');

//replace all double quotes
var myStr = myStr.replace(/"/g, '');

//or abit of fun, replace single quotes with double quotes
var myStr = myStr.replace(/'/g, '"');

//or vice versa, replace double quotes with single quotes
var myStr = myStr.replace(/"/g, ''');
```

## 分享这篇文章