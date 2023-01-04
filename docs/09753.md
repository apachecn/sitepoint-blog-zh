# 如何检测浏览器对 CSS3 属性的支持

> 原文：<https://www.sitepoint.com/detect-css3-property-browser-support/>

特征检测曾经简单得多。

在网络早期，浏览器嗅探是一个合理的解决方案。开发人员将检查用户代理并运行针对应用程序的 JavaScript 代码。这很痛苦，但通常是必要的，因为浏览器技术变化很大。

web 标准的兴起减少了对代码分叉的需求。Internet Explorer 的事件模型和 XMLHttpRequest 实现与 W3C 标准不一致，但是一点点对象检测克服了这些障碍。我们的大部分代码可以在任何地方工作。

我们现在有 HTML5 和 CSS3。没有一个浏览器支持所有的功能，所以通常需要包含一些填充或者使用检测技术来确保跨浏览器的兼容性。考虑这个浮雕文本示例:

```
 body
{
	font-family: sans-serif;
	background-color: #fff;
}

.emboss
{
	font-size: 2.5em;
	font-weight: bold;
	color: #fff;
	text-shadow: 1px 1px 1px rgba(0,0,0,0.4);
} 
```

大多数现代浏览器显示出令人愉悦的效果:

![Embossed Text](img/f9ead43a2f9ebb9a88cab59fb7534315.png)

为了实现这一点，我们将字体设置为与背景相同的颜色。不幸的是，这使得文本在不支持文本阴影的浏览器中不可见。这包括 Internet Explorer 9.0 和所有浏览器的旧版本。

## 现代救援！

这是一个神奇的库，可以检测 CSS 属性、转换、HTML5 元素、画布、SVG、地理定位、本地存储、触摸事件等等。最小化的 gzipped 版本只有 3.7kb，您可以通过下载一个定制的版本来进一步减小它。

Modernizr 提供了一个 JavaScript API，并附加了诸如 textshadow、opacity、cssgradients、svg 等类名。到`html`元素。因此，我们可以相应地重写我们的浮雕文本 CSS:

```
 .emboss
{
	font-size: 2.5em;
	font-weight: bold;
	color: #333;
}

.textshadow .emboss
{
	color: #fff;
	text-shadow: 1px 1px 1px rgba(0,0,0,0.4);
} 
```

如果您需要检测各种各样的功能，Modernizr 是最佳解决方案之一。

## 滚动您自己的检测代码

如果您只想检测几个 CSS 特性，第三方库可能是多余的。Modernizr 和类似的解决方案通过创建一个元素(不附加到 DOM)并测试 CSS 属性的值来工作。不支持的属性通常返回“未定义”。

文本阴影是最容易检测的属性之一——如果支持的话，下面的代码会将“textshadow”类附加到`html`元素中:

```
 // detect CSS text-shadow support in JavaScript
if (document.createElement("detect").style.textShadow === "") {
	document.getElementsByTagName("html")[0].className += " textshadow";
} 
```

带有供应商前缀的属性有点复杂。例如，可能不直接支持 boxShadow，但这些属性中的一个可能是:WebkitBoxShadow、MozBoxShadow、OBoxShadow、msBoxShadow、KhtmlBoxShadow。因此，有必要遍历选项，例如

```
 // detect CSS box-shadow support in JavaScript
var d = document.createElement("detect"),
	CSSprefix = "Webkit,Moz,O,ms,Khtml".split(","),
	All = ("boxShadow " + CSSprefix.join("BoxShadow,") + "BoxShadow").split(",");

for (var n = 0, np = All.length; n < np; n++) {
	if (d.style[All[n]] === "") {
		document.getElementsByTagName("html")[0].className += " boxshadow";
		break;
	}
} 
```

这有点啰嗦，你不会想为每个属性编写相似的代码。因此，我们将把该功能封装在一个模块中，该模块检测 CSS 文本阴影、文本笔划、框阴影、边框半径、边框图像和不透明度支持:

```
 // CSS support detection
var Detect = (function() {

	var 
		props = "textShadow,textStroke,boxShadow,borderRadius,borderImage,opacity".split(","),
		CSSprefix = "Webkit,Moz,O,ms,Khtml".split(","),
		d = document.createElement("detect"),
		test = [],
		p, pty;

	// test prefixed code
	function TestPrefixes(prop) {
		var
			Uprop = prop.charAt(0).toUpperCase() + prop.substr(1),
			All = (prop + ' ' + CSSprefix.join(Uprop + ' ') + Uprop).split(' ');

		for (var n = 0, np = All.length; n < np; n++) {
			if (d.style[All[n]] === "") return true;
		}

        return false;
	}

	for (p in props) {
		pty = props[p];
		test[pty] = TestPrefixes(pty);
	}

	return test;

}()); 
```

如果支持，Detect.textShadow、Detect.textStroke、Detect.boxShadow、Detect.borderRadius、Detect.borderImage 和 Detect.opacity 的值将返回 true。如果需要，我们可以将相关的类名添加到`html`元素中:

```
 // append to HTML node
var html = document.getElementsByTagName("html")[0];
for (t in Detect) {
	if (Detect[t]) html.className += " " + t.toLowerCase();
} 
```

或者显示支持的属性列表:

```
 for (t in Detect) {
	document.write(
		"CSS " + t + " support? " + 
		(Detect[t] ? "YES" : "NO") +
		"<br>"
	);
} 
```

[**演示页面**](https://blogs.sitepointstatic.com/examples/tech/feature-detection/index.html) 展示了这段代码的运行。您可以将它作为您自己的检测库的基础——或者包含 Modernizr 并使用它更容易！

## 分享这篇文章