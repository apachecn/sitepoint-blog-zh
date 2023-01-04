# 使用 HTML5 表单数据接口的简单 Ajax

> 原文：<https://www.sitepoint.com/easier-ajax-html5-formdata-interface/>

如果您正在开发一个单页面应用程序或练习渐进式增强技术，您经常需要拦截表单提交并将其转换为 Ajax 调用。让我们来看一个典型的表格:

```
<form id="myform" action="webservice.php" method="post">

<input type="email" name="email" />

<select name="job">
<option value="">role</option>
<option>web developer</option>
<option>IT professional</option>
<option>other</option>
</select>

<input type="checkbox" name="freelancer" /> are you a freelancer?

<input type="radio" name="experience" value="4" /> less than 5 year's experience
<input type="radio" name="experience" value="5" /> 5 or more year's experience

<textarea name="comments" rows="3" cols="60"></textarea>

<button type="submit">Submit</button>

</form>
```

jQuery 中的表单拦截非常简单，因为您可以将表单节点传递给`serialize`方法来提取所有字段数据，例如

```
$("myform").on("submit", function(e) {
	e.preventDefault();
	$.post(this.action, $(this).serialize());
});
```

如果您使用原始 JavaScript，您需要自己实现类似的功能。您可以手动逐个提取每个字段，也可以实现通用的表单元素数据提取循环:

```
document.getElementById("myform").onsubmit = function(e) {

	e.preventDefault();

	var f = e.target,
		formData = '',
		xhr = new XMLHttpRequest();

	// fetch form values
	for (var i = 0, d, v; i < f.elements.length; i++) {
		d = f.elements[i];
		if (d.name && d.value) {
			v = (d.type == "checkbox" || d.type == "radio" ? (d.checked ? d.value : '') : d.value);
			if (v) formData += d.name + "=" + escape(v) + "&";
		}
	}

	xhr.open("POST", f.action);
	xhr.setRequestHeader("Content-Type","application/x-www-form-urlencoded; charset=UTF-8");
	xhr.send(formData);

}
```

即使您在一个可重用的函数中定义它，这也是一个合理的代码量。如果您禁用了字段或将其设为只读，您可能还需要额外的检查。

幸运的是，XMLHttpRequest2 中增加了一个鲜为人知的`FormData`接口，它可以为您处理许多困难的工作。让我们重新编写 JavaScript 提交处理程序来使用它:

```
document.getElementById("myform").onsubmit = function(e) {

	e.preventDefault();

	var f = e.target,
		formData = new FormData(f),
		xhr = new XMLHttpRequest();

	xhr.open("POST", f.action);
	xhr.send(formData);
}
```

这要简单得多——它也比 jQuery 更快、更容易阅读。

可以向`FormData`构造函数传递一个表单元素节点；这指示它检索并编码所有的字段名/值对。您还会注意到，我们不需要显式设置`xhr.setRequestHeader("Content-Type")`，因为数据是以表单的`submit()`方法中定义的相同格式发送的。还使用了`multipart/form-data`编码，这样您就可以上传文件。

如果没有表单元素传递给构造函数，就会创建一个空的`FormData`对象。无论以哪种方式初始化，都可以使用`append`方法添加额外的名称/值对，例如

```
var formData = new FormData();
formData.append("name", "value");
formData.append("a", 1);
formData.append("b", 2);
```

如果值是文件或 Blob，第三个参数可以指定可选的文件名。

所有现代浏览器都支持。只有 IE9 和更低版本会带来麻烦，但是，如果你支持 IE 的旧版本，你可能会使用 jQuery 或其他实现自己的字段数据提取方法的库。

更多信息，请参考 MDN 上的[表单数据参考](https://developer.mozilla.org/en-US/docs/Web/API/FormData)和[使用表单数据对象](https://developer.mozilla.org/en-US/docs/Web/Guide/Using_FormData_Objects)。

## 分享这篇文章