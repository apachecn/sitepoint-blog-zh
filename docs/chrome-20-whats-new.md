# Chrome 20 有什么新功能

> 原文：<https://www.sitepoint.com/chrome-20-whats-new/>

如果你今天早上注意到你的 Chrome 浏览器有轻微的变慢，那么它已经更新到版本 20 了。 [Chrome 19 于 5 月中旬](https://www.sitepoint.com/chrome-19-whats-new/)发布，那么谷歌的工程师们都在忙些什么呢？

在很大程度上，Chrome 20 是一个大的错误修复。除了更新的版本号和稍大的“新标签”按钮，普通用户不会注意到任何新的东西。幸运的是，对于开发者来说，有许多特性隐藏在幕后。

## HTML5 数据列表元素

HTML5 数据表提供了一个自动建议框。当用户开始键入时，合适的选项出现在下拉列表中，或者可以输入新值。例如:

```
 <label for="choose">What's your favorite browser?</label>
<input id="choose" type="text" value="" list="browser" />
<datalist id="browser">
	<option value="Safari">Apple Safari</option>
	<option value="Chrome">Google Chrome</option>
	<option value="IE">Microsoft Internet Explorer</option>
	<option value="Firefox">Mozilla Firefox</option>
	<option value="Opera">Opera</option>
</datalist> 
```

Webkit 并不总是领先于游戏，该元素在 Firefox 和 Opera 中已经被支持了一段时间。实现略有不同:

*   Firefox 提供了最好的体验，用户可以在选项文本中的任何地方搜索文本。
*   Opera 只允许在选项值属性中进行搜索。下拉列表显示值的开头与搜索字符串匹配的选项。
*   Chrome 的实现与 Opera 类似，只是显示了选项文本和值。

数据列表在 Safari 或 IE9 及更低版本中不起作用。幸运的是，您可以使用标准的选择框和常规输入来提供一个后备选项，例如

```
 <label for="choose">What's your favorite browser?</label>
<datalist id="browser">
	<select>
		<option value="">Other</option>
		<option value="Safari">Apple Safari</option>
		<option value="Chrome">Google Chrome</option>
		<option value="IE">Microsoft Internet Explorer</option>
		<option value="Firefox">Mozilla Firefox</option>
		<option value="Opera">Opera</option>
	</select>
	<label for="choose">or type one:</label>
</datalist>
<input id="choose" type="text" value="" list="browser" /> 
```

## 新的移动开发特性

Web 检查器(Ctrl+Shift/Cmd + I 或 F12)设置(窗口右下角的 cog 图标)现在提供了一个有用的“覆盖设备度量”选项来调整查看窗口的大小和设置字体缩放。

![Web Inspector Override device metrics setting](img/dfa17acf8513399cf821ebf38ce29efb.png)

您还会注意到“模拟触摸事件”选项。对移动开发者来说都非常有用。

## 新的 Chrome 页面

浏览器中添加了几个内部页面:

*   [chrome://inspect/](//inspect/) —列出正在运行的扩展和打开的标签，并链接到 Web Inspector。
*   [chrome://omnibox/](//omnibox/) —显示特定 omnibox 搜索词的结果。这主要是 Chrome 开发者感兴趣的，尽管我怀疑它也可以被 SEO 专家使用。
*   [chrome://policy/](//policy/) —用于在大公司中应用组策略。该功能似乎还没有完全实现。

虽然 Chrome 20 算不上是革命性的，但它仍然是世界上最常用的浏览器。Chrome 21 预计将于 2012 年 8 月中旬上市。

## 分享这篇文章