# 如何构建自动扩展的 Textarea jQuery 插件，第 3 部分

> 原文：<https://www.sitepoint.com/build-auto-expanding-textarea-3/>

在第一部分的[中，我们发现了如何构建](https://www.sitepoint.com/build-auto-expanding-textarea-1/)[自动扩展的文本区域](https://blogs.sitepointstatic.com/examples/tech/textarea-expander/demo.html)，并整理了需求。在第 2 部分中，我们决定了编码员如何初始化我们的组件。现在是时候接触一些 JavaScript 了。

我们正在构建一个名为 TextAreaExpander 的 jQuery 插件。你可以在本教程中了解 jQuery 插件开发的复杂性，但是我们的代码框架是:

```
 (function($) {

	// jQuery plugin definition
	$.fn.TextAreaExpander = function(minHeight, maxHeight) {

		// ... our code ...

		return this;
	};

})(jQuery); 
```

的美元。然后，fn 语句用参数 minHeight 和 maxHeight 声明我们的新 jQuery 插件 TextAreaExpander。我们可以在一个 JSON 对象中表达这些，但是我们不太可能需要更多的参数，所以我们保持简单。

请注意，“this”指的是 jQuery 对象；我们返回它是为了确保其他 jQuery 效果可以绑定到相同的 DOM 元素。

## 初始化文本区域

以下初始化代码被添加到我们的 TextAreaExpander 函数中:

```
 // initialize
this.each(function() {

	// is a textarea?
	if (this.nodeName.toLowerCase() != "textarea") return;

	// set height restrictions
	var p = this.className.match(/expand(d+)-*(d+)*/i);
	this.expandMin = minHeight || (p ? parseInt('0'+p[1], 10) : 0);
	this.expandMax = maxHeight || (p ? parseInt('0'+p[2], 10) : 99999);

	// initial resize
	ResizeTextarea(this);

	// add events
	if (!this.Initialized) {
		this.Initialized = true;
		$(this).css("padding-top", 0).css("padding-bottom", 0);
		$(this).bind("keyup", ResizeTextarea).bind("focus", ResizeTextarea);
	}
});

return this;
}; 
```

这将遍历所有 jQuery 选择的 DOM 节点，并运行一个匿名函数。该函数中“this”的值是一个单独的 textarea 节点。发生以下初始化:

1.  第一行确保只有 textareas 应用了自动扩展效果。
2.  接下来的三行提取最小和最大高度值。默认情况下使用传递给 TextAreaExpander 函数的参数。如果没有指定，则使用正则表达式分析 textarea 的“expand”类 HTML。如果我们仍然没有值，则假定为 0 和 99999(请注意，textarea 的最小高度始终为 1 个字符，因此不会应用零高度)。这些值存储为 textarea 节点对象的属性，因此我们可以从任何代码中检查它们。
3.  下面一行调用 ResizeTextarea 函数并传递 Textarea 节点。这将在自动扩展初始化时将高度设置为适当的大小。
4.  最后，我们重置任何垂直填充并定义“keyup”和“focus”事件。当 Textarea 获得焦点时和用户更新文本后，调用相同的 ResizeTextarea 函数。这些事件周围的“if”条件确保它们只能应用于任何 textarea 一次。这个条件可以应用于整个初始化函数，但是，这段代码允许我们随意改变最小和最大高度。

## 调整文本区域的大小

我们现在需要定义 ResizeTextarea 函数。

在[第一部分](https://www.sitepoint.com/build-auto-expanding-textarea-1/)中，我们讨论了浏览器的差异，并指出 IE 和 Opera 永远不应该设置 0px 的`textarea`高度。因此，如果正在使用 IE 或 Opera，我们将分配一个返回 false 的变量:

```
 var hCheck = !($.browser.msie || $.browser.opera); 
```

这是肮脏的，但恐怕我们不能依靠更好的方法，如对象检测。不过，我愿意接受建议！

我们现在可以编写 ResizeTextarea 函数了:

```
 // resize a textarea
function ResizeTextarea(e) {

	// event or element?
	e = e.target || e;

	// find content length and box width
	var vlen = e.value.length, ewidth = e.offsetWidth;
	if (vlen != e.valLength || ewidth != e.boxWidth) {

		if (hCheck && (vlen < e.valLength || ewidth != e.boxWidth)) e.style.height = "0px";
		var h = Math.max(e.expandMin, Math.min(e.scrollHeight, e.expandMax));

		e.style.overflow = (e.scrollHeight > h ? "auto" : "hidden");
		e.style.height = h + "px";

		e.valLength = vlen;
		e.boxWidth = ewidth;
	}

	return true;
}; 
```

此函数通过参数“e”传递。这要么是一个 textarea 节点(在初始化期间)，要么是一个事件对象(当 keyup 或 focus 发生时)。

1.  如果触发了事件，第一行将“e”更改为 textarea 节点对象。
2.  在文本区输入的字符数被分配给 vlen。盒子的像素宽度被指定为宽度。如果这些值没有改变，我们不需要担心调整框的大小(用户可能只是移动光标)。vlen 和 ewidth 作为名为 valLength 和 boxWidth 的 textarea 节点对象的属性保留。这些是在调整 textarea 的大小时设置的，因此调整大小总是在第一次调用 ResizeTextarea 时发生。
3.  下一行将 textarea 高度重置为 0px。只有在非 IE/Opera 浏览器中，如果内容被删除或框宽被更改，才会出现这种情况。
4.  textarea 的 scrollHeight 值现在被赋给了变量“h”。Math.min 和 Math.max 用于确保该值在为此 textarea 定义的最小和最大像素限制范围内。
5.  在我们改变 textarea 高度之前，我们改变 CSS 溢出属性。只有当内容高度超过 textarea 高度时，滚动条才会显示。
6.  我们现在可以修改 textarea 的高度，并更新 valLength 和 boxWidth 的值。
7.  最后，该函数返回 true 以确保其他 textarea 事件处理程序不会被取消。

我们的 TextAreaExpander jQuery 插件已经完成。然而，我们需要确保该效果应用于所有带有 HTML“expand”类的`textarea`标签。在文件的最后，我们可以添加一个事件，在页面加载后初始化所有适当的文本区域:

```
 // initialize all expanding textareas
jQuery(document).ready(function() {
	jQuery("textarea[class*=expand]").TextAreaExpander();
}); 
```

我希望这一系列教程对你有所帮助。在你自己的项目中随意使用自动扩展的 textarea 插件。

有用的资源:

*   [扩展 textarea 示例页面](https://blogs.sitepointstatic.com/examples/tech/textarea-expander/index.html)
*   [示例页面 HTML](https://blogs.sitepointstatic.com/examples/tech/textarea-expander/index.html.txt)
*   [完整的 jQuery 插件代码(jQuery . textarea-expander . js)](https://blogs.sitepointstatic.com/examples/tech/textarea-expander/jquery.textarea-expander.js)
*   [下载 ZIP 文件中的完整代码](https://blogs.sitepointstatic.com/examples/tech/textarea-expander/textarea-expander.zip)

另请参见:

*   [第 1 部分:构建一个自动扩展的文本区域](https://www.sitepoint.com/build-auto-expanding-textarea-1/)
*   [第二部分:文本区初始化](https://www.sitepoint.com/build-auto-expanding-textarea-2/)
*   [如何开发一个 jQuery 插件](https://www.sitepoint.com/how-to-develop-a-jquery-plugin/)

## 分享这篇文章