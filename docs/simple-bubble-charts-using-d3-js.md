# 使用 D3.js 的简单气泡图

> 原文：<https://www.sitepoint.com/simple-bubble-charts-using-d3-js/>

在我的工作场所，我被分配了可视化一些数据的任务。就在那时，我遇到了 [D3.js](http://d3js.org/) ，这是一个 JavaScript 库，用于以交互方式基于数据操作文档。它利用了 HTML5、JavaScript、SVG 和 CSS3。在本教程中，我们将使用 D3 以 abubble 图表的形式可视化我们的数据。

*开始之前，下载 [D3](http://d3js.org/) 源码。*

## 创建 X 和 Y 轴

让我们开始用 D3 画一些轴。首先，我们的 HTML 页面中需要 SVG。SVG 是一种基于 XML 的矢量图像格式，支持交互和动画。下面的代码示例显示了我们的 HTML 页面应该是什么样子。注意主体中对 D3 和`svg`标签的引用。

```
<html>
  <head>
    <script src="jquery.js"></script>
    <script src="d3.v3.js"></script>
    <script>
      $(function() {
        InitChart();
      });

      function InitChart() {
        // Chart creation code goes here
      }
    </script>
  </head>
  <body>
    <svg id="svgVisualize" width="500" height="500"></svg>
  </body>
</html>
```

D3.js 有一组[API](https://github.com/mbostock/d3/wiki/API-Reference)，我们将用它们来绘制我们的坐标轴。我们将使用的一个 API 是 [`d3.scale.linear()`](https://github.com/mbostock/d3/wiki/Quantitative-Scales#wiki-linear) ，它用于创建一个量化尺度。使用这个 API，我们可以定义每个轴的范围和域。域定义了图表上显示的最小值和最大值，而范围是我们将要覆盖的 SVG 的数量。我们的 svg 是 500×500，因此，我们将范围定义为 40×400。

```
var xRange = d3.scale.linear().range([40, 400]).domain([0,100]);
var yRange = d3.scale.linear().range([40, 400]).domain([0,100]);
```

接下来，我们需要扩展到两个轴。为此我们将使用 [`axis.scale()`](https://github.com/mbostock/d3/wiki/SVG-Axes#wiki-scale) :

```
var xAxis = d3.svg.axis().scale(xRange);
var yAxis = d3.svg.axis().scale(yRange);
```

接下来，通过 JavaScript 将 x 轴和 y 轴附加到 SVG 元素:

```
vis.append("svg:g").call(xAxis);
vis.append("svg:g").call(yAxis);
```

此时，我们的`InitChart()`函数如下所示:

```
function InitChart() {
  var vis = d3.select("#svgVisualize");
  var xRange = d3.scale.linear().range([40, 400]).domain([0,100]);
  var yRange = d3.scale.linear().range([40, 400]).domain([0,100]);
  var xAxis = d3.svg.axis().scale(xRange);
  var yAxis = d3.svg.axis().scale(yRange);
  vis.append("svg:g").call(xAxis);
  vis.append("svg:g").call(yAxis);
}
```

您可以查看到目前为止代码的演示。您应该会看到一条粗黑线。实际上，有两条线相互重叠。要分离轴，请修改我们附加 y 轴的代码，如下所示:

```
vis.append("svg:g").call(yAxis).attr("transform", "translate(0,40)");
```

更新后的代码可在[这里](https://jsfiddle.net/jady/YrF5L/2/)获得。现在，您可以看到两个轴，因为我们已经将 y 轴移动了 40 个单位。

接下来，我们需要做两件事:1。)将 y 轴从 x 轴移动 40 个单位，从 y 轴移动 0 个单位和 2。)将其方向更改为向左。更新后的`InitChart()`如下所示，更新后的演示可在[这里](https://jsfiddle.net/jady/YrF5L/3/)获得。

```
function InitChart() {
  var vis = d3.select("#svgVisualize");
  var xRange = d3.scale.linear().range([40, 400]).domain([0,100]);
  var yRange = d3.scale.linear().range([40, 400]).domain([0,100]);
  var xAxis = d3.svg.axis().scale(xRange);
  var yAxis = d3.svg.axis().scale(yRange).orient("left");
  vis.append("svg:g").call(xAxis);
  vis.append("svg:g").call(yAxis).attr("transform", "translate(40,0)");
}
```

现在，我们的 y 轴看起来不错，但 x 轴需要下移。让我们用`transform`把它拉下来:

```
vis.append("svg:g").call(xAxis).attr("transform", "translate(0,400)");
```

现在，如果我们看一下图表，我们会看到 y 轴刻度从 100 到 0。我们需要像这样反过来:

```
var yRange = d3.scale.linear().range([400, 40]).domain([0,100]);
```

修改后的`InitChart()`函数如下所示:

```
function InitChart() {
  var vis = d3.select("#svgVisualize");
  var xRange = d3.scale.linear().range([40, 400]).domain([0,100]);
  var yRange = d3.scale.linear().range([400, 40]).domain([0,100]);
  var xAxis = d3.svg.axis().scale(xRange);
  var yAxis = d3.svg.axis().scale(yRange).orient("left");
  vis.append("svg:g").call(xAxis).attr("transform", "translate(0,400)");
  vis.append("svg:g").call(yAxis).attr("transform", "translate(40,0)");
}
```

而[这里的](https://jsfiddle.net/jady/YrF5L/4/)是更新后的 demo。

## 泡泡图

现在轴已经设置好了，是时候创建气泡图了。我们首先需要一些数据:

```
var sampleData = [{
  "x": 1,
  "y": 5
}, {
  "x": 20,
  "y": 20
}, {
  "x": 40,
  "y": 10
}, {
  "x": 60,
  "y": 40
}, {
  "x": 80,
  "y": 5
}, {
  "x": 100,
  "y": 60
}];
```

早些时候，我们从 0 到 100 为每个轴硬编码了我们的域。既然有了数据，就可以动态设置域了。D3 有`min()`和`max()`的功能，让我们的生活更轻松。简单地修改`xRange`和`yRange`变量，如下所示。

```
var xRange = d3.scale.linear()
                .range([40, 400])
                .domain([d3.min(sampleData, function(d) {
                  return (d.x);
                }), d3.max(sampleData, function(d) {
                  return d.x;
                })]);
var yRange = d3.scale.linear()
                .range([400, 40])
                .domain([d3.min(sampleData, function(d) {
                  return d.y;
                }), d3.max(sampleData, function(d) {
                  return d.y;
                })]);
```

### 创建圆

现在，我们需要根据来自`sampleData`的 x 和 y 值绘制圆。首先，我们需要将`sampleData`绑定到圆圈:

```
var circles = vis.selectAll("circle").data(sampleData);

circles.enter();
```

上面的代码从文档中选择`circle`，并返回一个占位符。现在，我们需要将`xRange`和`yRange`应用于坐标，将它们转换到绘图空间:

```
var circles = vis.selectAll("circle").data(sampleData);

circles
    .enter()
    .insert("circle")
    .attr("cx", function(d) { return xRange (d.x); })
    .attr("cy", function(d) { return yRange (d.y); })
    .attr("r", 10)
    .style("fill", "red");
```

## 结论

D3.js 是一个非常棒的数据可视化库。在本教程中，我们着重于创建气泡图。我们的图表相当简单，但是可视化可以通过使用转换变得更具交互性，我们将在以后的教程中讨论和实现。本文成品的演示可在[此处](https://jsfiddle.net/jady/Pvj2u/)获得。

## 分享这篇文章