# jQuery 动态添加表单元素

> 原文：<https://www.sitepoint.com/jquery-dynamically-add-form-elements/>

如何使用 jQuery/JavaScript 动态添加表单元素？当 DOM 已经加载完毕，并且您需要添加一个额外的输入字段(比如说，基于用户的操作或选择)时，这非常有用。您可以创建任何新的表单元素，而不仅仅是输入字段。或者，您也可以在页面中创建输入，简单地隐藏它们，然后在需要时显示它们。

示例:更改密码 1 时，在名为“密码 1”的输入框后插入一个名为“密码 2”的新输入框。

```
//change event on password1 field to prompt new input
$('#password1').change(function() {
		//dynamically create new input and insert after password1
		$("#password1").append("<input type="text" name="password2" id="password2">");
});
```

## 分享这篇文章