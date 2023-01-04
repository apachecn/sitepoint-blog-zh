# jQuery 获取选中复选框 id 的数组

> 原文：<https://www.sitepoint.com/jquery-array-checked-checkboxes-ids/>

使用 jQuery **获取选中复选框 id 的数组**。


```
var checkedIds = Array();
$('form:input[type="checkbox"][checked]').each( function(i,v)
{
    checkedIds.push($(v).attr('id'));
});
console.log(checkedIds);
```

## 分享这篇文章