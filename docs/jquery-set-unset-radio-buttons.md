# jQuery 设置+取消设置单选按钮

> 原文：<https://www.sitepoint.com/jquery-set-unset-radio-buttons/>

设置和取消单选按钮输入值的简单 jQuery 代码片段。我只是想分享一下，因为我在使用早期版本的 jQuery 和设置表单上的单选按钮时遇到了一些问题。

```
//jQuery Set Radio Button
$('input[name="correctAnswer"]').attr('checked', false);

//To unselect the button
$("[name=color]").removeAttr("checked");

//jQuery (1.4+) code to pre-select one button :
var presetValue = "black";
$("[name=color]").filter("[value="+presetValue+"]").attr("checked","checked");
```

## 分享这篇文章