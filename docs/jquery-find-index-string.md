# jQuery 查找最后一个字符串的索引

> 原文：<https://www.sitepoint.com/jquery-find-index-string/>

快速将 jQuery 代码片段**插入到字符串中的特定索引**(或最后一个字符串)。例如，查找表格行的最后一个位置。


```
//find last position of 
 var lastRowIndex = $itemForm.lastIndexOf('');
 console.log(lastRowIndex);
```

然后，您可以将该字符串插入另一个字符串的索引中，如下所示:

```
console.log($itemForm.substring(lastRowIndex, $itemForm.length));
$itemForm = $itemForm.substring(0,lastRowIndex) + "" + data.tableRowTemplate.html() + "" + $itemForm.substring(lastRowIndex, $itemForm.length);
console.log($itemForm);
```

## 分享这篇文章