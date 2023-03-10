# jQuery 验证仅在表单提交时验证

> 原文：<https://www.sitepoint.com/jquery-validation-validate-form-submit/>

如果你使用 jQuery 验证插件，当你在输入框中输入时，你可能会看到一个断断续续的验证。当您的**使用定制的验证规则来触发 ajax 请求以验证用户输入**时，这是最常见的，比如检查用户的电子邮件在数据库中是否是唯一的。口吃=不好。

要删除一致性验证检查**，向表单验证函数**添加以下参数:

```
onkeyup: false,
   onclick: false,
   onfocusout: false,
```

因此，您的验证函数可能如下所示:

```
$("#form").validate(
{
   onkeyup: false,
   onclick: false,
   onfocusout: false,

   //validation rules
   rules: {

       ...

   },

   //validation messages
   messages: {

       ...

   },

   //submit handler
   submitHandler: function(form)
   {

       ...

   }

})
```

## 其他美元。验证()选项

```
messages: {},
groups: {},
rules: {},
errorClass: "error",
validClass: "valid",
errorElement: "label",
focusInvalid: true,
errorContainer: $( [] ),
errorLabelContainer: $( [] ),
onsubmit: true,
ignore: ":hidden",
ignoreTitle: false,
onfocusin: function(element, event) { ... },
onfocusout: function(element, event) { ... },
onkeyup: function(element, event) { ... },
onclick: function(element, event) { ... },
highlight: function(element, errorClass, validClass) { ... },
unhighlight: function(element, errorClass, validClass) { ... }
```

## 分享这篇文章