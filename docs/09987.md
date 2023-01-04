# jQuery 防止多表单提交

> 原文：<https://www.sitepoint.com/jquery-prevent-multiple-form-submit/>

对于某些表单来说，防止意外地在一个表单上多次提交可能很有用。这段代码绑定了 Submit 事件并存储了 disabledOnSubmit”数据，因此下次事件“submit”触发时将返回 false。所有提交按钮也被禁用，因此表单不能提交两次。

```
$(document).ready(function() {
  $('form').submit(function() {
    if(typeof jQuery.data(this, "disabledOnSubmit") == 'undefined') {
      jQuery.data(this, "disabledOnSubmit", { submited: true });
      $('input[type=submit], input[type=button]', this).each(function() {
        $(this).attr("disabled", "disabled");
      });
      return true;
    }
    else
    {
      return false;
    }
  });
});
```

## 分享这篇文章