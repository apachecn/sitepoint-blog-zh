# 使用 HTML5 约束 API 进行表单验证

> 原文：<https://www.sitepoint.com/using-the-html5-constraint-api-for-form-validation/>

对于许多开发人员来说，验证 web 表单一直是一项痛苦的任务。以一种用户友好和开发人员友好的方式执行客户端验证真的很难。此外，以令人愉快的方式通知用户验证错误是一项繁琐的任务。HTML5 约束验证 API 帮助开发人员避免使用 JavaScript 进行简单的验证。

对于复杂的验证逻辑，API 可以用来非常容易地执行客户端验证和显示错误消息。在本教程中，我将向您概述 HTML5 约束验证 API，并讨论如何在您的项目中使用它来创建更好的 web 表单。

在进一步了解之前，请查看这张[兼容性表](http://caniuse.com/#search=form%20validation)，以确定您的浏览器支持哪些功能。请注意，尽管 HTML5 约束验证 API 提供了一种很好的验证表单字段的方式，但是服务器端的验证必须始终完成。

## 基本约束验证

可以通过为输入元素的`type`属性选择最合适的值来执行基本验证。例如，您可以通过编写以下 HTML 来验证电子邮件:

```
<input type=”email” />		//The field value must be an email
```

您可以通过编写以下标记来验证 URL:

```
<input type=”URL” />			// The field value must be a URL
```

通过使用`email`或`url`作为`type`属性的值，当提交表单时，会自动添加一个约束并自动验证字段。如果发生任何验证错误，浏览器还会以非常用户友好的方式显示一条错误消息。

您还可以在表单中使用几个基于验证的属性。以下是可用于实现基本约束的一些属性:

1.  **pattern**: The pattern attribute is used to specify a regular expression and the field value must match this pattern. This attribute can be used with input types like `text`, `password`, `email`, `url`, `tel` and `search`.
    For example, The following HTML snippet uses a `pattern` attribute for an input field.

    ```
    <input type=”text” pattern=”[1-4]{5}” />
    ```

    提交表单时，将对输入字段进行验证。因此，在这种情况下，像 ABCD 这样的值不会通过验证。

2.  **required**: A `required` attribute indicates that a value must be specified for the input element.

    ```
    <input type=”text” required />
    ```

    上面的代码片段利用了`required`属性。如果您将该字段留空并尝试提交表单，将会出现验证错误。

3.  **maxlength**: This is an integer value that specifies the maximum number of characters allowed for a particular input field.

    ```
    <input type=”text” maxlength=”20” />
    ```

    上面的代码片段为输入字段增加了一个上限。在此输入元素中输入的值必须少于 20 个字符。

4.  **最小&最大**:顾名思义，`min`和`max`属性分别指定了输入元素的下限和上限。

## 处理复杂的约束

使用 HTML5 约束 API 可以轻松处理复杂的验证逻辑。例如，您可以有一个密码字段和一个确认密码字段。您需要确保在提交时两个字段中的值是相同的。否则，应该向用户显示一条错误消息。这实际上可以用 HTML5 约束 API 非常容易地完成。

首先，我们需要给密码字段附加一个`onchange`监听器。下面的代码片段显示了 HTML 表单。

```
<form name="ValidationForm">
	Password: <input type="password" id="password1"/>
	Confirm Password:<input type="password" id="password2"/>
	<input type="submit" value="submit"/>
</form>
```

因为在所有字段都被完全验证之前不会有`submit`事件，所以确实没有办法知道表单何时被提交。这就是我们对`change`事件感兴趣的原因。每当触发`change`事件时，我们需要检查两个密码是否匹配。如果是，我们在 input 元素(本例中是密码字段)上调用`setCustomValidity()`，使用一个空字符串作为参数。

这意味着密码字段被标记为有效，因此提交表单时不会出现验证错误。另一方面，如果我们在一个`change`事件中检测到密码不匹配，我们调用`setCustomValidity()`,并以一条错误消息作为参数。这意味着密码字段将被标记为无效，并且当用户提交表单时将显示错误消息。

以下 JavaScript 实现了这一逻辑:

```
<script type="text/javascript">
window.onload = function () {
	document.getElementById("password1").onchange = validatePassword;
	document.getElementById("password2").onchange = validatePassword;
}
function validatePassword(){
var pass2=document.getElementById("password2").value;
var pass1=document.getElementById("password1").value;
if(pass1!=pass2)
	document.getElementById("password2").setCustomValidity("Passwords Don't Match");
else
	document.getElementById("password2").setCustomValidity('');	 
//empty string means no validation error
}
</script>
```

使用上述方法的最大好处是，您不必担心如何向用户显示错误消息。您只需要用适当的参数调用一个简单的方法——`setCustomValidity()`——就会相应地显示错误消息。

## 结论

您可以使用 HTML5 约束验证 API 实现许多简单到高级的约束。

该 API 提供了一套庞大的工具来定制验证过程。我们刚刚讨论了 API 的一部分。

要了解 CSS 钩子、有效性状态等更高级的概念，请查看 Mozilla 的教程。

## 分享这篇文章