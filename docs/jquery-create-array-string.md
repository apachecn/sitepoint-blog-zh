# jQuery 从字符串创建数组

> 原文：<https://www.sitepoint.com/jquery-create-array-string/>

从字符串创建数组的简单 jQuery 代码片段。使用类似 PHP explode()方法的 jQuery split()命令将字符串转换成数组。

```
var numbersString = "1,2,3,4,5,6";
var numbersArray = numbersString.split(',');
```

然后你可以像这样循环数组的值:

```
$.each(numbersArray, function(index, value) { 
  alert(index + ': ' + value);
});
```

您还可以像这样联接数组项目:

```
var numbersArray = new Array("1", "2", "3", "4", "5", "6");
var numbersString = numbersArray.join(',');
alert(numbersString); 
//alerts 1,2,3,4,5,6
```

**不要忘记**将 jQuery 代码放在文档就绪函数中。参见 [4 个不同的 jQuery 文档就绪示例](http://www.jquery4u.com/dom-modification/types-document-ready/)了解更多信息。

## 分享这篇文章