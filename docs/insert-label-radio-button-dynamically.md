# 动态插入单选按钮的标签

> 原文：<https://www.sitepoint.com/insert-label-radio-button-dynamically/>

动态插入单选按钮标签的简单 jQuery 代码片段。您可以创建一个标签元素并将其插入单选按钮之前，如下所示:

```
var labelText = $('#radioBtnId').attr('name');
$('<label for="' + labelText + '">' + labelText + '</label>').insertBefore('#radioBtnId');
```

## 分享这篇文章