# 如何使用跨浏览器 Web 字体，第 1 部分

> 原文：<https://www.sitepoint.com/how-to-use-cross-browser-web-fonts-part-1/>

读者们，你们好！本周我将探索网络字体，寻找在你的网站上跨浏览器使用自定义字体的最佳方式。

我将从一些基本的解释开始，这样我们都知道我们在谈论什么，然后我将在下一篇文章中继续讨论一些更高级的方面。

## 介绍

多年来，在 web 上使用自定义字体(在通常的 web 安全字体集之外)是一件非常痛苦的事情，Web 设计师不得不求助于使用怪异的文本替换技术，如[图像替换](http://www.zeldman.com/2012/03/01/replacing-the-9999px-hack-new-image-replacement/)或 [siFR](http://en.wikipedia.org/wiki/Scalable_Inman_Flash_Replacement) ，这些技术被证明是粗糙且不灵活的。

但是，没有了！自从 CSS 字体模块 3 级获得了跨浏览器的广泛支持，事情已经有了很大的改善。见鬼，网络字体甚至可以追溯到 IE5.5。

让我们进一步探索网络字体。

## 基本 web 字体语法

在其核心，网络字体系统依赖于两件事。

### 1.@font-face 规则

首先，在 CSS 中插入一个块，描述要嵌入到页面中的 web 字体。这些信息都包含在一个`@font-face`块中，就像这样:

```
@font-face {
	...
}
```

这应该放在你的 CSS 的顶部:在你试图将字体应用到任何文本之前，你需要确保字体是可用的。

首先，我们包含一个字体系列名称，以在 CSS 中标识字体。这可以是您喜欢的任何名称，只要您始终如一地使用它(如果名称中有空格，您需要在名称两边加上引号):

```
@font-face {
  font-family: Abril;
}
```

**注:** Abril Fatface 字体可以在[字体松鼠](http://www.fontsquirrel.com/)上找到，这是一个免费网络字体的伟大来源。

接下来，您需要包含您想要在页面上包含的字体文件的位置:

```
@font-face {
  font-family: 'Abril Fatface Regular';
  src: url("AbrilFatface-Regular.otf");
}
```

请注意，您可以在该行中包含一系列位置，用逗号分隔:

```
src: local('Abril Fatface Regular'), url("AbrilFatface-Regular.otf"), url("AbrilFatface-Regular.eot");
```

浏览器按顺序浏览选项，并找到最适合它的选项。在这种情况下，我们要求浏览器首先检查字体是否安装在本地，如果有本地版本，就使用本地版本(这有助于节省带宽)。)

接下来，我们包括一个开放类型格式字体的引用，然后最后我们包括一个嵌入式开放类型版本，供旧版本的 IE 使用(这是他们所能理解的)。)

请注意，您不能指向不同域上的字体，除非您使用 CORS(参见 David Walsh 网站上的 Apache 专用解决方案)

要领就是这样；我们的字体现在应该可以在我们的网页上使用了。

### 2.标准字体系列声明

现在我们已经将字体嵌入到您的页面中，将它应用到一些文本就像使用`font-family`声明一样简单，就像我们通常会做的那样:

```
h1 {
  font-size: 8em;
  font-family: 'Abril Fatface Regular', sans-serif;
}
```

见我的[第一个例子](https://blogs.sitepointstatic.com/fontface/basic-example/basic-web-fonts-example.html)上面的代码在行动。

## 其他@font-face 选项

您可以在您的`@font-face`规则中包含许多其他选项:

```
@font-face {
  font-family: 'Abril Fatface';
  src: url("AbrilFatface.otf");
  font-weight: bold;
  font-style: italic;
  font-stretch: condensed;
  unicode-range: U+0026;
}
```

`font-*`描述符用于一个文件中有多种不同字体的情况。例如，我们在这里指定，如果可能的话，我们希望使用粗体、斜体、压缩的字体变体，而不是由浏览器下载全部内容。

描述符指定了你想要使用的字体文件中的字形。`U+0026`是与符号(&)的 unicode 编码，这意味着在这种情况下，只有与符号会从这个字体文件中下载，以便在页面上使用。当您只想在该字体中使用非常特殊的字符集时，这是一种节省带宽的好方法。

更多详情，请阅读 https://www.w3.org/TR/css3-fonts/#font-prop-desc 和 https://www.w3.org/TR/css3-fonts/#unicode-range-desc 的

## 跨浏览器现实

让 web 字体跨浏览器工作的现实有点不同。不同的浏览器支持的字体格式稍有不同，所以您需要提供一套备选方案。

*   Web 开放字体格式(。woff):适用于所有现代浏览器
*   嵌入式开放类型:用于旧版本的 Internet Explorer(即
*   SVG 字体:适用于旧版本的 iOS Safari (3.2-4.1)
*   Truetype 字体:用于默认 android 浏览器的旧版本

在我的第二个[跨浏览器示例](https://blogs.sitepointstatic.com/fontface/cross-browser-example/cross-browser-web-fonts-example.html)中的`@font-face`规则是这样的:

```
@font-face {
  font-family: 'abril_fatfaceregular';
  src: url('abrilfatface-regular-webfont.eot');
  src: url('abrilfatface-regular-webfont.eot?#iefix') format('embedded-opentype'),
       url('abrilfatface-regular-webfont.woff') format('woff'),
       url('abrilfatface-regular-webfont.ttf') format('truetype'),
       url('abrilfatface-regular-webfont.svg#abril_fatfaceregular') format('svg');
  font-weight: normal;
  font-style: normal;
}
```

这很难看，但幸运的是，您不需要自己生成它:像 Font Squirrel 这样的在线资源有易于使用的生成器，它接受一个字体文件，并生成您需要的所有替代格式文件和 CSS。

在下一部分中，我们将更进一步。

注:如果你真的想知道*为什么*所有这些工作，读保罗·爱尔兰的[防弹@font-face 语法](http://www.paulirish.com/2009/bulletproof-font-face-implementation-syntax/)。

## 分享这篇文章