# 带有 SVG 的 CSS:真实世界的用法

> 原文：<https://www.sitepoint.com/css-with-svg/>

SVG 是一种轻量级矢量图像格式，用于在网络和其他支持交互性和动画的环境中显示各种图形。在本文中，我们将探索使用 CSS 和 [SVG](https://www.sitepoint.com/svg-101-what-is-svg/) 的各种方法，以及在网页中包含 SVG 并操作它们的方法。

[可缩放矢量图形(SVG)](https://www.sitepoint.com/svg-101-what-is-svg/) 格式自 1999 年以来一直是一种开放标准，但随着 Internet Explorer 9 的发布，浏览器的使用在 2011 年变得实用。今天， [SVG 在所有浏览器上都得到了很好的支持](https://caniuse.com/#feat=svg)，尽管更多的高级特性可能会有所不同。

## SVG 优势

位图图像格式如 WebP、PNG、JPG 和 GIF 定义了单个像素的颜色。一张`100` × `100` PNG 图像需要 10000 个像素。每个像素需要四个字节用于红色、绿色、蓝色和透明度，因此得到的文件是 40，000 字节(加上一点点用于元数据)。应用压缩来减小文件大小:PNG 和 GIF 使用类似 ZIP 的无损压缩，而 JPG 有损压缩并删除不太明显的细节(WebP 可以使用这两种方法)。

位图是照片和更复杂图像的理想选择，但图像放大后清晰度会下降。

SVG 是用 XML 定义的矢量图像。点、线、曲线、路径、椭圆、矩形、文本等。是在 SVG 画布上绘制的。例如:

```
<svg xmlns="https://www.w3.org/2000/svg" viewBox="0 0 800 600">
  <circle cx="400" cy="300" r="250" stroke-width="20" stroke="#f00" fill="#ff0" />
</svg> 
```

`viewBox`定义一个坐标空间。在本例中，从位置`0,0`开始的`800` × `600`区域有一个带红色边框的黄色圆圈，在中心绘制了一个`250`单位半径:

![SVG circle](img/f99ff14f9c12c980fe4b85c480f35a56.png)

以下是矢量相对于位图的优势:

*   上面的 SVG 使用不到 150 个字节，这比同等的 PNG 或 JPG 要小得多
*   SVG 背景默认是透明的
*   图像可以缩放到任何大小而不损失质量
*   SVG 代码/元素可以在服务器(使用任何语言)或浏览器(使用 CSS 和 JavaScript)上生成和操作
*   就可访问性和 SEO 而言，文本和绘图元素是机器可读和人类可读的。

SVG 图像是徽标、图表、图标和简单图表的理想选择。只有照片通常是不切实际的，尽管 SVG 已经被用于[延迟加载占位符](https://github.com/technopagan/sqip)。

## SVG 工具

理解 [SVG 绘图](http://svgpocketguide.com/book/)的[基础](https://www.sitepoint.com/svg-drawing-basics/)很有用，但是你很快就会想要用一个可以生成代码的编辑器来创建更复杂的形状。选项包括:

*   [Adobe Illustrator](https://www.adobe.com/products/illustrator.html) (商业)
*   [亲和力设计师](https://affinity.serif.com/en-gb/designer/)(商业)
*   [草图](https://www.sketchapp.com/)(商用)
*   [喷墨打印机](https://inkscape.org/en/)(开源)
*   [Gravit 设计师](https://gravit.io/)(免费商业计划，在线)
*   [Vectr](https://vectr.com/) (免费，在线)
*   SVG-Edit ( [开源](https://github.com/SVG-Edit/svgedit)，在线)
*   [四四方方的 SVG](https://boxy-svg.com/) (仅商业、在线但闪烁的浏览器)
*   免费商业计划，在线，但只显示浏览器
*   [SVG 图表库](http://mediatemple.net/blog/tips/svg-charting-libraries/)，它通常使用传递给 JavaScript 函数的数据创建 SVG 图表。

每种工具都有不同的优势，对于看似相同的图像，你会得到不同的结果。一般来说，更复杂的图像需要更复杂的软件。

使用 [SVGO](https://github.com/svg/svgo) ( [插件可用于大多数构建工具](https://github.com/svg/svgo#other-ways-to-use-svgo)，或者杰克·阿奇博尔德的 [SVGOMG 交互工具](https://jakearchibald.github.io/svgomg/)，通常可以进一步简化和最小化结果代码。

## 作为静态图像的 SVG

当在 HTML `<img>`标签或 CSS `background-url`中使用时，SVG 的行为与位图相同:

```
<!-- HTML image -->
<img src="myimage.svg" alt="a vector graphic" /> 
```

```
/* CSS background */
.myelement {
  background-image: url("mybackground.svg");
} 
```

浏览器将禁用嵌入到 SVG 文件中的任何脚本、链接和其他交互式功能。您可以使用 CSS 以与使用`transform`、`filters`等操作其他图像相同的方式操作该 SVG。结果通常优于位图，因为 SVG 可以无限缩放。

### CSS 内嵌 SVG 背景

SVG 可以作为背景图像直接内嵌在 CSS 代码中。这对于较小的、可重复使用的图标来说是理想的，可以避免额外的 HTTP 请求。例如:

```
.mysvgbackground {
  background: url('data:image/svg+xml;utf8,<svg  viewBox="0 0 800 600"><circle cx="400" cy="300" r="50" stroke-width="5" stroke="#f00" fill="#ff0" /></svg>') center center no-repeat;
} 
```

可以使用标准的 UTF-8 文本编码(而不是 base64 ),所以如果需要的话，编辑 SVG 图像更容易。

使用像 [PostCSS 资产插件](https://github.com/borodean/postcss-assets)这样的工具，这个过程通常更实用。

### 带有 SVG 的 CSS:响应性 SVG 图像

当创建一个响应式网站时，图像的大小通常是其容器的宽度或图像本身的宽度(以较小者为准)。这在 CSS 中是通过使用:

```
img {
  display: block;
  max-width: 100%;
  height: auto;
} 
```

然而，`<img>`标签*中使用的 SVG 可能*没有隐式维度。您可能会发现`max-width`被计算为零，图像完全消失。要解决这个问题，请确保在`<svg>`标签中定义了默认的`width`和`height`:

```
<svg xmlns="https://www.w3.org/2000/svg" width="400" height="300"> 
```

## HTML 内嵌的 SVG 图像

SVG 可以直接放入 HTML 标记中。然后，图像成为 DOM 的一部分，可以使用 CSS 和 JavaScript 进行操作:

```
<p>SVG is inlined directly into the HTML:</p>

<svg id="invader" xmlns="https://www.w3.org/2000/svg" viewBox="35.4 35.4 195.8 141.8">
  <path d="M70.9 35.4v17.8h17.7V35.4H70.9zm17.7 17.8v17.7H70.9v17.7H53.2V53.2H35.4V124h17.8v17.7h17.7v17.7h17.7v-17.7h88.6v17.7h17.7v-17.7h17.7V124h17.7V53.2h-17.7v35.4h-17.7V70.9h-17.7V53.2h-17.8v17.7H106.3V53.2H88.6zm88.6 0h17.7V35.4h-17.7v17.8zm17.7 106.2v17.8h17.7v-17.8h-17.7zm-124 0H53.2v17.8h17.7v-17.8zm17.7-70.8h17.7v17.7H88.6V88.6zm70.8 0h17.8v17.7h-17.8V88.6z"/>
  <path d="M319 35.4v17.8h17.6V35.4H319zm17.6 17.8v17.7H319v17.7h-17.7v17.7h-17.7V159.4h17.7V124h17.7v35.4h17.7v-17.7H425.2v17.7h17.7V124h17.7v35.4h17.7V106.3h-17.7V88.6H443V70.9h-17.7V53.2h-17.7v17.7h-53.2V53.2h-17.7zm88.6 0h17.7V35.4h-17.7v17.8zm0 106.2h-35.5v17.8h35.5v-17.8zm-88.6 0v17.8h35.5v-17.8h-35.5zm0-70.8h17.7v17.7h-17.7V88.6zm70.9 0h17.7v17.7h-17.7V88.6z"/>
</svg>

<p>The SVG is now part of the DOM.</p> 
```

在这种情况下，`width`或`height`属性不是必需的，因为可以直接控制尺寸。例如:

```
#invader {
  display: block;
  width: 200px;
  height: auto;
}

#invader path {
  stroke-width: 0;
  fill: #080;
} 
```

但是，添加维度可以确保在不应用 CSS 时 SVG 的大小不会不合适。

见 [CodePen](https://codepen.io) 上的笔 [HTML-inline SVG](https://codepen.io/SitePoint/pen/WgNJEq)by SitePoint([@ SitePoint](https://codepen.io/SitePoint))
。