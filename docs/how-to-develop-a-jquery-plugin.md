# 如何开发一个 jQuery 插件

> 原文：<https://www.sitepoint.com/how-to-develop-a-jquery-plugin/>

jQuery 是最流行的 JavaScript 库，许多网站采用它来实现动态效果和 Ajax 功能。然而，相对而言，很少有开发人员深入研究插件开发的深度。

在本教程中，我们将创建一个简单的插件来解释基础知识。我们的代码将反转一个或多个选定节点中的文本— [查看演示页面](https://blogs.sitepointstatic.com/examples/tech/jquery-plugin/index.html)。

## 为什么要创建 jQuery 插件？

一句话:复用。通过扩展 jQuery，您可以创建可在任何网页上使用的可重用组件。您的代码是封装的，因此在其他地方使用相同函数名的风险较小。

## jQuery 如何工作

jQuery 的核心是传递 DOM 元素或包含 CSS 选择器的字符串。它返回一个 jQuery 对象，这是一个类似数组的 DOM 节点集合。然后，一个或多个方法可以被*链接*到这组节点，例如

```
 // color all <p> tags red
$("p").css("color", "red"); 
```

注意:尽管 jQuery 库被命名为“jQuery”，但是“$”是引用它的快捷变量。要知道其他库可以自己抢' $ '。

## jQuery 插件如何工作

jQuery 允许将方法添加到它的库中。当被调用时，这些方法被传递给 JavaScript“this”对象中的 jQuery 对象。可以根据需要操作 DOM 节点，方法应该返回“this ”,这样就可以链接其他函数。

我们的示例插件将使用如下代码调用:

```
 // reverse the text in all <p> tags
$("p").reverseText(); 
```

我们还将添加两个可选参数，minlength 和 maxlength。定义时，字符串长度必须在这些限制之间，以使反转发生。

## 插件声明

插件是使用 jQuery fn 函数定义的，例如

```
 jQuery.fn.reverseText = function(params) { ... }; 
```

使用' jQuery '而不是' $ '可以确保不会与其他 JavaScript 库冲突。我们所有的内部代码也应该引用' jQuery '而不是' $ '。但是，我们可以使用匿名函数节省一些输入并减小文件大小:

```
 (function($) {
	$.fn.reverseText = function(params) { ... };
})(jQuery); 
```

该函数立即运行，并作为名为“$”的参数传递给 jQuery。由于' $ '是一个局部变量，我们可以假设它总是引用 jQuery 库，而不是另一个首先获取全局' $ '变量的库。

## 插件参数

我们的插件需要两个参数:minlength 和 maxlength。最简单的方法是将它们定义为函数参数，例如

```
 (function($) {
	$.fn.reverseText = function(minlength, maxlength) { ... };
})(jQuery);

// example
$("p").reverseText(0, 100); 
```

但是如果我们以后决定添加更多的参数呢？我们的插件可能有几十个选项——参数处理会很快变得复杂。或者，我们可以传递一个 JSON 对象，例如

```
 (function($) {
	$.fn.reverseText = function(params) { ... }
})(jQuery);

// example
$("p").reverseText( { minlength: 0, maxlength: 100 } ); 
```

reverseText 函数中的第一行应该定义一组默认参数，然后用任何用户定义的值覆盖这些参数。jQuery 扩展函数可以为我们处理这个问题:

```
 // merge default and user parameters
params = $.extend( {minlength: 0, maxlength: 99999}, params); 
```

因此， **params.minlength** 为 0， **params.maxlength** 为 99999，除非调用代码覆盖这些值。

## 插件代码

我们现在可以编写我们的主要插件代码了:

```
 // traverse all nodes
this.each(function() {

	// express a single node as a jQuery object
	var $t = $(this);

	// find text
	var origText = $t.text(), newText = '';

	// text length within defined limits?
	if (origText.length >= params.minlength &&  origText.length <= params.maxlength) {

		// reverse text
		for (var i = origText.length-1; i >= 0; i--) newText += origText.substr(i, 1);
		$t.text(newText);

	}

}); 
```

解释:

1.  this.each 函数遍历所有 jQuery DOM 节点，并调用一个匿名函数。
2.  在函数中，“this”包含一个节点。jQuery 节点集合被分配给$t，因此我们可以运行 jQuery 方法。
3.  变量 origText 被赋予 DOM 节点中的文本字符串。newText 被设置为空字符串。
4.  如果 origText 的长度在 params.minlength 和 params.maxlength 之间，循环将在 newText 中创建一个反向文本字符串。DOM 节点会相应地更新。

## 不要弄断链条！

最后，我们应该记住返回 jQuery 对象，以便可以链接其他方法:

```
 return this; 
```

## 完整的代码

我们的插件代码现在已经完成:

```
 (function($) {

	// jQuery plugin definition
	$.fn.reverseText = function(params) {

		// merge default and user parameters
		params = $.extend( {minlength: 0, maxlength: 99999}, params);

		// traverse all nodes
		this.each(function() {

			// express a single node as a jQuery object
			var $t = $(this);

			// find text
			var origText = $t.text(), newText = '';

			// text length within defined limits?
			if (origText.length >= params.minlength &&  origText.length <= params.maxlength) {

				// reverse text
				for (var i = origText.length-1; i >= 0; i--) newText += origText.substr(i, 1);
				$t.text(newText);

			}

		});

		// allow jQuery chaining
		return this;
	};

})(jQuery); 
```

这个文件保存为 jquery.reversetext.js。在 jquery 库加载后，我们可以将它包含在任何 HTML 页面中，例如

```
 <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01//EN" "https://www.w3.org/TR/html4/strict.dtd">
<html lang="en">
<head>
<title>jQuery plugin: reverseText demonstration</title>
</head>
<body>

<h1>jQuery plugin: reverseText</h1>
<p>This jQuery plugin reverses all the text in the selected nodes.</p>

<ul>
<li>This text will be reversed</li>
<li>This text will not be reversed</li>
<li>reversed</li>
<li>not reversed</li>
</ul>

<script type="text/javascript" src="jquery-1.3.2.min.js"></script>
<script type="text/javascript" src="jquery.reversetext.js"></script>
<script type="text/javascript">
// reverse even-numbered LI tag text
$("ul li:even").reverseText();
</script>

</body>
</html> 
```

此页中的列表现在在第一个和第三个项目符号上反转了文本(请记住，第一个项目从 0 开始编号):

![reversed text](img/03c2ec696f3141375e042bc8a778dce9.png)

资源:

*   [**查看插件演示页面**](https://blogs.sitepointstatic.com/examples/tech/jquery-plugin/index.html)
*   [查看插件 JavaScript 代码](https://blogs.sitepointstatic.com/examples/tech/jquery-plugin/jquery.reversetext.js)
*   [下载插件和示例代码](https://blogs.sitepointstatic.com/examples/tech/jquery-plugin/jquery.reversetext.zip)

您现在应该对 jQuery 插件开发有了很好的理解。SitePoint JavaScript 论坛也是一个很好的帮助和建议资源。

即将推出:一个新的三部分教程，描述如何构建一个有用的页面组件作为 jQuery 插件。

## 分享这篇文章