# 不同的方式。可以使用 Alert()

> 原文：<https://www.sitepoint.com/ways-jquery-alert/>

## 什么是。警报()

有时您需要回到基础，使用。alert()函数来查看变量是什么，或者检查您的 JavaScript 实际上是否在工作。jquery 可能是使用最少的函数之一，但肯定是使用最多的。警报()。jQuery 警报也称为 jQuery 命令框或系统弹出窗口，包含显示给用户的文本信息。

## 如何使用。警报()

```
//when the webpage is loaded
jQuery(document).ready(function($) {
alert("js is working");
});
```

其他变化:

```
alert($('#test'));
// or
alert($('#test').get(0));
// also try
alert(document.getElementById('test'));
```

如果最后一个不起作用(它应该警告“HTMLElement”，这取决于浏览器)，那么 jQuery 不起作用，您可能需要检查您的导入是否正确，或者检查 JavaScript 文件中的错误。

## 分享这篇文章