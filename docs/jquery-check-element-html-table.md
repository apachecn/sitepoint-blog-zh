# jQuery 检查元素是否在 html 表中

> 原文：<https://www.sitepoint.com/jquery-check-element-html-table/>

这个代码片段递归地搜索 input 元素的父元素的 DOM，以找到一个 table 元素。


```
//filter parents by HTML table tag
$('.item :first :input[name="code"]').parents('table')

//conditional, not found parent table element
($('.item :first :input[name="code"]').parents('table').length == 0)
```

如果找到，长度传回 1，如果没有找到，传回 0。

## 分享这篇文章