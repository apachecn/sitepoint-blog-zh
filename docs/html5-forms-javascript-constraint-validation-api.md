# HTML5 表单:JavaScript 和约束验证 API

> 原文：<https://www.sitepoint.com/html5-forms-javascript-constraint-validation-api/>

在这个关于 HTML5 web 表单的三部分系列的最后一篇文章中，我们将讨论 JavaScript 集成和约束验证 API。如果你还没有这样做，请阅读[标记](/html5-forms-markup)和 [CSS](/html5-forms-css) 文章，以确保你熟悉这些概念。

HTML5 允许我们在没有任何 JavaScript 代码的情况下实现客户端表单验证。但是，在实现更复杂的表单时，我们需要增强本机验证，因为:

*   并非所有的浏览器都支持所有的 HTML5 输入类型和 CSS 选择器
*   错误消息气泡使用通用文本(“请填写此字段”)，很难设置样式
*   `:invalid`和`:required`样式在用户与表单交互之前应用于页面加载。

少量的 JavaScript 魔法和约束验证 API 可以改善用户体验。请注意，如果你想支持广泛的浏览器和输入类型，这可能会有点麻烦，我们将努力做到这一点。

## 拦截表单提交

在 HTML5 之前，客户端验证包括将一个`submit`处理程序附加到表单上，该处理程序将验证字段、显示错误并阻止提交事件。

在 HTML5 中，浏览器将首先执行自己的验证——在表单有效之前,`submit`事件不会触发。因此，如果您想做一些复杂的事情，比如显示您自己的错误、比较或自动填充字段，您必须通过将表单的`noValidate`属性设置为 true 来关闭本机验证:

```
var form = document.getElementById("myform");
form.noValidate = true;

// set handler to validate the form
// onsubmit used for easier cross-browser compatibility
form.onsubmit = validateForm;
```

当然，这意味着您必须检查代码中的字段错误，但是仍然可以利用本机浏览器验证，我们很快就会看到这一点。

## 田野。将验证属性

每个输入字段都有一个`.willValidate`属性。这将返回:

*   **true** 浏览器将对字段进行本机验证
*   **false** 当浏览器不验证字段时，或者
*   **未定义**当浏览器不支持原生 HTML5 验证时，如 IE8。

因为我们禁用了上面的本地验证，所以每个字段都将返回 false。让我们创建我们的`validateForm`处理程序，它遍历所有字段并检查本地验证是否可用:

```
function validateForm(event) {

	// fetch cross-browser event object and form node
	event = (event ? event : window.event);
	var
		form = (event.target ? event.target : event.srcElement),
		f, field, formvalid = true;

	// loop all fields
	for (f = 0; f < form.elements; f++) {

		// get field
		field = form.elements[f];

		// ignore buttons, fieldsets, etc.
		if (field.nodeName !== "INPUT" && field.nodeName !== "TEXTAREA" && field.nodeName !== "SELECT") continue;
```

该循环遍历表单的`elements`集合中的所有字段，并检查它们是输入，而不是按钮和字段集等其他类型。下一行很重要…

```
// is native browser validation available?
		if (typeof field.willValidate !== "undefined") {

			// native validation available

		}
		else {

			// native validation not available

		}
```

**假**和**未定义**都是假值，所以你不能只检查`field.willValidate`！

我们现在知道第一个块中的代码将评估何时可以使用本机验证。然而…

## 浏览器支持输入类型吗？

如果你读过[第一部分](/html5-forms-markup)，你会想起不支持的输入类型退回到`text`。例如:

```
<input type="date" name="dob" />
```

Firefox 29 或 IE11 本身不支持。这些浏览器将(有效地)呈现:

```
<input type="text" name="dob" />
```

**但是**两个浏览器都支持`text`类型的验证，所以`field.willValidate`不会返回**未定义的**！因此，我们必须检查我们的`type`属性是否匹配对象的`.type`属性——如果它们不匹配，我们需要实现遗留的回退验证，例如

```
// native validation available
			if (field.nodeName === "INPUT" && field.type !== field.getAttribute("type")) {

				// input type not supported! Use legacy JavaScript validation

			}
```

## 田野。checkValidity()方法

如果本地验证可用，可以执行`.checkValidity()`方法来验证字段。如果没有问题，该方法返回**真**，否则返回**假**。

有一个类似的`.reportValidity()`方法，它返回当前状态而不需要重新检查，尽管这不是很有用，也不是所有的浏览器都支持。

这两种方法还将:

1.  设置字段的`.validity`对象，以便可以更详细地检查错误
2.  当验证失败时，在字段上触发一个`invalid`事件。这可以用来显示错误，改变颜色等。注意没有相应的`valid`事件，所以如果必要的话记得重置错误类型和消息。

## 田野。有效性对象

`.validity`对象具有以下属性:

`.valid`–如果字段没有错误，返回**真**，否则返回**假**。
`.valueMissing`–如果该字段为必填字段，但已输入值，则返回 **true** 。
`.typeMismatch`–如果值的语法不正确，例如电子邮件地址格式不正确，则返回 **true** 。
`.patternMismatch`–如果值与`pattern`属性的正则表达式不匹配，则返回 **true** 。
`.tooLong`–如果值比允许的`maxlength`长，则返回**真**。
`.tooShort`–如果数值比允许的`minlength`短，则返回**真**。
`.rangeUnderFlow`–如果数值低于`min`则返回**真**。
`.rangeOverflow`–如果值大于`max`则返回**真**。
`.stepMismatch`–如果值与`step`不匹配，则返回**真**。
`.badInput`–如果条目无法转换为值，则返回 **true** 。
`.customError`–如果该字段有自定义错误集，则返回 **true** 。

不是所有的浏览器都支持所有的属性，所以不要做太多的假设。在大多数情况下，`.valid`或`.checkValidity()`的结果应该足以显示或隐藏错误消息。

## 支持。在旧浏览器中的有效性

您可以在传统浏览器中手动模拟`.validity`对象，例如

```
// native validation not available
			field.validity = field.validity || {};

			// set to result of validation function
			field.validity.valid = LegacyValidation(field);
```

这确保了`.validity.valid`可以在所有浏览器中测试。

## 田野。setCustomValidity()方法

`.setCustomValidity()`方法可以通过:

*   一个空字符串。这将该字段设置为有效，因此`.checkValidity()`和`.validity.valid`将返回**真值**，或者
*   包含错误消息的字符串，将显示在消息气泡中(如果使用的话)。该消息还将该字段标记为失败，因此`.checkValidity()`和`.validity.valid`将返回**假**并且`invalid`事件将被触发。

注意，您也可以使用字段的`.validationMessage`属性来检查当前消息。

## 把所有的放在一起

我们现在有了一个简单、通用的跨浏览器表单验证系统的基础:

```
var form = document.getElementById("myform");
form.noValidate = true;

// set handler to validate the form
// onsubmit used for easier cross-browser compatibility
form.onsubmit = validateForm;

function validateForm(event) {

	// fetch cross-browser event object and form node
	event = (event ? event : window.event);
	var
		form = (event.target ? event.target : event.srcElement),
		f, field, formvalid = true;

	// loop all fields
	for (f = 0; f < form.elements; f++) {

		// get field
		field = form.elements[f];

		// ignore buttons, fieldsets, etc.
		if (field.nodeName !== "INPUT" && field.nodeName !== "TEXTAREA" && field.nodeName !== "SELECT") continue;

		// is native browser validation available?
		if (typeof field.willValidate !== "undefined") {

			// native validation available
			if (field.nodeName === "INPUT" && field.type !== field.getAttribute("type")) {

				// input type not supported! Use legacy JavaScript validation
				field.setCustomValidity(LegacyValidation(field) ? "" : "error");

			}

			// native browser check
			field.checkValidity();

		}
		else {

			// native validation not available
			field.validity = field.validity || {};

			// set to result of validation function
			field.validity.valid = LegacyValidation(field);

			// if "invalid" events are required, trigger it here

		}

		if (field.validity.valid) {

			// remove error styles and messages

		}
		else {

			// style field, show error, etc.

			// form is invalid
			formvalid = false;
		}

	}

	// cancel form submit if validation fails
	if (!formvalid) {
		if (event.preventDefault) event.preventDefault();
	}
	return formvalid;
}

// basic legacy validation checking
function LegacyValidation(field) {

	var
		valid = true,
		val = field.value,
		type = field.getAttribute("type"),
		chkbox = (type === "checkbox" || type === "radio"),
		required = field.getAttribute("required"),
		minlength = field.getAttribute("minlength"),
		maxlength = field.getAttribute("maxlength"),
		pattern = field.getAttribute("pattern");

	// disabled fields should not be validated
	if (field.disabled) return valid;

    // value required?
	valid = valid && (!required ||
		(chkbox && field.checked) ||
		(!chkbox && val !== "")
	);

	// minlength or maxlength set?
	valid = valid && (chkbox || (
		(!minlength || val.length >= minlength) &&
		(!maxlength || val.length <= maxlength)
	));

	// test pattern
	if (valid && pattern) {
		pattern = new RegExp(pattern);
		valid = pattern.test(val);
	}

	return valid;
}
```

`LegacyValidation`故意保持简单；它检查`required`、`minlength`、`maxlength`和`pattern`正则表达式，但是你需要额外的代码来检查电子邮件、URL、日期、数字、范围等。

这就引出了一个问题:*如果您正在为遗留浏览器编写字段验证代码，为什么还要使用原生浏览器 API 呢？*非常好的观点！只有当您希望支持 IE6 以上的所有浏览器并提供类似的用户体验时，才需要上面的代码。这并不总是必要的…

*   简单表单可能不需要任何 JavaScript 代码。那些使用传统浏览器的人可以退回到服务器端验证——这应该**总是**被实现。
*   如果您需要更复杂的表单，但只需要支持最新的浏览器(IE10+)，您可以删除所有遗留的验证代码。如果你的表单使用了 Firefox 和 IE 目前不支持的日期，你只需要额外的 JavaScript。
*   即使您确实需要代码来检查诸如电子邮件、号码等字段。在 IE9 和更低版本中，保持简单，一旦停止支持这些浏览器就删除它。现在有点乱，但是情况会好转的。

但是请记住总是使用正确的 HTML5 字段类型。浏览器将提供本地输入控件，即使在禁用 JavaScript 的情况下也能执行更快的客户端验证。

## 分享这篇文章