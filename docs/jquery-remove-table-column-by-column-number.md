# jQuery 删除表列(按列号)

> 原文：<https://www.sitepoint.com/jquery-remove-table-column-by-column-number/>

简单的 jQuery 代码片段**根据列号**删除整个表列。它还会删除与已删除列相关联的表格行标题。


```
//remove the 1st column
$('#table').find('td,th').first().remove();

//remove the 1st column
$('table tr').find('td:eq(1),th:eq(1)').remove();

//remove the 2nd column
$('table tr').find('td:eq(1),th:eq(1)').remove();

//remove the nth column
$('table tr').find('td:eq(n),th:eq(n)').remove();
```

## 分享这篇文章