# Snap.svg 简介

> 原文：<https://www.sitepoint.com/introduction-snap-svg/>

尽管 SVG 已经存在了十多年，但在过去几年中，它作为一种在 web 应用程序中绘制图表的方式变得流行起来，这要归功于一些伟大的库，它们让开发人员可以毫不费力地获得漂亮的图表和绘图:特别是用于图表的 [D3.js](http://d3js.org/) 和用于很酷的 SVG 绘图和动画的[拉斐尔](http://raphaeljs.com/)。

新的优秀图书馆最近已经出现；它们为前端开发人员和设计人员提供了新方法和惊人的新功能:

*   [Snap.svg](http://snapsvg.io/) ，正如我们将要看到的，提供了最新的 svg 特性，如遮罩、裁剪、图案、渐变等…
*   PathsJs 是一个用于基于 SVG 的图表创建的最小库。它通过生成可用于模板引擎的 SVG 路径来支持反应式编程。它最好与基于小胡子的模板引擎一起工作，比如 [Ractive](http://www.ractivejs.org/) 。
*   虽然不是基于 SVG 的，但是 [P5](http://p5js.org/) 值得一提。这是一个尝试，而且显然是一个很好的尝试，来克服影响 HTML5 canvas 元素的传统问题——尤其是交互。

在本文的其余部分，我们将从基础开始，仔细研究 Snap.svg。

## 拉菲尔

如果你还没有机会看一看拉斐尔，你可能应该看一看。这是由 Dmitry Baranovskiy 作为个人项目创建的一个不错的 JavaScript。虽然它是作为一个个人项目开始的，但其结果在界面(非常清晰和一致)、性能和外观(尤其是动画)方面是显著的。该库更倾向于“徒手”绘图和动画，而不是图表。[graph al](http://g.raphaeljs.com/)扩展后来被发布来解决这个问题，但它没有像 D3 那样流行和广泛。

尽管走在其他图书馆的前面，拉斐尔还是开始显示出它的局限性。例如，为了与旧的浏览器兼容，Raphaë不支持那些能让你的动画脱颖而出的很酷的新 SVG 特性。

这就是为什么它的作者决定重新开始一个新项目 Snap.svg，它当然受益于设计 Raphaë所积累的经验。Snap.svg 也打破了过去，允许引入一种全新的特效。

## 哦，太好了！

在深入研究 Snap 的语法和开始使用几个示例之前，让我们快速回顾一下这个新库的优缺点:

**优点:**

*   它支持我们上面提到的所有酷功能。
*   捕捉可以环绕和动画现有的 SVG。您可以使用 Adobe Illustrator、Inkscape 或 Sketch 等工具生成 SVG，或者异步加载 SVG 字符串并查询出将 SVG 文件转换为 sprite 所需的片段。
*   它是免费和开源的。

**缺点:**

*   这是一个低级的库，所以如果你需要可视化数据，不幸的是还不支持图表。
*   不支持数据绑定。
*   Snap 是一个年轻的项目，还没有完全成熟。这对于你的个人项目来说已经很棒了，但是在复杂的项目中使用它之前，你必须权衡这一点。

正如我们提到的，Snap 使用了旧浏览器不支持的功能。尽管还没有给出一个完整的、更新的兼容性表，但是这个库至少应该可以在以下浏览器版本(以及更新的版本)中正常工作:

*   火狐 ESR 18
*   IE 9.0.8
*   铬 29
*   歌剧 24

## Snap 入门

从 [GitHub 库](https://github.com/adobe-webplatform/Snap.svg/archive/v0.3.0.zip)下载源文件后，你可以解压它们并寻找`dist`文件夹，其中包含构建好的分发文件。关于用 Grunt 构建 snap 的详细说明，或者检查最新版本，请看[这里](https://github.com/adobe-webplatform/Snap.svg/)。

一旦你在新项目的`js`文件夹中复制了这个文件的缩小版本，只需要在你的 HTML 页面中包含这个脚本。假设它位于项目的根目录中，您可以在页面的结束标记`body`之前添加这一行:

```
<script src="/js/snap.svg-min.js"></script>
```

现在我们准备为矢量图形创建一个绘图区域。我们有两种方法可以做到这一点:

*   创建一个全新的绘图表面，它将被追加到页面的 DOM 中(在`body`中)。
*   重用现有的 DOM 元素，并将其包装在 Snap 结构中。您可以包装任何元素，但是对于绘图方法，您需要一个 SVG 元素。

第一种方法允许您在 JavaScript 代码中创建时显式设置表面区域的宽度和高度。如果您想在表示和内容之间实现更大程度的分离，您可以使用第二种方法，在 CSS 规则中指定值。在高层次上，第一种方法允许您动态调整绘图表面的外观，但如果您不需要，第二种方法更符合 MVC。此外，包装允许您导入和修改用外部工具创建的 SVG 绘图，如简介部分所述。

因此，例如，要创建一个新的 800 乘 600 像素的绘图区域，您只需要下面一行 JavaScript 代码:

```
var s = Snap(800, 600);
```

相反，如果您想包装一个现有的，就说`#complexSVGfromIllustrator`:

```
<svg id='complexSVGfromIllustrator' version="1.1" >
  ...
</svg>
```

您仍然可以使用一行 JavaScript 来导入绘图表面:

```
var s = Snap('#complexSVGfromIllustrator');
```

**旁注:**对于好奇的读者来说:如果你在创建后检查快照对象，你会注意到它们有一个`paper`字段，证明拉斐尔的遗产。

## 形状

一旦我们创建了我们的绘图表面，我们的`Snap`包装器，是时候在上面绘制一些形状了。假设你想画一个圆:

```
var paper = Snap('#complexSVGfromIllustrator'),
    circle = paper.circle(100, 50, 10);
```

从[文档](http://snapsvg.io/docs/#Paper.circle)中可以看到，`circle()`方法中的前两个参数是圆心坐标，第三个参数是圆的半径。所有这些参数都是必需的，不提供它们将导致抛出错误。与所有其他绘制方法一样，`circle()`方法将返回一个对象的引用。

您也可以绘制[省略号](http://snapsvg.io/docs/#Paper.ellipse)，如下面的代码示例所示。这次需要垂直和水平半径。同样，所有参数都是必需的。

```
var ellipse = paper.ellipse(100, 50, 10, 20);
```

如果你想画一个[矩形](http://snapsvg.io/docs/#Paper.rect)，使用下面的代码。这将创建一个矩形，其左上角为(100 像素，100 像素)，宽度为 200 像素，高度为 200 像素。

```
var r = paper.rect(100, 100, 200, 300);
```

关于`rect()`方法很酷的一点是，它还接受两个可选的参数来控制圆角的半径，独立于垂直轴和水平轴。如果没有传递，这些参数默认为 0，但是请注意，如果您只传递了一个参数(水平半径)，第二个参数将不会被设置为零，而是两个参数将采用相同的值。

```
var rect = paper.rect(100, 100, 200, 300, 10); //equivalent to  paper.rect(100, 100, 200, 300, 10, 10);
```

现在，如果您想从头开始，您可以创建另一个绘图表面，或者您可以使用`paper.clear()`方法从`paper`中删除所有绘图。

### 线条和多边形

为了涵盖更复杂的绘图，我们需要退一步，谈谈画线。正如您所料，该方法采用一条线的端点的四个坐标，如下所示。

```
var line = paper.line(10, 100, 110, 200);
```

更有趣的是绘制复杂的[折线的可能性](http://snapsvg.io/docs/#Paper.polyline) : `var line = paper.polyline(10, 100, 110, 200);`原则上等同于上面的`line()`方法，但是你可能会对它的视觉效果感到惊讶。要了解原因，让我们试试这个

```
var p1 = paper.polyline(10, 10, 10, 100, 210, 20, 101, 120);
```

`paper.polyline()`和`paper.polygon()`是同一方法的别名，默认情况下，生成的(闭合)多边形是用黑色填充绘制的，没有描边。这就是为什么您看不到上面用`polyline()`画的那条线(尽管您可以通过检查页面来检查它的 SVG 代码是否确实被附加到了它的容器中)。

为了改变这种行为以及其他元素的外观，我们必须引入属性。

### 属性

Snap 元素的属性概念比通常的要宽泛一些，这意味着它在同一个接口下包括 HTML 属性和 CSS 属性(而大多数其他库在 HTML 属性的`.attr()`方法和。用于 CSS 的 style()')。通过在 Snap wrapper 对象上使用`element.attr()`方法，您可以设置它的`class`或`id`，以及它的颜色或宽度。

如上所述，使用 Snap 有两种方法可以将 CSS 属性分配给元素。一种方法是将这些属性包含在一个单独的 CSS 文件中，然后将适当的类分配给元素:

```
.big-circle {
  stroke: red;
  stroke-width: 2;
  fill: yellow;
}
```

```
circle.attr({class: 'big-circle'});
```

通过使用 JavaScript 分配这些属性可以获得相同的结果:

```
circle.attr({
  stroke: 'red';
  stroke-width: 2;
  fill: 'yellow';
});
```

同样，第一种方法可以更好地分离内容和表示，而第二种方法提供了动态改变属性的可能性。如果你想混合使用这两种策略，请记住，在 CSS 文件中定义的规则将胜过你用`element.attr()`指定的规则，不管它们被指定给元素的时间顺序。

如果您还没有维护对想要样式化的元素的引用，不要担心，您可以使用 CSS 选择器轻松地获取它:

```
circle = paper.select('circle');  //First circle in paper's DOM tree
circle = paper.select('circle.big-circle');  //First circle in paper's DOM tree which has class 'big-circle'
circle = paper.select('circle:nth-child(3)');  //Third circle in paper's DOM tree
circle = paper.selectAll('circle.big-circle');  //All circles in paper's DOM tree with class 'big-circle'
```

### 组

可以对 SVG 元素进行分组，以便可以更容易地将常见的转换和事件处理应用于组中的所有元素。[创建群组](http://snapsvg.io/docs/#Paper.g)很简单:

```
var group = paper.g(circle, rect);
var g2 = paper.group(rect, circle, ellipse);  //an alias for paper.g
```

小心:顺序或论点很重要！其次，如果您将一个元素分配给一个组，它将从它可能已经属于的任何组中删除。

当然，元素也可以在创建后添加到现有的组中:

```
group.add(circle);
```

### 形象

Snap 支持在 SVG 元素中嵌套光栅图像[和](http://snapsvg.io/docs/#Paper.image),异步加载并仅在加载完成时显示。

```
var img = paper.image('bigImage.jpg', x, y, width, height);
```

得到的对象可以被视为一个 SVG 元素。注意，如果您稍后在图像上使用`select()`来检索它们，那么创建的包装器将是 HTML 元素的包装器，因此 SVG 元素可用的大多数方法将不受支持。

### 转换

我们已经看到了如何绘制不对称的多边形，如椭圆和矩形。然而，基本方法限制我们绘制这些与笛卡尔轴对齐的图形。如果我们想画一个椭圆，它的轴相对于 x-y 轴旋转了 45 °,会怎么样呢？我们不能在创建方法中指定这一点，但是我们可以使用转换来获得相同的结果。

同样，我们可能需要旋转一个图像，或者在创建后的某个时刻移动一个元素(或者一个组)。 [`transform()`方法](http://snapsvg.io/docs/#Element.transform)允许我们这样做，通过传递一个 SVG 转换字符串:

```
var ellipse = paper.ellipse(100, 50, 10, 20);
ellipse.transform('r45');
```

此方法可以接受字符串或对象作为输入。我们还可以使用与一个元素相关联的变换矩阵将相同的变换应用于另一个元素:

```
var e1 = paper.ellipse(100, 50, 10, 20),
    e2 = paper.ellipse(200, 50, 12, 24);
e1.transform('r45');
e2.transform(e1.matrix);
```

**小心:**第二个元素的变换中心仍然是第一个元素的变换中心，所以最终的效果可能会让你大吃一惊。

`transform()`方法也可以用于检索它所调用的元素的转换描述符对象——只需不带参数地调用它。在嵌套元素的情况下，该描述符可用于检索局部变换矩阵和差分矩阵:

```
var g1 = paper.group(),
    e1 = paper.ellipse(200, 50, 12, 24);

g1.add(e1);
g1.transform('r30');
e1.transform('t64.6447,-56.066r45,0,0');
console.log(e1.transform());
```

## 结论

这篇文章介绍了 Snap.svg 的基础知识。如果你有兴趣看到最酷的东西，请继续关注，因为很快就会发布一篇高级后续文章。

如果您想了解有关数据可视化和快照的更多信息，这里有一些有用的资源:

1.  Snap.svg [教程](http://snapsvg.io/start/)。
2.  [幻灯片](http://mlarocca.github.io/graphicalweb2014/)来自关于数据可视化和高级技术的演示。
3.  在 CodePen 上看看上面的一些[代码](http://codepen.io/mlarocca/pen/EdIBD)。

## 分享这篇文章