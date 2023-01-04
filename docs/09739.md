# jQuery 替换字符串中的所有内容

> 原文：<https://www.sitepoint.com/jquery-replace-string/>

替换字符串中所有出现的字符(或字符串)的简单 jQuery 代码片段。它可以用来查找和替换字符串中的所有子字符串。不要忘记省略第一个参数的引号(g 代表全局，您也可以使用 I 进行不区分大小写的搜索)。

```
//global search in string
str = str.replace(/find/g,”replace”);
//OR global and case insensitive search in string
str = str.replace(/find/gi,”replace”);
```

### 用动态变量进行全局替换

```
//global replace
var regex = new RegExp('{'+fieldname+'}', "igm");
$itemForm = $itemForm.replace(regex, fieldvalue);
```

**参见:** [jQuery 通过 AJAX 读取文本文件](http://www.jquery4u.com/ajax/jquery-read-text-file/)。

## 分享这篇文章