# Fabric.js 简介

> 原文：<https://www.sitepoint.com/introduction-to-fabric-js/>

在本文中，我将向您介绍[fabric . js](http://fabricjs.com/)—一个强大的 JavaScript 库，它使使用 HTML5 canvas 元素变得轻而易举。Fabric 为 canvas 提供了一个缺失的对象模型，以及一个 SVG 解析器、一个交互层和一整套其他不可或缺的工具。这是一个完全开源的项目，由 MIT 授权，多年来有许多贡献。

三年前，在发现使用原生 canvas API 的痛苦之后，我开始使用 Fabric 进行开发。我正在为 printio.ru 创建一个交互式设计编辑器——我的初创公司允许用户设计他们自己的服装。那时候，我想要的那种交互性只存在于 Flash 应用程序中。现在，很少有库接近 Fabric 的可能性，所以让我们仔细看看。

## 为什么是面料？

[Canvas](https://www.w3.org/html/wg/drafts/html/master/embedded-content-0.html#the-canvas-element) 允许你在网络上创建一些[绝对](http://net.tutsplus.com/articles/web-roundups/21-ridiculously-impressive-html5-canvas-experiments/) [惊人的](http://speckyboy.com/2011/12/07/20-amazing-implementations-of-html5-canvas/) [图形](http://artatm.com/2012/01/23-truly-amazing-and-unbelievable-html5-canvas-and-javascript-experiments/)，但是它提供的 API[低得令人失望](https://www.w3.org/html/wg/drafts/2dcontext/html5_canvas/)。如果你只是想在画布上画一些基本的形状，然后忘掉它们，这是一回事。如果你需要任何类型的交互，在任何一点上改变一张图片，或者画更复杂的形状，情况就会发生巨大的变化。Fabric 旨在解决这个问题。

原生 canvas 方法只允许您发出简单的图形命令，盲目地修改整个 canvas 位图。你想画一个长方形吗？使用 fillRect(左、上、宽、高)。想划清界限吗？使用 moveTo(左，上)和 lineTo(x，y)的组合。这就好像你在用画笔在画布上绘画，在上面涂越来越多的油或丙烯，几乎没有控制。

Fabric 在本地方法之上提供了一个简单但强大的对象模型，而不是在如此低的级别上运行。它负责画布状态和渲染，并让您直接处理对象。

这里有一个简单的例子来说明这种差异。假设你想在画布上画一个红色的矩形。下面是如何使用原生 canvas API 来实现这一点:

```
// reference canvas element (with id="c")

var canvasEl = document.getElementById('c');

// get 2d context to draw on (the "bitmap" mentioned earlier)

var ctx = canvasEl.getContext('2d');

// set fill color of context

ctx.fillStyle = 'red';

// create rectangle at a 100,100 point, with 20x20 dimensions

ctx.fillRect(100, 100, 20, 20);
```

下面的代码展示了如何用 Fabric 做同样的事情。两种方法的结果如图 1 所示。

```
// create a wrapper around native canvas element (with id="c")

var canvas = new fabric.Canvas('c');

// create a rectangle object

var rect = new fabric.Rect({

    left: 100,

    top: 100,

    fill: 'red',

    width: 20,

    height: 20

});

// "add" rectangle onto canvas

canvas.add(rect);
```

![Figure 1 Red Rectangle Drawn with Fabric or Native Canvas Methods ](img/d8b8c9361cd6776ff51c7b48debfb0d5.png)
 *图 1 用布料或原生画布绘制红色矩形的方法*

在这一点上，矩形的大小几乎没有区别——这两个例子非常相似。然而，您已经可以看到使用 canvas 的方法是多么的不同。使用原生方法，您可以对 context——表示整个画布位图的对象——进行操作。在 Fabric 中，您对对象进行操作——实例化它们，更改它们的属性，并将它们添加到画布中。你可以看到这些物品是织物土地上的一等公民。

渲染一个普通的红色矩形太简单了。你至少可以从中得到一些乐趣，或许还可以稍微改变一下形状。让我们尝试 45 度，首先使用本地画布方法:

```
var canvasEl = document.getElementById('c');

var ctx = canvasEl.getContext('2d');

ctx.fillStyle = 'red';

ctx.translate(100, 100);

ctx.rotate(Math.PI / 180 * 45);

ctx.fillRect(-10, -10, 20, 20);
```

这是你如何在布料上做到的。(结果见图 2)。

```
var canvas = new fabric.Canvas('c');

// create a rectangle with angle=45

var rect = new fabric.Rect({

    left: 100,

    top: 100,

    fill: 'red',

    width: 20,

    height: 20,

    angle: 45

});

canvas.add(rect);
```

![Figure 1 Red Rectangle Drawn with Fabric or Native Canvas Methods ](img/d0102800fd1996b16a50428a54f9f46c.png)

*图 2 红色，用布料或原生画布绘制的旋转矩形方法* 
这里发生了什么？在 Fabric 中，您只需将对象的角度值更改为 45°。然而，对于本机方法，需要做更多的工作。记住你不能在物体上操作。相反，您必须调整

整个画布位图(ctx.translate，ctx.rotate)的位置和角度符合您的需要。然后再次绘制矩形，记住适当地偏移位图(-10，-10)，以便它仍然在点 100，100 处渲染。另外，在旋转画布位图时，必须将度数转换为弧度。

我相信你已经开始明白为什么 Fabric 会存在，它隐藏了多少低级的样板文件。

让我们看看另一个例子:跟踪画布状态。

如果在某个时候，您想将矩形移动到画布上稍有不同的位置，该怎么办？如果不能对对象进行操作，您怎么能做到这一点呢？你会在画布位图上调用另一个 fillRect 吗？不完全是。调用另一个 fillRect 命令实际上会在画布上已经绘制的内容上绘制一个矩形。要移动矩形，您需要首先擦除任何以前绘制的内容，然后在新的位置绘制矩形(参见图 3)。

```
var canvasEl = document.getElementById('c');

...

ctx.strokRect(100, 100, 20, 20);

...

// erase entire canvas area

ctx.clearRect(0, 0, canvasEl.width, canvasEl.height);

ctx.fillRect(20, 50, 20, 20);
```

以下是如何用布料实现这一目标:

```
var canvas = new fabric.Canvas('c');

...

canvas.add(rect);

...

rect.set({ left: 20, top: 50 });

canvas.renderAll();
```

![Figure 3 Red Rectangle Drawn at a New Location ](img/d8641a4f42652ae7c24fc85cffd4cb9f.png)

*图 3 在新位置绘制的红色矩形*

请注意一个非常重要的区别:使用 Fabric，您不需要在尝试修改任何内容之前删除内容。您仍然可以简单地通过更改对象的属性来处理对象，然后再次渲染画布以获得新的图片。

## 目标

在上一节中，您看到了如何通过实例化 fabric 来处理矩形。Rect 构造函数。当然，织物也包括其他基本形状——圆形、三角形、椭圆形等等。这些形状作为 fabric 显示在 fabric“命名空间”下。圆形，面料。三角形，面料。椭圆等等。面料提供七种基本形状:

*   [织物。圆圈](http://fabricjs.com/docs/symbols/fabric.Circle.html)
*   [织物。椭圆](http://fabricjs.com/docs/symbols/fabric.Ellipse.html)
*   [织物。行](http://fabricjs.com/docs/symbols/fabric.Line.html)
*   [织物。多边形](http://fabricjs.com/docs/symbols/fabric.Polygon.html)
*   [织物。折线](http://fabricjs.com/docs/symbols/fabric.Polyline.html)
*   [fabric.Rect](http://fabricjs.com/docs/symbols/fabric.Rect.html)
*   [织物。三角形](http://fabricjs.com/docs/symbols/fabric.Triangle.html)

要画一个圆，只需创建一个圆形对象，并将其添加到画布上。

```
var circle = new fabric.Circle({

    radius: 20, fill: 'green', left: 100, top: 100

});

var triangle = new fabric.Triangle({

    width: 20, height: 30, fill: 'blue', left: 50, top: 50

});

canvas.add(circle, triangle);
```

你可以对任何其他基本形状做同样的事情。图 4 显示了在位置 100，100 绘制的绿色圆圈和在位置 50，50 绘制的蓝色三角形的示例。

![Figure 4 A Blue Triangle and a Green Circle Drawn with Fabric ](img/82a35cae1c3320d54cad64b772ee29c6.png)

*图 4 用布料绘制的蓝色三角形和绿色圆圈*

操纵对象

创建图形对象——矩形、圆形或其他——只是开始。在某些时候，你可能需要修改你的对象。也许某个动作会触发状态的改变或者播放某种动画。或者您可能希望在某些鼠标交互中更改对象属性(如颜色、不透明度、大小、位置)。

Fabric 负责画布渲染和状态管理。我们只需要修改对象本身。前面的示例演示了 set 方法以及调用 set({ left: 20，top: 50 })如何将对象从其先前的位置移动。以类似的方式，您可以更改对象的任何其他属性。

如您所料，Fabric 对象具有与定位(左、上)、尺寸(宽度、高度)、渲染(填充、不透明度、描边、strokeWidth)、缩放和旋转(scaleX、scaleY、angle)以及翻转(flipX、flipY)相关的属性。是的，在 Fabric 中创建翻转对象就像将 flip*属性设置为 true 一样简单。

您可以通过 get 方法读取这些属性，并通过 set 设置它们。下面是一个如何改变红色矩形属性的例子。图 5 显示了结果。

```
var canvas = new fabric.Canvas('c');

...

canvas.add(rect);

rect.set('fill', 'red');

rect.set({ strokeWidth: 5, stroke: 'rgba(100,200,200,0.5)' });

rect.set('angle', 15).set('flipY', true);
```

![Figure 5 Red, Rotated, Stroked Rectangle Drawn with Fabric ](img/e5e001f423f7cfc57cd82dc1222daca1.png)

*图 5 用织物绘制的红色旋转描边矩形*

首先，填充值设置为“红色”。下一个语句设置 strokeWidth 和 stroke 值，给矩形一个浅绿色的 5 px 描边。最后，代码更改了 angle 和 flipY 属性。请注意这三个语句使用的语法略有不同。

这表明 set 是一种通用的方法。你可能会经常使用它，它意味着尽可能的方便。吸气剂呢？有一个通用的 get 方法，也有一些特定的方法。若要读取对象的 width 属性，可以使用 get('width ')或 getWidth()。要获得 scaleX 值，可以使用 get('scaleX ')、getScaleX()等等。每个“公共”对象属性(stroke、strokeWidth、angle 等)都有一个类似 getWidth 或 getScaleX 的方法。

您可能已经注意到，在前面的例子中，对象是用与我们刚刚在 set 方法中使用的配置散列相同的配置散列创建的。您可以在创建时“配置”对象，也可以在以后使用 set 方法:

```
var rect = new fabric.Rect({ width: 10, height: 20, fill: '#f55', opacity: 0.7 });

// or functionally identical

var rect = new fabric.Rect();

rect.set({ width: 10, height: 20, fill: '#f55', opacity: 0.7 });
```

## 默认选项

此时，您可能想知道在没有传递任何“配置”对象的情况下创建一个对象会发生什么。它还有那些属性吗？

是的。如果在创建过程中省略了特定设置，Fabric 中的对象将始终具有一组默认属性。您可以使用下面的代码来亲自了解这一点:

```
var rect = new fabric.Rect(); // notice no options passed in

rect.getWidth(); // 0

rect.getHeight(); // 0

rect.getLeft(); // 0

rect.getTop(); // 0

rect.getFill(); // rgb(0,0,0)

rect.getStroke(); // null

rect.getOpacity(); // 1
```

这个矩形有一组默认的属性。它的位置为 0，0，为黑色且完全不透明，没有笔划和尺寸(宽度和高度均为 0)。因为没有给出维度，所以在画布上看不到。赋予它任何正值的宽度和高度都会在画布的左上角显示一个黑色的矩形，如图 6 所示。

![Figure 6 How Default Rectangle Looks When Given Dimensions ](img/d41a6d282ede134b0bb68b86a8eaac34.png)

*图 6 给定尺寸时默认矩形的外观*

## 等级和继承

Fabric 对象不是彼此独立存在的。他们形成了一个非常精确的等级体系。大多数对象从根 fabric . object . fabric 继承。对象根对象(或多或少)表示二维形状，位于二维画布平面中。它是一个实体，具有左/上和宽/高属性，以及一系列其他图形特征。为对象列出的属性(填充、描边、角度、不透明度、翻转*等)对于从 fabric.Object 继承的所有 Fabric 对象都是通用的

这种继承允许您在 fabric 上定义方法。对象，并在所有子“类”之间共享它们。例如，如果您希望所有对象都有一个 getAngleInRadians 方法，您只需在 fabric 上创建它。Object.prototype，如下:

```
fabric.Object.prototype.getAngleInRadians = function() {

    return this.getAngle() / 180 * Math.PI;

};

var rect = new fabric.Rect({ angle: 45 });

rect.getAngleInRadians(); // 0.785...

var circle = new fabric.Circle({ angle: 30, radius: 10 });

circle.getAngleInRadians(); // 0.523...

circle instanceof fabric.Circle; // true

circle instanceof fabric.Object; // true
```

如您所见，该方法立即在所有实例上可用。

即使子“类”继承自 fabric。对象，它们通常还定义自己的方法和属性。例如，织物。圆需要半径属性和结构。image——我们稍后将会看到——需要 getElement 和 setElement 方法来访问和设置产生图像实例的 HTML 元素。

## 帆布

既然您已经详细了解了对象，让我们回到 canvas。

在所有 Fabric 示例中，您首先看到的是一个画布对象的创建——new Fabric。画布(“…”)。布料。Canvas 对象作为

<canvas>元素的包装器，负责管理特定画布上的所有 Fabric 对象。它接受一个元素的 ID 并返回一个 fabric.Canvas 实例。</canvas>

您可以向其中添加对象、从其中引用对象或移除对象，如下所示:

```
var canvas = new fabric.Canvas('c');

var rect = new fabric.Rect();

canvas.add(rect); // add object

canvas.item(0); // reference fabric.Rect added earlier (first object)

canvas.getObjects(); // get all objects on canvas (rect will be first and only)

canvas.remove(rect); // remove previously-added fabric.Rect
```

管理对象是 fabric 的主要目的。Canvas，但它也充当配置主机。您是否需要为整个画布设置背景颜色或图像，将所有内容裁剪到某个区域，设置不同的宽度和高度，或者指定画布是否是交互式的？所有这些选项(以及其他选项)都可以在 fabric 上设置。画布，无论是创作时还是后来。

```
var canvas = new fabric.Canvas('c', {

    backgroundColor: 'rgb(100,100,200)',

    selectionColor: 'blue',

    selectionLineWidth: 2

    // ...

});

// or

var canvas = new fabric.Canvas('c');

canvas.backgroundImage = 'http://...';

canvas.onFpsUpdate = function(){ /* ... */ };

// ...
```

## 交互性

Fabric 独特的内置特性之一是对象模型之上的交互层。对象模型的存在是为了允许对画布上的对象进行编程访问和操作，但是在外部——在用户级别——有一种方法可以通过鼠标(或通过触摸设备上的触摸)来操作这些对象。一旦你通过新的 fabric 初始化了画布。Canvas('…')调用，可以选择对象(参见图 7)，拖动它们，缩放或旋转它们，甚至将它们分组(参见图 8)以在一个块中操纵它们！

![Figure 7 Red, Rotated Rectangle in Selected State (Controls Visible) ](img/6dd6d41cd71ebc998d30ee6039fba93b.png)

*图 7 红色，旋转矩形处于选中状态(控件可见)*

![Figure 8 Rectangle and Circle Grouped (Controls Visible)](img/5f2c68da84ff1adae2f75d147d97a143.png)

*图 8 矩形和圆形分组(控件可见)*

如果您希望允许用户在画布上拖动某些东西——比如说一个图像——您需要做的就是初始化画布并向其中添加一个对象。不需要额外的配置或设置。

要控制这种交互性，可以将画布对象上的 Fabric 选择布尔属性与各个对象的可选布尔属性结合使用:

```
var canvas = new fabric.Canvas('c');

...

canvas.selection = false; // disable group selection

rect.set('selectable', false); // make object unselectable
```

但是如果你根本不想要交互层呢？如果是这样的话，你可以随时更换面料。Canvas with fabric.StaticCanvas .初始化语法完全相同:

```
var staticCanvas = new fabric.StaticCanvas('c');

staticCanvas.add(

    new fabric.Rect({

        width: 10, height: 20,

        left: 100, top: 100,

        fill: 'yellow',

        angle: 30

    }));
```

这创建了一个“更轻”的 canvas 版本，没有任何事件处理逻辑。您仍然可以使用整个对象模型——添加、移除或修改对象，以及更改任何画布配置。所有这些仍然有效，只是事件处理功能消失了。

在本文的后面，当我查看定制构建选项时，您将会看到如果 StaticCanvas 是您所需要的，您甚至可以创建一个更轻量级的 Fabric 版本。如果您需要在应用程序中使用非交互式图表或非交互式图像，这可能是一个不错的选择。

## 形象

在画布上添加矩形和圆形很有趣，但正如你现在所能想象的，Fabric 也使处理图像变得非常容易。这里是你如何实例化织物。图像对象并将其添加到画布中，首先以 HTML 格式，然后以 JavaScript 格式:

### 超文本标记语言

```
<canvas id="c"></canvas>

<img src="my_image.png" id="my-image">
```

### Java Script 语言

```
var canvas = new fabric.Canvas('c');

var imgElement = document.getElementById('my-img');

var imgInstance = new fabric.Image(imgElement, {

    left: 100,

    top: 100,

    angle: 30,

    opacity: 0.85

});

canvas.add(imgInstance);
```

请注意，您向 fabric 传递了一个图像元素。图像构造器。这将创建一个 fabric 实例。看起来与文档中的图像相似的图像。此外，您立即将“左/上”值设置为 100/100，“角度”设置为 30，“不透明度”设置为 0.85。一旦图像被添加到画布上，它就会以 30 度角呈现在位置 100，100 处，并且略微透明(参见图 9)。还不错！

![Figure 9 Slightly Transparent and Rotated Image, Rendered with Fabric](img/ec979d2f7ff656bab7344913f35d1fce.png)

*图 9 轻微透明和旋转的图像，用织物渲染*

如果文档中没有图像，只有图像的 URL，可以使用 fabric。Image.fromURL:

```
fabric.Image.fromURL('my_image.png', function(oImg) {

    canvas.add(oImg);

});
```

看起来很简单，不是吗？就叫面料。Image.fromURL，带有图像的 URL，并在加载和创建图像后给它一个回调函数来调用。回调函数接收已经创建的 fabric。图像对象作为它的第一个参数。此时，您可以将它添加到画布上，或者先更改它，然后再添加它，如下所示:

```
fabric.Image.fromURL('my_image.png', function(oImg) {

    // scale image down, and flip it, before adding it onto canvas

    oImg.scale(0.5).setFlipX(true);

    canvas.add(oImg);

});
```

## 路径和路径组

我们已经看了简单的形状和图像。更复杂、更丰富的形状和内容呢？遇见路径和路径组，权力的一对。

Fabric 中的路径表示形状的轮廓，可以用其他方式填充、描边和修改。路径由一系列命令组成，本质上是模仿钢笔从一点到另一点。在移动、直线、曲线和圆弧等命令的帮助下，路径可以形成极其复杂的形状。在路径组(PathGroup)的帮助下，可能性会更大。Fabric 中的路径非常类似于 SVG <path>元素。它们使用相同的命令集，可以从<path>元素创建，并且可以序列化到其中。稍后我将描述更多关于序列化和 SVG 解析的内容，但是现在值得一提的是，您可能很少会手工创建路径实例。相反，您将使用 Fabric 的内置 SVG 解析器。但是为了理解什么是路径对象，让我们手动创建一个简单的路径对象(结果见图 10):</path></path>

```
var canvas = new fabric.Canvas('c');

var path = new fabric.Path('M 0 0 L 200 100 L 170 200 z');

path.set({ left: 120, top: 120 });

canvas.add(path);
```

![Figure 10 Simple Path Rendered with Fabric](img/d7582c5f8b7e2a8dd1bf56034c7c6b35.png)

*图 10 用织物渲染的简单路径*

这里您实例化了 fabric。对象，并向它传递一串路径指令。这看起来很神秘，但实际上很容易理解。m 代表移动命令，告诉隐形笔移动到点 0，0。l 代表 line，让笔画一条线到点 200，100。然后，另一个 L 创建了一条到 170，200 的线。最后，z 强制绘图笔关闭当前路径并最终确定形状。

因为布料。Path 就像 Fabric 中的任何其他对象一样，您也可以更改它的一些属性，或者对它进行更多的修改，如这里和图 11 所示:

```
...

var path = new fabric.Path('M 0 0 L 300 100 L 200 300 z');

...

path.set({ fill: 'red', stroke: 'green', opacity: 0.5 });

canvas.add(path);
```

![Figure 11 A Simple, Modified Path](img/40ecf11c269580dbb1a17f1dea10898b.png)

*图 11 一个简单的修改路径*

出于好奇，我们来看看稍微复杂一点的路径语法。你会明白为什么手动创建路径可能不是最好的主意:

```
...

var path = new fabric.Path('M121.32,0L44.58,0C36.67,0,29.5,3.22,24.31,8.41

c-5.19,5.19-8.41,12.37-8.41,20.28c0,15.82,12.87,28.69,28.69,28.69c0,0,4.4,

0,7.48,0C36.66,72.78,8.4,101.04,8.4,101.04C2.98,106.45,0,113.66,0,121.32

c0,7.66,2.98,14.87,8.4,20.29l0,0c5.42,5.42,12.62,8.4,20.28,8.4c7.66,0,14.87

-2.98,20.29-8.4c0,0,28.26-28.25,43.66-43.66c0,3.08,0,7.48,0,7.48c0,15.82,

12.87,28.69,28.69,28.69c7.66,0,14.87-2.99,20.29-8.4c5.42-5.42,8.4-12.62,8.4

-20.28l0-76.74c0-7.66-2.98-14.87-8.4-20.29C136.19,2.98,128.98,0,121.32,0z');

canvas.add(path.set({ left: 100, top: 200 }));
```

在这里，M 仍然代表移动命令，因此笔从点 121.32，0 开始它的绘制旅程。然后有一个 L (line)命令将笔带到 44.58，0。到目前为止一切顺利。现在是 C 命令，代表“三次贝塞尔曲线”该命令使钢笔从当前点到 36.67，0 绘制一条贝塞尔曲线。它使用 29.5，3.22 作为一条线的起点控制点，使用 24.31，8.41 作为线的终点控制点。这

整个操作之后是十几个其他的三次贝塞尔命令，最终创建了一个漂亮的箭头形状，如图 12 所示。

![Figure 12 Complex Path Rendered with Fabric](img/4ef9e5683af6c3ba256b13b73fe4a135.png)

*图 12 用织物渲染的复杂路径*

很有可能，你不会直接和这样的野兽合作。相反，您可以使用 fabric.loadSVGFromString 或 fabric.loadSVGFromURL 方法加载整个 SVG 文件，并让 fabric 的 SVG 解析器遍历所有 SVG 元素并创建相应的 Path 对象。

在这种情况下，虽然 fabric 的 Path 对象通常表示一个 SVG <path>元素，但是 SVG 文档中经常出现的一组路径被表示为 PathGroup 实例(Fabric。路径组)。PathGroup 不过是一组路径对象，因为 fabric。PathGroup 继承自 fabric。对象，它可以像任何其他对象一样添加到画布上，并以同样的方式进行操作。</path>

就像路径一样，您可能不会直接使用路径组。但是如果您在解析一个 SVG 文档后偶然发现了它，您就会确切地知道它是什么以及它的用途。

## 暂时结束

我只触及了布料的皮毛。现在，您可以轻松创建任何简单形状、复杂形状或图像。将它们添加到画布上，并按照您想要的任何方式修改它们——它们的位置、尺寸、角度、颜色、笔画、不透明度——只要您想得到。

在本系列的下一篇文章中，我将讨论如何与团队合作；动画；文本；SVG 解析、渲染和序列化；事件；图像过滤器等。与此同时，你可以随意看看[带注释的演示](http://fabricjs.com/demos/)或[基准](http://fabricjs.com/benchmarks/)，在[栈溢出](http://stackoverflow.com/questions/tagged/fabricjs)参与讨论，或者直接进入[文档](http://fabricjs.com/docs/)、[维基](https://github.com/kangax/fabric.js/wiki)和[源代码](https://github.com/kangax/fabric.js)。你也可以在 [MSDN IE 开发者中心](http://msdn.microsoft.com/en-us/library/ie/hh771733%28v=vs.85%29.aspx)了解更多关于 HTML5 Canvas 的信息，或者在 Script Junkie 上查看 Rey Bango 的[HTML 5 Canvas 元素介绍](http://msdn.microsoft.com/en-us/magazine/ff961912.aspx)。

体验布料的乐趣！希望你旅途愉快。

*本文最初发表于[http://msdn.microsoft.com/en-us/magazine/jj856929.aspx](http://msdn.microsoft.com/en-us/magazine/jj856929.aspx)，经允许在此转载。*

## 分享这篇文章