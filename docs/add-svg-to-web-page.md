# 如何将可缩放矢量图形添加到您的网页中

> 原文：<https://www.sitepoint.com/add-svg-to-web-page/>

在本教程中，我们将讨论如何给你的网页添加 [SVG](https://www.sitepoint.com/svg-101-what-is-svg/) 图片。与位图图像不同，SVG 是描述形状、线条和填充的 XML 标记，因此文档可以以多种方式使用。

在这个系列中，我们已经讨论了什么是 SVGs】、[为什么你应该考虑它们](https://www.sitepoint.com/7-reasons-to-consider-svgs-instead-of-canvas/)和[基本矢量绘图](https://www.sitepoint.com/svg-drawing-basics/)。

在某些时候，您会希望将精心制作的 SVG 直接嵌入到网页中。至少有六种方法可以达到这个目标，但是所有的方法都有优点和缺点。

## 1.将 SVG XML 直接嵌入到 HTML 页面中

可以使用外部`<svg>`标签将 SVG 图像作为代码岛直接添加到 HTML 页面中:

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Embedded SVG</title>
</head>
<body>

  <h1>Embedded SVG</h1>

  <!-- SVG code -->
  <svg width="300px" height="300px"
    xmlns="https://www.w3.org/2000/svg">
    <text x="10" y="50" font-size="30">My SVG</text>
  </svg>

</body>
</html> 
```

这种方法适用于所有现代浏览器。SVG 成为页面 DOM 的一部分，因此可以用 CSS 或 JavaScript 操作它，也许是为了添加动画或对点击事件做出反应。*(注意，嵌入 SVG 本身的任何 JavaScript 都将被阻止。)*

主要的缺点是 SVG 必须嵌入到每一个需要它的页面中，并且可能需要为可重用的图标重复使用。这增加了页面权重，尽管 HTML 可以被缓存，但是 SVG 代码不能在其他地方被重用。

解决图像重复问题的一个办法是在每个页面上创建一个隐藏的 SVG 块(用 CSS `display: none`)。这可以选择性地包含使用`id`引用的多个图像:

```
<svg xmlns="https://www.w3.org/2000/svg" style="display: none;">
  <defs>
    <symbol id="box" viewBox="0 0 32 32">
      <title>box</title>
      <rect width="32" height="32" fill="#00c" />
    </symbol>
    <symbol id="circle" viewBox="0 0 32 32">
      <title>circle</title>
      <circle cx="16" cy="16" r="16" fill="#c00" />
    </symbol>
  <defs>
</svg> 
```

单个项目可以通过 [SVG `use`元素](https://developer.mozilla.org/docs/Web/SVG/Element/use)使用任意次:

```
<svg width="20" height="20">
  <use xlink:href="#box" />
</svg>

<svg width="30" height="30">
  <use xlink:href="#box" />
</svg>

<svg width="40" height="40">
  <use xlink:href="#circle" />
</svg> 
```

原始图像仍然可以使用 CSS 进行样式化，尽管不可能对`<use>`本身应用额外的样式。

## 2.使用一个`<img>`标签

SVG 可以像任何其他图像一样添加到您的网页中:

```
<img src="image.svg" alt="my image" /> 
```

通常的`width`、`height`、`alt`和其他[、`<img>`属性](https://developer.mozilla.org/docs/Web/HTML/Element/img#attributes)可以根据需要添加。

浏览器将 SVG 视为任何其他图像。出于安全原因，任何脚本、外部样式表、链接和其他 SVG 交互都将被禁用。

如果在一个 SVG 中定义了多个图像——比如`myimage.svg#circle`——可以使用目标锚点，但这在旧浏览器中不起作用。

## 3.应用 CSS 背景图像

SVGs 可以用作任何元素的 CSS 背景:

```
#myelement {
  background-image: url('./image.svg');
} 
```

UTF8 编码的内联数据 URIs 对于较小的、经常使用的 SVG 也很实用，这些 SVG 不太可能经常改变(并使整个样式表无效):

```
.myelement {
  background: url('data:image/svg+xml;utf8,<svg  viewBox="0 0 300 300"><circle cx="150" cy="150" r="100" stroke-width="5" stroke="#f00" fill="#ff0" /></svg>');
} 
```

像`<img>`标签、脚本、链接和其他类型的交互在你使用这个方法时被禁用。

## 4.载入一个`<iframe>`

浏览器可以自己呈现一个 SVG 文档，所以您可以在一个`iframe`中加载一个图像:

```
<iframe src="./image.svg">
  Your browser does not support iframes.
</iframe> 
```

如果您有一个带有自己的脚本和样式的可移植 SVG，并且需要从主页中分离出来，那么这可能是一个实用的方法。然而，从主页的 JavaScript 操作 SVG 图像将变得更加困难，尤其是当它托管在另一个域上时。

## 5.使用一个`<object>`标签

HTML [`<object>`标签](https://developer.mozilla.org/docs/Web/HTML/Element/object)可以用来给你的页面添加一个 SVG:

```
<object type="image/svg+xml" data="./image.svg">
  <img src="./fallback-bitmap.png" />
</object> 
```

回退文本或图像可以在`<object>`块中以类似于 iframes 的方式使用。

SVG 中的样式和脚本将会运行。不可能在 HTML 页面中使用 CSS 来设置 SVG 的样式，但是脚本可以操作单个元素:

```
// get first <object>
const objTag = document.querySelector('object');

// wait for SVG to load
objTag.addEventListener('load', () => {

  // reference to SVG document
  const svg = objTag.getSVGDocument();

  // make changes
  Array.from(svg.getElementsByTagName('path'))
    .forEach(p => p.setAttribute('fill', '#00f'))

}); 
```

大约十年前，这是最常用的选项，因为这是在旧版本 IE 中使用 SVG 的唯一可靠方式。

## 6.使用一个`<embed>`标签

为了完整起见，我将 [`<embed>`](https://developer.mozilla.org/docs/Web/HTML/Element/embed) 包括在内，但请不要使用它！它与`<object>`的相似之处在于它将外部内容嵌入到页面中，但是它被设计成与外部应用程序(比如浏览器插件)进行通信。

```
<embed type="image/svg+xml" src="./image.svg">
  not supported
</embed> 
```

自从 Flash 和 Silverlight 消亡以来，现代浏览器已经弃用并移除了对浏览器插件的支持。千万不要依赖`<embed>`。

## 应该使用哪种 SVG 技术？

如果您使用 SVG 作为图像，并且不需要改变样式或添加脚本，那么`<img>`标签或 CSS 背景可能是最好的选择。该文件可以缓存在浏览器中，无需进一步下载即可在其他页面上重用。

在需要交互性的地方，最流行的选择是将 SVG 直接嵌入 HTML。样式和脚本可以操作 DOM，链接可以添加到任何形状，复杂的 SVG 过滤器可以应用到其他页面元素。

这个标签已经失宠了。然而，如果您希望保持 SVG 的独立性和可缓存性，这仍然是一种可行的技术，但是它们需要一点 JavaScript 操作。

Iframes 可能适用于某些项目，但是永远不要使用`<embed>`,除非你不喜欢为古老的浏览器编码。

## 分享这篇文章