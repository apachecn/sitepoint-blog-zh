# jQuery 单选按钮组获取值

> 原文：<https://www.sitepoint.com/jquery-radio-button-group/>

从单选按钮组中获取所选值的简单 jQuery 代码片段。

```
$('input:radio[name=theme]:checked').val();
```

或者当它被选中时，尝试:

```
$('input:radio[name=theme]').click(function() {
var value = $(this).val();
});
```

## 分享这篇文章