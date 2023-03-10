# 对媒体查询使用 SVG

> 原文：<https://www.sitepoint.com/using-svg-with-media-queries/>

在 HTML 文档中，我们可以根据视窗的条件显示、隐藏或重新排列页面的某些部分。例如，如果浏览器窗口的宽度为 480 像素，我们可以将导航从水平的切换到垂直的可折叠列表。当对媒体查询使用 [SVG](https://www.sitepoint.com/svg-101-what-is-svg/) 时，我们可以做类似的事情。

本文节选自蒂芙尼的著作 *[CSS Master，第 3 版](https://www.sitepoint.com/premium/books/css-master-3rd-edition)* 。如果您想掌握其他高级 CSS3 特性，如 Flexbox 和 Grid，请查看它！

* * *

除了在 HTML 中使用 CSS，我们还可以在 SVG 中使用 CSS，或*可缩放矢量图形*。SVG 是一种用于描述平面二维图像的标记格式。因为它是一种标记语言，所以它有一个文档对象模型，可以和 CSS 一起使用。

通过将 CSS 与 SVG 结合使用，我们可以根据用户交互来改变 SVG 的外观。或者我们可以在多个地方使用同一个 SVG 文档，并根据视窗的宽度显示或隐藏它的一部分。

所有主要的浏览器引擎都支持 [SVG 1.1](https://caniuse.com/?search=svg) 规范，而且它们已经支持了很多年。另一方面，对 [SVG 2](https://svgwg.org/svg2-draft/) 特性的支持仍在进行中。在撰写本文时，我们在这里讨论的一些内容对浏览器的支持有限。当你读到这篇文章时，情况可能已经改变了。关注 Chromium 元问题——[实现 SVG2 特性](https://bugs.chromium.org/p/chromium/issues/detail?id=225863)——跟踪基于 Chromium 的浏览器的开发进度。观看[支持 SVG 2 特性](https://bugzilla.mozilla.org/show_bug.cgi?id=1328534)元问题以跟踪 Firefox 的实现工作，以及 WebKit 的[为 Safari 实现 SVG 2](https://bugs.webkit.org/show_bug.cgi?id=191292) 元问题。问题跟踪器可能不容易操作，但目前它们是跟踪 SVG 2 支持的最佳方式。

然而，在我们进一步讨论之前，让我们先讨论一下什么是 SVG 以及为什么应该使用它。

## 矢量图像与光栅图像

目前网络上使用的大多数图像是光栅图像，也称为位图图像。**光栅图像**由固定网格上的像素组成，每英寸有固定数量的像素。JPEG、WebP、GIF 和 PNG 都是光栅图像格式的例子。

光栅图像依赖于*分辨率*。144 PPI(每英寸像素)的 PNG 图像在具有 144 PPI 显示分辨率的设备上看起来很棒。然而，当在更高分辨率的 400 PPI 显示器上观看时，同样的图像可能看起来模糊。光栅图像也有固定的尺寸，以其原始尺寸看起来最好。将 150 x 150 像素的图像放大到 300 x 300 像素会使其变形。

矢量图像格式不是使用网格上的像素，而是描述构成图像的基本形状(圆形、矩形、线条或路径)以及它们在文档坐标系中的位置。因此，矢量图像与*分辨率无关*，无论显示分辨率或显示尺寸如何，都能保持其质量。

分辨率独立是 SVG 的最大优势。我们可以在不损失质量的情况下放大或缩小图像。同样的图像在高和低 PPI 设备上看起来都很棒。也就是说，SVG 不太适合照片所需的大量色彩数据。最适合画画和造型。用它来代替 PNG 和 GIF 图像，并作为图标字体的更灵活的替代。

SVG 的另一个优点是它被设计成可以和其他 web 语言一起使用。我们可以用 JavaScript 创建、修改和操作 SVG 图像。或者，正如我们将在下面看到的，我们可以使用 CSS 样式化和动画化 SVG。

## 将 CSS 与 SVG 文档相关联

对 SVG 使用 CSS 很像对 HTML 使用它。我们可以使用 SVG 元素的`style`属性应用 CSS，使用`<style>`元素在文档中对 CSS 进行分组，或者链接到外部样式表。每种方法的优点和缺点与在 HTML 中使用 CSS 是一样的。

### 使用`style`属性

下面是一个简单的 SVG 文档，其中的代码创建了一个黑色的圆圈:

```
<svg version="1.1" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 200 200" 
➥enable-background="new 0 0 200 200">
    <circle cx="101.3" cy="96.8" r="79.6" />
</svg> 
```

下图显示了该代码如何在浏览器中呈现。

![A circle in SVG](img/c4c18773e1cac7939f0c63f93c905b5c.png)

让我们用 CSS 和`style`属性给我们的圆一个粉红色的填充:

```
<svg version="1.1" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 200 200" 
➥enable-background="new 0 0 200 200">
    <circle cx="101.3" cy="96.8" r="79.6" style="fill: #f9f" />
</svg> 
```

这样做的效果如下所示。

![Using the style attribute to add a fill color](img/9e8107af0ea592945940872e9e76cdd5.png)

在 HTML 中使用 CSS 和在 SVG 中使用 CSS 之间有一个区别:属性名。我们在 HTML 文档中使用的许多 CSS 属性与 SVG 不兼容，反之亦然。我们将在本章后面回到这一点。

当然，使用`style`属性并不是使用 CSS 的最佳方式。这样做限制了跨多个元素或文档重用这些样式的能力。相反，我们应该使用内联或链接的 CSS。

### 在 SVG 文档中嵌入 CSS

我们可以使用`<style>`元素，而不是使用`style`属性:

```
<svg version="1.1" xmlns="http://www.w3.org/2000/svg" viewBox="0 0
➥ 200 200" enable-background="new 0 0 200 200">
    <style type="text/css"> circle {
            fill: #0c0;
        } </style>
    <circle cx="101.3" cy="96.8" r="79.6" />
</svg> 
```

在 SVG 文档中嵌入 CSS 可以让我们在同一个文档中的多个元素中重用这些样式，但是它会阻止 CSS 在多个文档之间共享。这对于徽标和图标来说很好。但是如果您正在创建类似图表样式库的东西，外部 CSS 文件是更好的选择。

使用标准文本编辑器，您还可以将 CSS 添加到用 Sketch、Inkscape 或 Illustrator 等软件创建的 SVG 图像中。这样做不会影响您使用绘图应用程序编辑图像的能力，但是如果您使用图像软件编辑文件，应用程序可能会重写或删除您的 CSS。

### 从 SVG 链接到外部 CSS 文件

与 HTML 一样，链接到外部 CSS 文件使得在几个 SVG 文档之间共享样式成为可能。要链接外部 CSS 文件，请在 SVG 文件的开头添加`<? xml-stylesheet ?>`:

```
<?xml version="1.0" encoding="utf-8"?>
<?xml-stylesheet href="style.css" type="text/css"?>
<svg version="1.1" xmlns="http://www.w3.org/2000/svg" viewBox="0 0
➥ 200 200" enable-background="new 0 0 200 200">
    <circle cx="101.3" cy="96.8" r="79.6" />
</svg> 
```

#### 使用`<link>`元素

或者，使用 HTML `<link>`元素。如果您使用这个方法，您需要包含`xmlns`名称空间属性，如下所示:

```
<link href="style.css" type="text/css" rel="stylesheet"
    ➥ xmlns="http://www.w3.org/1999/xhtml" /> 
```

*注意:一些老的浏览器需要用`<defs>`或`<g>`标签将`<link>`元素括起来。*

`<link>`元素不是 SVG 元素。属于 HTML 和 XHTML。XHTML 是根据 XML 标记规则解析的 HTML 的变体。根据 XML 的规则，我们可以借用其他 XML 方言的元素及其行为，比如 XHTML。然而，要做到这一点，我们需要使用`xmlns`属性告诉浏览器该元素属于哪个名称空间。

#### 使用`@import`

我们还可以通过在`<style>`和`</style>`标签中使用`@import`链接到外部样式表:

```
<style type="text/css"> @import('style.css'); </style> 
```

该方法的功能类似于`<link>`方法。

#### SVG 和`<img>`元素:局限性

从 SVG 文件链接到外部资源，包括 CSS 文件，对`<img>`元素不起作用。这是浏览器内置的`<img>`元素的安全限制。

如果您想在 SVG 图像中使用链接 CSS，您需要做以下两件事之一:

*   在 SVG 文档中使用`<style>`元素来内联放置 CSS
*   使用一个`<iframe>`或`<object>`元件(见下面的注释)

*注:Craig Buckler 的教程《[如何给你的网页](https://www.sitepoint.com/add-svg-to-web-page/)添加可缩放矢量图形》详细讨论了`<iframe>`和`<object>`的使用。*

一般来说，你应该用`<iframe>`而不是`<object>`。然而，`<object>`元素可以是`<a>`元素的子元素，而`<iframe>`不能。使用`<iframe>`或`<object>`也可以让父文档的文档树使用 SVG 文档树。这意味着我们可以用 JavaScript 与之交互(比如用`document.querySelector('iframe').contentDocument`)。

#### 内联 SVG 和外部资产

将 SVG 添加到 HTML 时，浏览器不会加载 SVG 文档引用的外部资源。但是，我们可以从 HTML 文档的`<head>`链接到 SVG 文档的 CSS:

```
<head>
    ⋮
    <link href="svg.css" type="text/css" rel="stylesheet" />
</head> 
```

HTML 文档中的 SVG 元素也成为 HTML 文档树的一部分。如果使用内联 SVG，将 HTML 相关和 SVG 相关的 CSS 组合在同一个样式表中是非常好的。

## SVG 和 HTML 的区别

虽然 SVG 和 HTML 都是标记语言，但它们之间有两个显著的差异，这两个差异会影响它们使用 CSS 的方式:

*   SVG 并不遵循 CSS 盒子模型
*   SVG 缺乏定位方案

### SVG 并不遵循 CSS 盒子模型

当与 HTML 一起使用时，CSS 布局遵循 CSS 盒模型的规则。另一方面，SVG 使用坐标进行布局。它遵循的可能是最好理解的“形状模型”。

SVG 形状不限于矩形盒子。因此，大多数与盒子模型相关的属性都不适用于 SVG 元素。例如，您不能更改 SVG 元素的`padding`或`margin`。也不能使用`box-sizing`、`box-shadow`、`outline`或`border-*`属性。网格布局、浮动和 Flexbox 也不起作用。

但是，您可以使用 CSS 来设置或更改一系列 SVG 属性和属性值。完整的列表在 [SVG 2](https://www.w3.org/TR/SVG2/propidx.html) 规范中列出，尽管在大多数浏览器中的支持是不完整的。有些 CSS 属性，比如`filter`，可以和 SVG 或者 HTML 一起使用。在本章中，我们将在特定技术的背景下讨论其中的一些。

### SVG 缺乏定位方案

当 CSS 用于 HTML 时，元素框可以:

*   存在于正常流程中
*   用`float`属性从正常流程中删除
*   用`position`属性从正常流程中删除

CSS 规范将这些称为*定位方案*。SVG 中不存在定位方案。`position`属性对 SVG 元素没有影响。像`top`、`left`和`bottom`这样的属性也是如此，它们依赖于被定位的元素。您也不能在 SVG 文档中浮动元素。

相反，SVG 使用坐标系来放置元素。例如，要创建一个`<circle>`，需要使用`cx`和`cy`属性设置其中心点坐标，并使用`r`属性设置半径长度。多边形由一系列点坐标和绘制在它们之间的线段组成。换句话说，您可以定义一个元素将被绘制到 SVG 画布的什么地方，但是您不能在 CSS 意义上“定位”它们。

与定位方案相关，SVG 也缺乏`z-index`和堆叠上下文的思想。

*注意: [SVG 2](https://svgwg.org/svg2-draft/) 规范的确定义了 SVG 文档中`z-index`和堆栈上下文的行为，但是大多数浏览器还不支持它。*

相反，SVG 元素根据它们的源顺序进行堆叠。那些出现在文档后面的元素位于栈顶。如果您想改变 SVG 元素的堆叠顺序，您需要在源代码中移动它们，或者使用 JavaScript 在 DOM 树中对它们重新排序。

事实上，大多数 CSS 2.1 属性并不适用于 SVG 文档。例外情况包括动画和变换、`display`、`overflow`、`visibility`、`filter`，以及一些与字体和文本相关的属性。相反，您必须对 SVG 文档使用[特定于 SVG 的样式属性](https://www.w3.org/TR/SVG/propidx.html)。这些属性中的大多数也可以表示为 SVG 元素属性。

## 样式化 SVG 元素

下面是一个简单的例子，展示了如何使用 CSS 样式化 SVG 元素。首先是我们的 SVG 文档，它是一个独立的文件:

```
<?xml version="1.0" encoding="utf-8"?>
<?xml-stylesheet href="styles.css" type="text/css" ?>
<svg version="1.1"  xmlns:xlink=
➥"http://www.w3.org/1999/xlink" x="0px" y="0px" viewBox="0 0 497
➥ 184" enable-background="new 0 0 497 184" xml:space="preserve">
    <polygon id="star" points="77,23.7 98.2,66.6 145.5,66.5 111.2,
➥106.9,119.3,154 77,131.8 34.7,154 42.8,106.9 8.5,67.5 55.8,
➥66.6 "/>
    <circle id="circle" cx="245" cy="88.9" r="67.5"/>
</svg> 
```

该标记创建了如下所示的图像。

![A simple circle and star SVG image](img/bee6f18d3b951ebffec1724b0ef3c939.png)

虽然我们不能在 SVG 文档中使用大多数 CSS 属性，但是我们可以使用 CSS 来改变元素的颜色。让我们把星星变成黄色:

```
#star {
    fill: hsl( 44, 100%, 50% );
} 
```

您会经常看到与 SVG 标签一起使用的`fill`属性—例如，`<circle fill="rgb( 255, 185, 0 )" cx="3" cy="10" r="100">` —但是它也是一个可以与 CSS 一起使用的属性。

我们还可以使用 CSS 来调整元素的`stroke`，它是 SVG 形状的轮廓。即使没有设置`stroke`属性，形状的描边仍然存在。让我们给我们的圆一个深蓝色，十像素宽的虚线边框。我们还将把它的`fill`属性设置为`cornflowerblue`:

```
circle {
    fill: cornflowerblue;
    stroke: darkblue;
    stroke-width: 10;
    stroke-dasharray: 10, 15;
    stroke-linecap: round;
} 
```

这给了我们下面的结果。

![A simple circle and star SVG image](img/75390d31a5f2c4e83d0c74b04abfbfa1.png)

### 使用 SVG 属性作为 CSS 属性

我们还可以用 CSS 设置一些形状元素的坐标值:`<rect>`、`<circle>`、`<ellipse>`。通常，我们会对这些元素使用 SVG 属性:

```
<svg viewBox="0 0 400 400" xmlns="http://www.w3.org/2000/svg">
    <rect x="20" y="200" width="300" height="300" fill="#f60" />
</svg> 
```

但是，SVG 2 将一些 SVG 属性重新定义为几何属性。这意味着我们可以使用 CSS 来设置它们的值:

```
<svg viewBox="0 0 400 400" token class">.w3.org/2000/svg">
    <style type="text/css">
      rect {
          x: 20px;
          y: 50px;
          width:  300px;
          height: 300px;
          fill: #f60;
      }
    </style>
    <rect />
</svg> 
```

可以使用 CSS 设置坐标属性(`x`和`y`)、中心坐标属性(`cx`和`cy`)和半径属性(`rx`、`ry`和`r`)。`width`和`height`也可以。SVG 属性的单位是可选的。另一方面，CSS 值*需要*单位。长度和百分比对于这里提到的属性都是有效的，但是要注意长度在 SVG 文档中的作用有点不同。记住 SVG 中的 S 代表*可伸缩*。SVG 元素的计算大小还取决于:

*   根 SVG 元素的计算出的`width`和`height`
*   根元素的`viewBox`属性的值
*   应用于元素或其祖先的任何缩放变换

换句话说，我们的`<rect>`元素的角在 SVG 坐标系中是`(20, 50)`、`(20, 320)`、`(350, 320)`和`(20, 350)`。然而，*实际尺寸*可能会更大或更小，这取决于上述因素。

不是每个 SVG 属性都可以通过 CSS 获得——至少不是在每个浏览器中都可以。例如，Chrome 和 Edge 支持使用 CSS `path()`函数来设置路径数据，或者使用`d`属性:

```
path {
    d: path("M 454.45223,559.21474 -304.96705,163.45948 417.4767,-296.33928 Z");
} 
```

在撰写本文时，它们是唯一支持的浏览器。在 Firefox 和 WebKit 中增加支持的工作还没有开始。

对于其他形状元素，SVG 2 规范完全不一致。迄今为止，您*必须*使用元素属性来设置`<line>`、`<polyline>`和`<polygon>`元素的属性。

也就是说，我们并不局限于使用类型(或元素)选择器来设置属性。例如，我们可以使用类选择器定义小、中和大圆:

```
<svg viewBox="0 0 400 400" xmlns="http://www.w3.org/2000/svg">
    <style type="text/css"> .small {
        cx: 20px;
        cy: 20px;
        r:  20px;
        fill:  #0c0;
      }

      .medium {
        cx: 80px;
        cy: 80px;
        r:  60px;
        fill:  #fc0;
      }

      .large {
        cx: 220px;
        cy: 220px;
        r:  120px;
        fill: #00f;
      } </style>

    <circle class="small" />
    <circle class="medium" />
    <circle class="large" />
</svg> 
```

不管选择器是什么，使用 CSS 语法来指定属性也使得动画化它们变得容易。我们将在下一节看看如何做到这一点。

## 动画和过渡 SVG CSS 属性

当我们添加过渡和动画时，使用 CSS 和 SVG 变得更加有趣。这个过程就像用 CSS 制作 HTML 元素的动画一样，但是使用了特定于 SVG 的属性。让我们使用下面的 SVG 文档创建一个闪烁的星星效果:

```
<svg version="1.1" xmlns="http://www.w3.org/2000/svg" x="0px"
➥ y="0px" viewBox="0 0 497 184" xml:space="preserve">
    <defs>
        <link href="twinkle.css" type="text/css" rel="stylesheet"
➥ xmlns="http://www.w3.org/1999/xhtml"/>
    </defs>
    <polygon class="star" points="77,23.7 98.2,66.6 145.5,66.5 111.2
➥,106.9 119.3,154 77,131.8 34.7,154 42.8,106.9 8.5,67.5
➥ 55.8,66.6 "/>
    <polygon class="star twinkle" points="77,23.7 98.2,66.6 145.5,
➥66.5 111.2,106.9 119.3,154 77,131.8 34.7,154 42.8,106.9
➥ 8.5,67.5 55.8,66.6 "/>
</svg> 
```

我们的文档包含两个星形多边形元素，每个元素都有一个类名`star`。为了创造闪烁的效果，我们将制作第一个动画。这是我们的 CSS:

```
@keyframes twinkle {
    from {
        fill-opacity: .4;
    }
    to {
        fill-opacity: 0;
        transform: scale( 2 );
    }
}
.star {
    fill: rgb( 255,195,0 );
    transform-origin: 50% 50%;
}
.twinkle {
    animation: twinkle 1.5s infinite forwards ease-in;
} 
```

这里我们使用了特定于 SVG 的属性`fill-opacity`。与 CSS 一样，如果我们可以插入 SVG 样式属性的值，我们就可以对它进行动画处理或转换。您可以在下图中看到动画的两个不同点。

![Our pulsing star animation](img/c4802792708edec1561d29f734dfe7a4.png)

让我们看另一个例子。这一次我们将通过转换`stroke-dasharray`属性来创建一个绘图效果。这是我们的 SVG 文档:

```
<svg version="1.1" xmlns="http://www.w3.org/2000/svg"
➥xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
        viewBox="0 0 200 200" enable-background="new 0 0 200 200">
    <circle fill="transparent" stroke-width="16" cx="101.3"
➥ cy="96.8" r="79.6"/>
</svg> 
```

`stroke-dasharray`属性接受创建虚线模式的长度或百分比值的逗号分隔列表。奇数编号的值决定了虚线的长度。偶数编号的值决定间隙长度。`5, 10`的`stroke-dasharray`值意味着笔画将为`5px`长，每个虚线之间有一个`10px`的间隙。值为`5, 5, 10`时，`5px`和`10px`虚线长度交替变化，中间有`5px`间隙。

我们可以使用`stroke-dasharray`来创建一个画图效果，从零破折号长度和大破折号间隔开始，以大破折号长度和零破折号间隔结束。然后我们将在两者之间转换。下面是我们的 CSS 的样子:

```
circle {
    transition: stroke-dasharray 1s ease-in;
    fill: transparent;
    stroke-dasharray: 0, 500;
}
.animate {
    stroke-dasharray: 500, 0;
} 
```

在过渡开始时，我们的笔画是不可见的，因为虚线长度是`0`而我们的间隙是`500`。但是，当我们将`animate`类添加到我们的圆中时，我们将破折号长度转换为`500`并消除了这个间隙。效果有点像用圆规画圆。为什么是 500？这是产生这种特殊效果的最小的值。

### 充满活力的未来之路

还记得上一节中我们通过 CSS 定义路径的例子吗？有一天，我们可能能够使用 CSS 在每个浏览器中制作路径动画:

```
path {
    d: path("M357.5 451L506.889 192.25H208.111L357.5 451Z");
    transition: d 1s ease-in-out;
}
.straighten {
    d: path("M357.5 8871L406 -10113.75H208.111L357.5 351Z");
} 
```

然而，迄今为止，只有基于 Chromium 的浏览器如 Google Chrome 和 Microsoft Edge 支持以这种方式动画化路径定义。要在其他浏览器中实现这一点，可以使用 JavaScript 库，如 [GreenSock](https://greensock.com/) 及其 MorphSVGPlugin。除了跨浏览器兼容性之外，GreenSock 和 MorphSVGPlugin 还使两个形状之间的变形变得更加容易，而不管每个形状中的点数是多少。

## 对媒体查询使用 SVG

对于 HTML 文档，我们可以根据视窗的条件显示、隐藏或重新排列页面的某些部分。例如，如果浏览器窗口的宽度为 480 像素，我们可以将导航从水平的切换到垂直的可折叠列表。我们可以对媒体查询和 SVG 文档做类似的事情。考虑一个徽标，例如下图中虚构的六边形网页设计与开发。

![A very real logo for a fictitious company](img/8d97c3c06a02b1030d0c1be56ab1ebd1.png)

如果没有媒体查询，这个 SVG 徽标将简单地拉伸或收缩以适应视窗或其容器。但是有了媒体查询，我们可以做更聪明的事情。

让我们来区分 HTML 文档视口和 SVG 文档视口。当 SVG 内联时，HTML 视口和 SVG 视口是同一个视口。SVG 文档的行为类似于任何其他 HTML 元素。另一方面，当一个 SVG 文档被链接时——就像`<iframe>`、`<object>`或`<img>`元素一样——我们处理的是 SVG 文档视窗。

媒体查询在这两种情况下都有效，但是当 SVG 文档被链接时，它的视区独立于它的 HTML 文档。在这种情况下，浏览器窗口的大小并不决定 SVG 视口的大小。相反，视口大小由`<object>`、`<iframe>`或`<img>`元素的尺寸决定。以下面的(删节的)SVG 文档为例:

```
<svg version="1.1" id="HexagonLogo" xmlns="http://www.w3.org/2000/
➥svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
➥ viewBox="0 0 555 174" xml:space="preserve">
    <defs>
        <style type="text/css"> /* CSS goes here */ </style>
    </defs>
    <g id="hex">
        <polygon id="hexagonbg" points="55.2,162 10,86.5 55.2,11
➥ 145.5,11 190.7,86.5 145.5,162  "/>
        <path id="letterH" fill="#FFFFFF" d="M58,35.5h33v35.2h18.
➥4V35.5 h33.2v103.4h-33.2v-38.3H91v38.3H58V35.5z M77.5,126.5V87.
➥3h45.6v39.2h4V47.9h-4v35.6H77.5V47.9h-4v78.6H77.5z"/>
    </g>

    <g id="word-mark">
        <g id="hexagon-word">
            ...
        </g>
        <g id="web-design-and-dev">
            ...
        </g>
    </g>
</svg> 
```

*注意:这种技术的完整演示，包括这个 SVG 文档的完整源代码，可以在[代码档案](https://github.com/spbooks/csspro3)中找到。*

在较小的视口中，让我们只显示六边形符号中的 H:

```
@media (max-width: 320px) {
    [id=word-mark] {
        display: none;
    }
} 
```

现在，每当我们的 SVG 的容器小于或等于`20em`，只有我们的标志的符号部分将是可见的。

![Showing/hiding elements based on the SVG viewport size](img/6a59152a637f81423451cca87ac46d6a.png)

要从 HTML 文档中触发这个视图，请设置 SVG 容器的宽度:

```
<iframe src="hexlogo.svg" style="width: 320px; border:0"></iframe> 
```

从上面的图片中您可能已经注意到，我们的 SVG 图像保留了它的固有维度，即使它的一部分被隐藏了。不幸的是，这是 SVG 的一个局限。要解决这个问题，我们需要更改 SVG 文档的`viewBox`属性，但是只有当视口小于某个尺寸时。这是`matchMedia`的一个很好的用例(这将在第 10 章“T2 有条件地应用 CSS”中讨论)。

顾名思义，`viewBox`属性决定了 SVG 元素的可视区域。通过调整它，我们可以确定 SVG 图像的哪一部分填充了视口。下面是一个使用`matchMedia`和媒体查询来更新`viewBox`属性的例子:

```
<script type="text/javascript">
const svg = document.querySelector( 'svg' );

/* Store the original value in a variable */
const originalViewBox = svg.getAttribute( 'viewBox' );

/* Define our media query and media query object */
const mq = matchMedia( '( max-width: 320px )' );

/* Define the handler */
const updateViewBox = () => {
    if (mq.matches) {
        /* Change the viewBox dimensions to show the hexagon */
        svg.setAttribute( 'viewBox', '0 0 200 174' );
    } else {
        svg.setAttribute( 'viewBox', originalViewBox );
    }
}

svg.addEventListener( 'SVGLoad', updateViewBox );

/* Fire if the media condition changes */
mq.addEventListener( 'change', updateViewBox );
</script> 
```

现在，每当 SVG 容器为 320 像素或更小时，`viewBox`的值将为`"0 0 200 174"`。当超过 320 像素时，`viewBox`会恢复到初始值。

![Adjusting the viewBox attribute based on the viewport’s width](img/fac305289ff06c4c58d7d4ea18fc1aa3.png)

因为这种技术使用了`onload`事件属性或者`SVGLoad`事件，所以将 CSS 和 JavaScript 嵌入到 SVG 文件中是一个好主意。当 CSS 是外部的时，`SVGLoad`事件可能会在其关联的 CSS 完成加载之前触发。

### 通过`background-size`使用媒体查询

SVG 文档和媒体查询不限于前景图像。我们还可以使用 CSS `background-size`属性来调整 SVG 视口的大小。

我们将从这个 SVG 文档开始:

```
<?xml version="1.0" encoding="utf-8"?>
<svg version="1.1" xmlns="http://www.w3.org/2000/svg"
➥ xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
➥ viewBox="-20 -20 250 250" xml:space="preserve">
    <style type="text/css"> circle {
            stroke: #000;
            stroke-width: 30;
            fill: #009688;
        }
        @media ( width: 100px ) {
            circle {
                fill: #673ab7;
            }
        }
        @media ( width: 300px ) {
            circle {
                fill: #ffc107;
            }
        } </style>
    </defs>
    <circle cx="100" cy="100" r="100" />
    <circle cx="100" cy="100" r="50" />
</svg> 
```

这是一个简单的例子。我们的`<circle>`元素在特定的视口宽度下获得新的`fill`颜色。当视口宽度为 20 像素时，`fill`值为蓝绿色。当它是 300 像素宽时，它是黄色的。

要做到这一点，我们必须使用我们的 SVG 图像作为背景图像，并设置选择器的`background-size`属性。在这种情况下，我们将使用我们的图像作为`<body>`元素和`<li>`元素的背景:

```
body, li  {
    background: url(circles.svg);
}
body  {
    background-color: #9c27b0;
    background-size: 300px auto;
}
li {
    background-position: left center;
    background-repeat: no-repeat;
    background-size: 1em auto;
    padding-left: 40px;
    font-size: 24px;
    margin: 1rem 0;
} 
```

结果如下图。

![Manipulating the SVG viewport with the CSS background-size property](img/41e5edca97bc4f344ea39e1f6173a5cc.png)

## 结论

将 SVG 与 CSS 结合使用为我们提供了更多灵活和适应性强的文档。您现在应该知道如何:

*   使用 CSS 样式化 SVG 元素
*   动画 SVG 属性
*   使用 CSS 媒体查询和`matchMedia` API 来显示和隐藏 SVG 文档的一部分

* * *

*本文摘自蒂芙尼的著作 [CSS Master，第三版](https://www.sitepoint.com/premium/books/css-master-3rd-edition)。如果您想掌握其他高级 CSS3 特性，如 Flexbox 和 Grid，请查看它！*

## 分享这篇文章