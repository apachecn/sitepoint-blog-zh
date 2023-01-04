# 如何在 JavaScript 中计算更亮或更暗的十六进制颜色

> 原文：<https://www.sitepoint.com/javascript-generate-lighter-darker-color/>

在 HTML、CSS 和 JavaScript 中使用颜色很容易。然而，经常需要以编程方式生成颜色，例如，您需要一种比#123 亮 20%或比#abcdef 暗 10%的颜色。

CSS3 提供了一个很好的解决方案:HSL。除了使用十六进制或 RGB 颜色，您还可以设置色调、饱和度、亮度(或*亮度*)和不透明度，例如

```
 color: hsla(50, 80%, 20%, 0.5);
background-color: hsl(120, 100%, 50%); 
```

除了 IE8 及以下版本，大多数浏览器都支持 HSL 和 HSLA。您可以设置第三个亮度参数来更改颜色的亮度或暗度。

不幸的是，我们并不总是有在 HSL 工作的奢侈。虽然您可以设置单独的 HSL 颜色，但浏览器最终会将其转换为 RGB。此外，RGB 通常更容易使用，您可能已经用这种格式定义了颜色。

有各种算法来改变颜色亮度。许多人将 RGB 转换为 HSL，然后再转换回来，这对于客户端脚本来说是一个相当复杂的计算。因此，我用 JavaScript 编写了一个快速简单的跨浏览器解决方案。ColorLuminance 接受两个参数:

*   **十六进制** —十六进制颜色值，如“#abc”或“#123456”(哈希是可选的)
*   **lum** —亮度系数，即-0.1 暗 10%，0.2 亮 20%，以此类推。

完整代码:

```
 function ColorLuminance(hex, lum) {

	// validate hex string
	hex = String(hex).replace(/[^0-9a-f]/gi, '');
	if (hex.length < 6) {
		hex = hex[0]+hex[0]+hex[1]+hex[1]+hex[2]+hex[2];
	}
	lum = lum || 0;

	// convert to decimal and change luminosity
	var rgb = "#", c, i;
	for (i = 0; i < 3; i++) {
		c = parseInt(hex.substr(i*2,2), 16);
		c = Math.round(Math.min(Math.max(0, c + (c * lum)), 255)).toString(16);
		rgb += ("00"+c).substr(c.length);
	}

	return rgb;
} 
```

本质上，前三行清理字符串，并将 3 位十六进制代码扩展为完整的 6 位表示。

该循环依次提取红色、绿色和蓝色值，将其转换为十进制，应用亮度因子，并将其转换回十六进制。示例:

```
 ColorLuminance("#69c", 0);		// returns "#6699cc"
ColorLuminance("6699CC", 0.2);	// "#7ab8f5" - 20% lighter
ColorLuminance("69C", -0.5);	// "#334d66" - 50% darker
ColorLuminance("000", 1);		// "#000000" - true black cannot be made lighter! 
```

请 [**查看演示页面**](https://blogs.sitepointstatic.com/examples/tech/color-luminance/index.html)；颜色渐变是使用一系列 100 个`div`元素和稍亮的背景生成的。

希望你觉得有用。我将在 SitePoint 即将推出的另一个演示中使用该功能…

## 分享这篇文章