# jQuery 克隆表行并使其为空

> 原文：<https://www.sitepoint.com/jquery-clone-table-row-empty/>

快速 jQuery 代码片段**克隆一个追加到表中的表行**(并清空)。

```
var clonedRow = $('tbody tr:first').clone();
clonedRow.find('input').val('');
$(this).prev().find($('table tbody')).append(clonedRow);
```

## 分享这篇文章