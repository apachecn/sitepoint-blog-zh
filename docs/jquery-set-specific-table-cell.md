# jQuery 在特定表格单元格上设置值

> 原文：<https://www.sitepoint.com/jquery-set-specific-table-cell/>

Helper JS 函数，**根据表格 id、行 id 和列号设置指定表格单元格**的价格。


```
function setRowPrice(tableId, rowId, colNum, newValue)
{
    $('#'+table).find('tr#'+rowId).find('td:eq(colNum)').html(newValue);
};
```

## 分享这篇文章