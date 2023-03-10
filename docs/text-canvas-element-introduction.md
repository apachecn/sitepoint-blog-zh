# 在画布元素上使用文本:简介

> 原文：<https://www.sitepoint.com/text-canvas-element-introduction/>

元素是 HTML5 的一部分，可以用来在支持的浏览器中“动态”绘制 2D 图形。这包括绘制各种形状的能力，如正方形、矩形和圆形，以及处理图像的能力。

有趣的是，你也可以给`canvas`元素添加文本，这就是我们今天要学习的内容。不过，在开始之前，让我们先来看看一些基础知识。

## 什么画布*不是*

画布不是简单的图像占位符**而不是**。它的内容使用 JavaScript 呈现。你可以在一个文档中不止一次地使用一个`canvas`元素，每个`canvas`元素就是一个嵌入的 **DOM** 节点，可以用来在`canvas`容器中创建图形。

自然，如果你在一个页面上创建多个`canvas`元素，你可以给每个`canvas`元素一个惟一的 ID 属性，这样你就可以用 JavaScript 轻松地访问每个`canvas`元素。

带有 ID 的基本`canvas`元素如下所示:

```
<canvas id="CanvasText" width="300" height="150"></canvas>
```

如果没有 CSS，`canvas`元素会显示为一个透明的矩形框，其大小可以通过 HTML 中的`width`和`height`属性进行调整，在`canvas`上绘制时的默认颜色是黑色。

## 我们的页面设置

现在我们已经对`canvas`元素有了一个基本的概述，让我们看看一些 HTML，我们可以用它们作为基本模板来进行画布操作。

```
<body>
  <canvas id="CanvasText" width="300" height="150">
  </canvas>
  <script>
    var canvas = document.getElementByID('CanvasText');
    if (canvas.getContext) {
      var context = canvas.getContext('2d');
    }
    // rest of the code goes here...
  </script>
</body>
```

注意:通常情况下，当我们确保 DOM 准备就绪时，就会执行上面看到的 JavaScript。为了简洁起见，我们在这里不包括它。

这为我们提供了一个基本的 300×150 像素的矩形画布(这是默认值，所以如果我们愿意，可以省略这些像素)，画布将是透明的，带有一个细的黑色边框。

## 用 JavaScript 绘制文本

您可以使用`strokeText()`或`fillText()`方法或两者的组合来创建文本。第一种方法生成不带填充的“描边”文本，而第二种方法生成填充文本。你也可以使用任何你想要的颜色，如果你真的想变得有创意，甚至可以使用渐变。您还需要定义字体属性，例如设置字体大小。

完成此任务的 JavaScript 如下所示:

```
context.font = "25px Arial";
context.fillText("HTML5 Canvas Text", 10, 50);
```

这将产生 25 像素的 Arial 字体文本。最后一行使用 x 和 y 坐标(10，50)来定义文本在`canvas`上的位置。

您可以像这样给文本添加颜色:

```
context.fillStyle = "#000099";
```

这将产生深蓝色的文本。您可以使用脚本中的样式，结合使用`fillText()`和`strokeText()`(为文本添加一个笔画或边框)和不同的颜色来创建各种效果。你也可以使用粗体和斜体，类似于 CSS 的[字体速记](http://www.impressivewebs.com/a-primer-on-the-css-font-shorthand-property/)属性的工作方式:

```
context.font="italic 25px Arial";
```

你可以改变笔画风格，使其更重；默认值为 1px，但可以更改:

```
context.lineWidth = 2;
```

如果您使用较大的字体和不同的颜色进行描边和填充，以产生一点对比，这将非常有用。

## 更多文本相关的东西

除了使用坐标来设置文本将出现在`canvas`上的位置，还可以使用`textAlign`属性，该属性采用以下值之一:

*   开始
*   结束
*   左边的
*   对吧
*   中心

因此，这将被定义为:`context.textAlign = "center";`(或左，右——无论你想改变)。

然而，对于文本，这改变了使用`strokeText()`和`fillText()`设置的 x 和 y 坐标的含义，所以在使用`textAlign`时要注意这一点。

您还可以使用`textBaseline`属性来指定文本出现在`canvas`的什么位置。其价值是:

*   中间
*   顶端
*   底部
*   绞刑
*   字母的
*   表意的

这将被这样使用:`context.textBaseline = "middle";`。

请记住，如果您使用此属性，它将调整笔画和填充的位置—假设您已经在定义了笔画和填充后声明了此属性。因此，如果在定义笔画之后但在定义填充之前调整`textBaseline`,您可能会得到笔画从填充偏移的有趣效果。

正如在 CSS 中所做的那样，如果用户系统上没有主要字体(如上面使用的 Arial ),您可以添加备用字体:

```
context.font = "bold italic 40px Arial, Helvetica, sans serif";
```

下面你会看到一个基本的演示，其中包含了我们在这里讨论过的一些内容:

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的 Pen [HTML5 画布文本演示](http://codepen.io/SitePoint/pen/bDAcE)。