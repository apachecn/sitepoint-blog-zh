# 如何在 HTML、CSS 和 JavaScript 中使用字符实体

> 原文：<https://www.sitepoint.com/character-entities-html-css-javascript/>

您几乎肯定在 HTML 页面中遇到过实体。它们通常用于国际字符、数学运算符、形状、箭头和其他符号。例如:

```
 &spades;
&clubs;
&hearts;
&diams;
&copy;
&reg; 
```

这些直接映射到 UTF-8 字符。圆周率(π)的符号可以写成π或者它的 UTF-8 数字π。你可以在 [HTML 字符实体引用](http://www.digitalmediaminute.com/reference/entity/)上查找流行符号，发现它们的 HTML 实体代码和 UTF-8 索引号。

严格地说，如果您将页面作为 UTF-8 提供，就没有必要使用这些代码；HTML 和 XML 文档的默认字符集。然而，当您不使用 UTF-8 时，可能会出现相邻字符导致问题，或者很难在键盘上输入特定的符号。同样，您可能对 CSS 和 JavaScript 文件使用 ANSI 或其他编码方法。

## CSS 中的实体

通过伪元素添加内容越来越普遍——尤其是当你使用[网络字体图标](https://www.sitepoint.com/webfont-icons/)的时候。要添加任何 UTF-8 字符，您需要找到它的数字并将其转换为十六进制。对于圆周率，是 03C0。这可以使用前面的反斜杠添加到 CSS 文件中，例如

```
 #pi:before
{
	content: "\03C0";
} 
```

## JavaScript 中的实体

像 CSS 一样，JavaScript 需要十六进制的 UTF-8 数。然而，在这种情况下，必须使用前面的“u”对其进行转义，例如

```
 var pi = "u03C0"; 
```

如果字符代码是 255 或更少，您也可以使用标准的 JavaScript 2 位十六进制表示法，例如

```
 var copyright = "xA9"; 
```

这就是全部了。简单的解决方案，但我总是忘记！

## 分享这篇文章