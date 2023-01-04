# 如何用 JavaScript 编写一个通用的表单更新检测函数

> 原文：<https://www.sitepoint.com/javascript-form-change-checker/>

在我的上一篇文章中，我们发现了如何检查单个表单元素是否被用户更改。今天，我们将使用这些信息来编写 JavaScript 代码，它可以检测任何表单的更新。

以下是示例和代码链接:

*   [代码演示页面](https://blogs.sitepointstatic.com/examples/tech/formchanges/index.html)
*   [FormChanges() JavaScript 代码](https://blogs.sitepointstatic.com/examples/tech/formchanges/formchanges.js)
*   [所有代码和示例的压缩文件](https://blogs.sitepointstatic.com/examples/tech/formchanges/formchanges.zip)

## 我们的先决条件

由于我们都是优秀的开发人员，我们将在削减任何代码之前定义我们的需求:

*   我们将编写一个函数 FormChanges()，它接受一个重载的表单参数——表单的 DOM 节点或字符串 ID。
*   该函数将返回用户已经更改的表单元素节点的数组。这使我们能够确定哪些字段发生了更改，或者如果数组为空，则确定没有字段发生更改。
*   如果找不到表单，该函数将返回 NULL。
*   我们不依赖于任何特定的 JavaScript 库，所以这个函数与它们都是兼容的。
*   它必须能在所有现代浏览器上运行——包括 IE6 或 IE7。

## FormChanges()函数

为了让你慢慢适应，我们的功能开始了:

```
 function FormChanges(form) { 
```

我们重载了 form 参数——它可以是一个 DOM 元素，但是如果它是一个 ID 字符串，我们需要在 DOM 中定位该元素:

```
 if (typeof form == "string") form = document.getElementById(form); 
```

如果我们没有表单节点，函数将返回 null，而不做任何进一步的工作:

```
 if (!form || !form.nodeName || form.nodeName.toLowerCase() != "form") return null; 
```

我们现在声明将在整个函数中使用的变量:

*   “changed”是已被用户更新的表单元素的返回数组
*   “n”是一个表单元素节点
*   如果元素已经被改变，则‘c’被设置为真
*   “def”是选择框的默认选项
*   “o”、“ol”和“opt”是循环中使用的临时变量

```
 var changed = [], n, c, def, o, ol, opt; 
```

我们现在可以开始我们的主循环，依次检查每个表单元素。c 最初被设置为 false，表示我们正在检查的元素没有发生变化:

```
 for (var e = 0, el = form.elements.length; e < el; e++) {
	n = form.elements[e];
	c = false; 
```

接下来，我们将提取节点名(input、textarea、select)并在 switch 语句中检查它。我们只寻找选择和非选择节点，所以 switch 语句不是绝对必要的。然而，它更容易阅读，并允许我们在引入节点类型时添加更多的节点类型。

请注意，大多数浏览器都以大写形式返回节点名，但是为了安全起见，我们总是将字符串转换为小写。

```
 switch (n.nodeName.toLowerCase()) { 
```

第一个 case 语句评估`select`下拉列表。这是最复杂的检查，因为我们必须遍历所有子`option`元素来比较 selected 和 defaultSelected 属性。

该循环还将 def 设置为带有“selected”属性的最后一个选项。如果我们有一个单选框，那么将 def 与该节点的 selectedIndex 属性进行比较，以确保我们处理的是没有`option`或多个`option`元素具有‘selected’属性的情况([参见上一篇文章以获得完整的解释](https://www.sitepoint.com/detect-html-form-changes/))。

```
 // select boxes
case "select":
	def = 0;
	for (o = 0, ol = n.options.length; o < ol; o++) {
		opt = n.options[o];
		c = c || (opt.selected != opt.defaultSelected);
		if (opt.defaultSelected) def = o;
	}
	if (c && !n.multiple) c = (def != n.selectedIndex);
	break; 
```

我们现在需要处理`input`和`textarea`元素。注意，我们的`case "textarea":`语句没有使用 break，所以它落入了`case "input":`代码中。

复选框和单选按钮元素的 checked 和 defaultChecked 属性进行比较，而所有其他类型的值与 defaultValue 进行比较:

```
 // input / textarea
	case "textarea":
	case "input":

		switch (n.type.toLowerCase()) {
			case "checkbox":
			case "radio":
				// checkbox / radio
				c = (n.checked != n.defaultChecked);
				break;
			default:
				// standard values
				c = (n.value != n.defaultValue);
				break;
		}
		break;
} 
```

如果 c 的值为真，则该元素已经改变，所以我们将它附加到改变后的数组中。循环现在完成了:

```
 if (c) changed.push(n);
} 
```

我们只需要返回改变后的数组并结束函数:

```
 return changed;
} 
```

## 示例用途

假设我们已经创建了以下表单:

```
 <form id="myform" action="index.html" method="post">
<fieldset>

	<legend>Your profile</legend>

	<input type="hidden" id="changed" name="changed" value="yes" />

	<div>
		<label for="name">name:</label>
		<input type="text" id="name" name="name" value="Jonny Dough" />
	</div>

	<div>
		<label for="job">job title:</label>
		<select id="job" name="job">
			<option>web designer</option>
			<option selected="selected">web developer</option>
			<option>graphic artist</option>
			<option>IT professional</option>
			<option>other</option>
		</select>
	</div>

	<div>
		<button type="submit">Update Profile</button>
	</div>

</fieldset>
</form> 
```

我们可以使用如下代码检查用户是否更改了任何表单字段:

```
 var changed = FormChanges("myform");
alert(changed.length + " field(s) have been updated."); 
```

或者，如果没有发生变化，我们可以在提交表单时将隐藏的“changed”值更新为“no”。这将允许服务器端代码跳过字段验证和数据库更新:

```
 var form = document.getElementById("myform");
form.onsubmit = function() {
	if (FormChanges(form).length == 0) {
		document.getElementById("changed").value = "no";
	}
	return true;
} 
```

*(注意:将“是”改为“否”会逐渐降级，因为如果 JavaScript 不可用，服务器将始终处理传入的数据。)*

希望你觉得有用。

## 分享这篇文章