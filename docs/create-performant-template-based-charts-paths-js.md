# 如何使用 Paths.js 创建基于模板的高性能图表

> 原文：<https://www.sitepoint.com/create-performant-template-based-charts-paths-js/>

在最近的一篇[帖子](https://www.sitepoint.com/introdution-snap-svg/)中，我们讨论了浏览器中数据可视化的艺术状态——特别是 SVG 库。在那里，我们关注于 [Snap.svg](http://snapsvg.io/) ，但是我们引入了 [Paths.js](https://github.com/andreaferretti/paths-js) 作为一个可行的替代方案——与模板引擎或数据绑定库一起使用。公平地说，Paths 可以做得更多，正如我们将在接下来的部分中展示的那样，用真实的用例带您浏览它的逻辑。

## 介绍

Paths 的主要目的是帮助前端开发人员通过直观的界面生成性能更好的 SVG 路径。顾名思义，尽管有像`rect`或`circle`这样的形状的原语，但一切都可以简化为路径。这种方法统一了不同的图表，提供了一致的界面，其中绘制命令总是返回准备好要绘制的路径列表。通过将静态模板引擎(如小胡子或车把)替换为数据绑定库，如 [Ractive.js](http://www.ractivejs.org/) 、 [Angular](http://angularjs.org/) 或 [React](http://facebook.github.io/react/) ，你甚至可以免费获得动画图形。

Paths.js 最大的优点可能是它提供了三个增量 API，抽象层次不断增加。最底层是一个可链接的 API，它可以生成任意的 SVG 路径。在此基础上，定义了多边形或扇形等简单几何形状的路径。最高级别的 API 允许生成一些简单的图形，这些图形可以由一组数据提供。(查看这个[演示](http://andreaferretti.github.io/paths-js-demo/)来查看可用的原语。)

实际上，注意:Paths 最好的特性是你可以在服务器端使用 Node.js 的库*和*，因为它不直接依赖任何库 [1](#fn:1 "See footnote") 。这样，您可以将图表结构和细节的生成转移到服务器上。除了加快应用程序的速度，你还可以避免向客户端发送原始数据，节省延迟并减少与客户端共享的信息量。

## 为什么是路径？

使用 Paths.js 而不是——比如说——D3 或 Snap.svg 的最大优点是，后者是必不可少的，而当与模板引擎或(甚至更好的)数据绑定框架配对时，Paths 固有地支持声明性编程。

将路径与 Ractive 或 React 之类的框架一起使用又带来了另一个好处。事实上，这些框架使用特定的优化来减少每次修改 DOM 时需要的回流和重绘次数；它们保存 DOM 的“影子”副本，并以一种“批处理模式”对其进行更新，最后以尽可能少的更改更新真正的 DOM。

这些框架发挥作用的另一个领域是事件处理。默认情况下，它们使用事件委托，从而在同一事件被附加到一系列元素的情况下提高性能。解决方案是简单地将这些事件处理程序附加到一些公共的元素容器中，但是当使用命令式方法时，很容易忽略这种模式(会带来可怕的后果——比如没有响应的页面)。

最后，Paths 是轻量级和模块化的:您可以只加载您实际需要的组件，关注图表，或者只处理 SVG。Paths 是那些专注于几件事并试图优化它们的库之一。通常，您可以将这些类型的库组合在一起执行复杂的任务。另一方面，D3 有许多额外的实用方法——如果你需要它们，这很好，因为你在一个地方有你需要的所有东西，但是如果你不需要，就有点笨重。

## 使用路径

如前所述，您可以在 Node.js 中或浏览器上使用路径。在后一种情况下，您可以将其作为 AMD 模块或独立的库来加载。

### 节点上的路径

如果您想在服务器上使用它，首先通过在控制台上键入以下命令来安装它(假设您已经正确安装了 node，并且它位于全局路径中):

`npm install paths-js`

安装后，您可以加载各个模块:

```
var Pie = require('paths-js/pie');
```

### 浏览器上的路径:AMD 模块

Paths.js 随 [Bower](http://bower.io/) 分发，可以从命令行安装:

`bower install paths-js`

当然，也可以从 GitHub 上的[库](https://github.com/andreaferretti/paths-js/tree/master/dist/amd)手动下载。

Paths 被构造成各种 AMD 模块，并且可以用 AMD 模块加载器来加载。使用 [RequireJS](http://requirejs.org/) (假设您已经安装了带有 Bower 的路径)，您可以这样配置它:

```
require.config({
  'paths': 'components/paths-js/dist/amd'
});
```

实际路径将取决于您的 Bower 配置，或者对于手动下载，取决于您的文件夹的结构。(小心放置上面链接的`amd`文件夹。)

正确配置后，您可以轻松地要求各个模块:

```
var Pie = require('paths/pie');
```

### 浏览器上的路径:独立脚本

如果您希望避免使用 AMD 模块，您可以安全地将路径作为独立的脚本:您需要的文件是`dist/global/paths.js`。一旦包含在页面中，`paths`对象将在全局范围内可用，因此单个模块将作为`paths.Pie`、`paths.Polygon`等可用。除了冗长之外，您还失去了只导入您需要的模块的能力——但是如果您需要很多模块，这将产生可以忽略的影响。

## 低级 API

如前所述，最底层的 API 目标是创建路径。创建目标就像调用一个构造函数一样简单:`Path()`。整个 API 都是可链接的，因此您可以通过调用先前调用结果的方法来创建复杂的路径。对象提供了增量扩展当前路径的方法；保留对路径中最后一个点的引用，可以从该点添加直线或曲线，模仿路径的 SVG 语法。您需要的主要方法有:

1.  `moveto(x, y)`:将光标移动到传递的坐标。
2.  `lineto(x, y)`:从路径的终点到这些坐标画一条线。
3.  `curveto(x1, y1, x2, y2, x, y)`:使用(x1，y1)作为曲线起点的控制点，使用(x2，y2)作为曲线终点的控制点，绘制从当前点到(x，y)的三次贝塞尔曲线。
4.  `smoothcurveto(x2, y2, x, y)`:绘制从当前点到(x，y)的三次贝塞尔曲线，根据第二个控制点和前一个命令(如果有)隐式计算第一个控制点。
5.  `arc('rx', 'ry', 'xrot', 'large_arc_flag', 'sweep_flag', 'x', 'y')`:从当前点到(x，y)画椭圆弧，通过其他参数控制椭圆半径和旋转。
6.  `closepath()`:关闭路径，将其变成多边形。

所有可用的方法都支持“详细”API，因此命名参数(以配置对象的形式)可以无缝地传递给每个方法。例如，上面的`moveto`方法可以称为`Paths().moveto({x: 10, y: 3})`或`Paths().moveto(10, 3)`。参数的名称遵循 SVG 规范。

更多的方法可用，一般来说，与 [SVG 路径命令](https://www.w3.org/TR/SVG/paths.html#PathData)有一对一的匹配。例如，`qcurveto(x1, y1, x, y)`和`smoothqcurveto(x, y)`类似于二次曲线的`curveto` `smoothcurveto`。

一般来说，使用模板引擎会更好，但这并不是绝对必要的。您可以使用命令式的路径，如下例所示。然而，这是**而不是**最佳实践:

```
<title>PathsJs test</title>
    <style type="text/css">
      .ocean {
        fill: blue;
      }
    </style>

    <svg width="640px" height="480px"><path id="testpath" class="ocean"></path></svg>

    <script type="text/javascript" src="lib/paths.js"></script>
```

```
var Path = require('paths/path');
var path = Path()
  .moveto(10, 20)
  .lineto(30, 50)
  .lineto(25, 28)
  .qcurveto(27, 30, 32, 27)
  .closepath();
document.getElementById("testpath").setAttribute("d", path.print());
```

`Path`对象的`print()`方法将构建的路径转换成相应的 SVG 数据字符串，就像它出现在路径的`d`(数据)属性中一样。一旦我们获得了这个值，我们就可以使用 CSS 选择器和`getElementById` / `getElementsBy*`方法在任何路径上手动设置适当的属性。

当然，使用模板引擎会极大地影响我们需要编写的样板代码的数量:

```
<svg width="640px" height="480px"><path d="{{ path.print() }}" fill="blue"></path></svg>
```

这样就不需要为`#testpath`手动设置一个`d`属性，甚至不需要为 path 元素分配一个 id。**这个**是创建带有路径的 SVG 图形的最佳实践风格。

## 中级 API

尽管它很强大，但实际上你很少需要低级 API。原因是 Paths 提供了一个基于它的 API，具有更高的抽象级别，允许您使用直观的语法直接创建多边形和形状。

在我们看一个例子之前，理解这个 API 是如何工作的很重要。我们已经说明了这个库产生的所有东西都是一条路径。事实上，每个中级 API 方法都返回一个具有两个字段的对象:

```
{
  path: <path object="">
  centroid: [<x>, <y>]
}
```

`path`字段包含一个符合底层 API 的`Path`对象，因此是可扩展的:描述路径数据属性的字符串可以像往常一样用它的`print()`方法检索。

相反,`centroid`字段与生成的路径并不正式相关，但尽管如此，它还是非常有用:根据经验，它被计算为图形中心的一个点，例如，它可以用来定位形状的标签。

为了理解这两个抽象层次之间的区别，我们将创建一个与上一节几乎相同的多边形，这次使用的是`Polygon`对象:

```
<svg width="640px" height="480px"><path id="testpath" class="ocean"></path><path id="testpath2" class="ocean" transform="translate(100)"></path></svg>
```

```
var Polygon = require('paths/polygon');
var polygon2 = Polygon({
  points: [[10, 20], [30, 50], [25, 28], [32, 27]],
  closed: true
});
document.getElementById("testpath2").setAttribute("d", polygon2.path.print());
```

如果您测试这段代码，您会发现这两个形状乍一看非常相似。不同之处在于，第一个是用低级 API 构建的，它的一边是用二次曲线而不是线段构建的。事实上，低级 API 的优势在于允许您在同一形状中混合不同类型的线条。

对于中级 API，没有这样一种方法允许您方便地混合它们。但是不用担心，什么也不会丢失:正如我们所说的，您总是可以编辑由`Polygon(...)`返回的`Path`:

```
<svg width="640px" height="480px"><path id="testpath" class="ocean"></path><path id="testpath2" class="ocean" transform="translate(100)"></path><path id="testpath3" class="ocean" transform="translate(50)"></path></svg>
```

```
var polygon3 = Polygon({
  points: [[10, 20], [30, 50], [25, 28]],
  closed: false
});
console.log(polygon3.path.print())
var polygon3Path = polygon3.path
              .qcurveto(27, 30, 32, 27)
              .closepath();
document.getElementById("testpath3").setAttribute("d", polygon3Path.print());
```

二级界面可用对象的完整列表如下:

*   [多边形](https://github.com/andreaferretti/paths-js#polygon):封闭和开放的多边形。
*   [半正多边形](https://github.com/andreaferretti/paths-js#semi-regular-polygon):多边形的特例:允许创建三角形、正方形、五边形等。(正多边形及其不规则变体)。它是相对于一个中心来定义的，从中心到点的线段之间的角度都是相同的，而这些点的距离可以是恒定的(正多边形)或者可以是变化的(不规则多边形)。
*   [矩形](https://github.com/andreaferretti/paths-js#rectangle):另一种特殊类型的多边形，虽然这次更直观。
*   [贝塞尔曲线](https://github.com/andreaferretti/paths-js#bezier):绘制一条通过一系列顶点的平滑贝塞尔曲线。返回的路径总是开放的。
*   [扇形](https://github.com/andreaferretti/paths-js#sector):圆形扇形。
*   [连接器](https://github.com/andreaferretti/paths-js#connector):定义为两个给定点之间的 S 形路径。(了解它的最好方法就是试一试。)

两个例子可以更好地展示半正多边形:

![Semi-Regular Polygons](img/494a110e69f50f8ec9a9824f981a1d71.png)

### 三角形(等边)

```
var SemiRegularPolygon = require('paths/semi-regular-polygon');
var triangle = SemiRegularPolygon({
  center: [50, 50],
  radii: [20, 20, 20]
});
document.getElementById("triangle").setAttribute("d", triangle.path.print());
```

### 三角形(等腰)

```
var triangleIrregular = SemiRegularPolygon({
  center: [50, 50],
  radii: [20, 30, 30]
});
```

### 平方

```
var square = SemiRegularPolygon({
  center: [50, 50],
  radii: [20, 20, 20, 20]
});
```

### 五边形

```
var pentagon = SemiRegularPolygon({
  center: [50, 50],
  radii: [20, 20, 20, 20, 20]
});
```

### 五边形(不规则)

```
var pentagonIrregular = SemiRegularPolygon({
  center: [50, 50],
  radii: [25, 20, 40, 30, 20]
});
```

## 高级 API

这是路径提供的最高级别的 API。其方法的目的是允许从要可视化的数据集开始创建完整的图表。一如既往，一切都翻译成路径！特别是，所有这些方法都返回一个包含`curves`字段的对象，该字段是一个数组，包含为每个数据点创建的形状。`curves`中的形状是具有一些相关字段的对象:

*   `item`:对应数据项的引用。
*   `index`:数据数组中对应数据项的索引。
*   一个或多个包含形状对象的字段(如饼图的`sector`，折线图的`line`和`area`)。

根据每个图表，返回的对象除了`curves`之外可能还有额外的字段。但是每个图表方法在输入中都接受一个`compute`参数。该参数允许用户传递任意数量的函数来根据输入数据计算额外的字段。(计算颜色是一个典型的例子。)

当使用这些高级方法时，模板引擎几乎是必须的，因为它们为开发人员节省了大量样板代码。它们还通过自动迭代复杂图表的形状集来简化图表创建。

然而，和往常一样，它们并不是严格需要的。让我们来看一个例子，看看没有它们也能凑合:

```
<svg id="chart-test" width="200px" height="200px"></svg>
```

```
var somePalette = ['blue', 'green', 'red', 'yellow', 'orange'],
    Pie = require('paths/pie'),
    pie = Pie({
      data: [
        { name: 'Italy', population: 59859996 },
        { name: 'Mexico', population: 118395054 },
        { name: 'France', population: 65806000 },
        { name: 'Argentina', population: 40117096 },
        { name: 'Japan', population: 127290000 }
      ],
      accessor: function(x) { return x.population; },
      compute: {
        color: function(i) { return somePalette[i]; }
      },
      center: [50, 50],
      r: 30,
      R: 50
    }),
    chartSvg = document.getElementById("chart-test"),
    chartFragment = document.createDocumentFragment(),
    dx = parseInt(chartSvg.getAttribute('width'), 10) / 2,
    dy = parseInt(chartSvg.getAttribute('height'), 10) / 2;

pie.curves.forEach(function (d, i){
  var path = document.createElementNS('https://www.w3.org/2000/svg',"path");
  path.setAttributeNS(null, 'd', d.sector.path.print());
  path.setAttributeNS(null, 'style', 'fill:' + d.color);
  var label = document.createElementNS('https://www.w3.org/2000/svg',"text");
  label.textContent = d.item.name;
  label.setAttributeNS(null, 'x', d.sector.centroid[0]);
  label.setAttributeNS(null, 'y', d.sector.centroid[1]);

  chartFragment.appendChild(path);
  chartFragment.appendChild(label);
});

chartSvg.appendChild(chartFragment);
```

在上面的代码中，我们使用一个代码片段来收集所有的扇区，然后将它们添加到页面中——因此只触发一次回流，而不是对每个扇区触发两次(一次用于路径，一次用于标签)。文档片段元素是一次性插入的，而如果我们使用一个`svg:g`元素对它们进行分组，那么每个节点都将被单独插入。(此外，在生成的 SVG 中可能存在冗余组。)文档片段的一个更大的优点是，如果我们需要克隆整个图表并多次将其添加到页面中，则每次克隆操作都需要固定数量的节点插入，而不是线性数量的节点插入。

现在，让我们将前面的代码与上面使用 Ractive 创建的相同图形进行比较:

```
<div id="pie-chart"></div><script id="myChartTemplate" type="text/ractive">
  <svg width=375 height=400>
      {{# pie }}
        {{# curves:num }}
            <path on-click="expand" d="{{ sector.path.print() }}" fill="{{ color }}" ></path>
            <text text-anchor="middle" x="d.sector.centroid[0]" y="d.sector.centroid[1]">{{ item.name }}</text>
          </g>
        {{/ curves }}
      {{/ end of pie}}
  </svg>
</script>
```

```
var Pie = require('paths/pie');
  var ractive = new Ractive({
        el: 'pie-chart',
        template: '#myChartTemplate',
        data: {
          pie: Pie({
                    data: [
                      { name: 'Italy', population: 59859996 },
                      { name: 'Mexico', population: 118395054 },
                      { name: 'France', population: 65806000 },
                      { name: 'Argentina', population: 40117096 },
                      { name: 'Japan', population: 127290000 }
                    ],
                    accessor: function(x) { return x.population; },
                    compute: {
                      color: function(i) { return somePalette[i]; }
                    },
                    center: [50, 50],
                    r: 30,
                    R: 50
                  })
        }
      });
```

结果看起来更好、更干净，并且通过查看标记，图表的结构立即变得明显。

目前有 9 种不同类型的图表可用:

*   [饼状图](https://github.com/andreaferretti/paths-js#pie-graph)
*   [条形图](https://github.com/andreaferretti/paths-js#bar-graph):允许并排绘制多个直方图。
*   [股价图](https://github.com/andreaferretti/paths-js#stock-graph):用折线图表示一个或多个时间序列。
*   [平滑折线图](https://github.com/andreaferretti/paths-js#smooth-line-graph):类似于股票图，但它使用平滑贝塞尔曲线在数据点之间插入线条。
*   [雷达图](https://github.com/andreaferretti/paths-js#radar-graph)
*   [树形图](https://github.com/andreaferretti/paths-js#tree-graph)
*   [瀑布图](https://github.com/andreaferretti/paths-js#waterfall-graph):一个条形图，允许将数值分成几部分。
*   [力定向图](https://github.com/andreaferretti/paths-js#force-directed-graphs):图形形式的物理模拟，顶点相互排斥，除非它们由一条边连接。
*   [桑基图](https://github.com/andreaferretti/paths-js#sankey-diagram):流程图，箭头与流量成正比。

你可以看看 Paths[showcase](http://andreaferretti . github . io/Paths-js-demo/)看看这些图表是什么样子的。这里展示的所有例子都利用了 Ractive 来轻松创建令人惊叹的动画。

## 结论

在这一点上，你可能会问 Paths 是否真的是你的正确选择。当然，对此没有简单的答案。看情况。如果你需要现成的小工具和图表，可能不需要——你最好用 [Highcharts](http://www.highcharts.com/) 、 [Flotcharts](http://www.flotcharts.org/) 或 [Dimple](http://dimplejs.org/) 。

然而，我们怎么强调在数据可视化中采用声明式编程风格的优势都不为过。如果您强制使用路径，结果可能不值得学习一个新的库。

如果您需要创建带有个性化样式或动画的自定义图表，或者创建响应用户交互的自定义行为，Paths 会很有帮助。但是 Paths 是一个真正的游戏改变者，您需要在服务器上生成图形。Paths 使得在逻辑中生成标记并将其作为 JSON 或字符串发送给客户机变得非常容易。

最后，这里有一些供进一步阅读的链接:

*   本文中的[代码示例](http://codepen.io/mlarocca/pen/iDegu)的代码笔。
*   我关于浏览器数据可视化的幻灯片:[Reactive . js 的反应式 SVG 图表](http://mlarocca.github.io/graphicalweb2014)。
*   [GitHub 上的 Paths-js home](https://github.com/andreaferretti/paths-js) 。
*   安德雷亚·费雷蒂关于浏览器可视化功能方法的讲座幻灯片。

* * *

1.  Paths.js 只是依赖核心 [EcmaScript 5](http://kangax.github.io/compat-table/es5/) 方法，这是老浏览器的问题。[这种多填充物](https://github.com/kriskowal/es5-shim/)增加了必要的支撑。 [↩](#fnref:1 "Return to article")

## 分享这篇文章