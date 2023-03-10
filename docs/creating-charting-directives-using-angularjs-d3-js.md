# 使用 AngularJS 和 D3.js 创建图表指令

> 原文：<https://www.sitepoint.com/creating-charting-directives-using-angularjs-d3-js/>

[D3](http://www.d3js.org) 是一个 JavaScript 库，可以用 HTML5 技术可缩放矢量图形(SVG)来创建交互式图表。直接使用 SVG 创建图表可能很痛苦，因为需要记住 SVG 支持的形状，并多次调用 API 以使图表动态化。D3 抽象了大部分问题，并提供了一个简单的接口来构建基于 SVG 的图表。 [Jay Raj](https://www.sitepoint.com/author/jraj/) 发表了两篇关于使用 D3 的不错的 SitePoint 文章，如果你还不熟悉 D3 的话可以看看。

你们大多数人可能不需要正式介绍 [AngularJS](http://www.angularjs.org) 。AngularJS 是一个客户端 JavaScript 框架，用于构建丰富的 web 应用程序。AngularJS 的最大卖点之一是对指令的支持。指令提供了一种定义我们自己的 HTML 属性和元素的好方法。它还有助于保持标记和代码相互分离。

AngularJS 在数据绑定方面也非常强大。这个特性节省了根据模型中的数据更新 UI 所需的大量时间和精力。在现代网络世界中，客户要求开发者建立实时响应的网站。这意味着客户希望总是在屏幕上看到最新的数据。一旦有人在后端修改了一条数据，就必须更新数据 UI。如果我们没有数据绑定的支持，执行这样的实时更新将会非常困难和低效。

在本文中，我们将看到如何构建包装 D3 图表的实时 AngularJS 指令。

## 安装

首先，我们需要设置环境。我们需要在 HTML 页面中包含 AngularJS 和 D3。因为我们将只构建一个图表指令，所以我们需要创建一个 AngularJS 控制器和一个指令。在控制器中，我们需要一个包含要绘制在图表中的数据的集合。下面的代码片段显示了初始控制器和指令。稍后我们将向这些组件添加更多的代码。

```
var app = angular.module("chartApp", []);

app.controller("SalesController", ["$scope", function($scope) {
  $scope.salesData = [
    {hour: 1,sales: 54},
    {hour: 2,sales: 66},
    {hour: 3,sales: 77},
    {hour: 4,sales: 70},
    {hour: 5,sales: 60},
    {hour: 6,sales: 63},
    {hour: 7,sales: 55},
    {hour: 8,sales: 47},
    {hour: 9,sales: 55},
    {hour: 10,sales: 30}
  ];
}]);

app.directive("linearChart", function($window) {
  return{
    restrict: "EA",
    template: "<svg width='850' height='200'></svg>",
    link: function(scope, elem, attrs){
    }
  };
});
```

我们将在上述指令中填充 link 函数，以使用存储在控制器中的数据，并使用 D3 绘制一个折线图。指令的模板包含一个`svg`元素。我们将在这个元素上应用 D3 的 API 来绘制图表。以下代码片段显示了指令的示例用法:

```
<div linear-chart chart-data="salesData"></div>
```

现在，让我们收集绘制图表所需的基本数据。它包括要绘制的数据、SVG 元素的 JavaScript 对象和其他静态数据。

```
var salesDataToPlot=scope[attrs.chartData];
var padding = 20;
var pathClass = "path";
var xScale, yScale, xAxisGen, yAxisGen, lineFun;

var d3 = $window.d3;
var rawSvg = elem.find("svg")[0];
var svg = d3.select(rawSvg);
```

一旦加载了 d3 的库，`d3`对象就可以作为全局变量使用了。但是，如果我们直接在代码块中使用它，就很难测试那个代码块。为了使指令可测试，我通过 *$window* 使用对象。

## 绘制简单的折线图

让我们设置绘制图表所需的参数。图表需要一个 x 轴、一个 y 轴和由这些轴表示的数据域。在本例中，x 轴表示以小时为单位的时间。我们可以取数组中的第一个和最后一个值。在 y 轴上，可能的值是从零到销售额的最大值。使用`d3.max()`可以找到最大销售额。轴的范围根据`svg`元件的高度和宽度而变化。

使用上面的值，我们需要让 d3 用期望的方向和刻度数绘制轴。最后，我们需要使用`d3.svg.line()`来定义一个函数，该函数根据我们上面定义的刻度来画线。所有上述组件都必须附加到指令模板中的`svg`元素。我们可以在追加项目时将样式和转换应用于图表。以下代码设置参数并追加到 SVG 中:

```
function setChartParameters(){
  xScale = d3.scale.linear()
             .domain([salesDataToPlot[0].hour, salesDataToPlot[salesDataToPlot.length - 1].hour])
             .range([padding + 5, rawSvg.clientWidth - padding]);

              yScale = d3.scale.linear()
                .domain([0, d3.max(salesDataToPlot, function (d) {
                  return d.sales;
                })])
             .range([rawSvg.clientHeight - padding, 0]);

  xAxisGen = d3.svg.axis()
               .scale(xScale)
               .orient("bottom")
               .ticks(salesDataToPlot.length - 1);

  yAxisGen = d3.svg.axis()
               .scale(yScale)
               .orient("left")
               .ticks(5);

  lineFun = d3.svg.line()
              .x(function (d) {
                return xScale(d.hour);
              })
              .y(function (d) {
                return yScale(d.sales);
              })
              .interpolate("basis");
}

function drawLineChart() {

  setChartParameters();

  svg.append("svg:g")
     .attr("class", "x axis")
     .attr("transform", "translate(0,180)")
     .call(xAxisGen);

   svg.append("svg:g")
      .attr("class", "y axis")
      .attr("transform", "translate(20,0)")
      .call(yAxisGen);

   svg.append("svg:path")
      .attr({
        d: lineFun(salesDataToPlot),
        "stroke": "blue",
        "stroke-width": 2,
        "fill": "none",
        "class": pathClass
   });
}

drawLineChart();
```

[这里的](http://codepen.io/sravikiran/pen/wFEvH)是展示上图的演示。

## 实时更新图表

如前所述，借助当今的 web 能力，我们的用户希望看到数据图表随着底层数据的变化而立即更新。可以使用 WebSockets 之类的技术将更改后的信息推送到客户端。我们刚刚创建的图表指令应该能够响应这样的更改并更新图表。

为了通过 WebSockets 推送数据，我们需要在服务器上使用 Socket 构建一个组件。IO 用 Node.js，SignalR 用。NET 或其他平台上的类似技术。对于[演示](http://codepen.io/sravikiran/pen/qwgbl)，我使用 AngularJS 的`$interval`服务将十个随机的销售额值以一秒钟的延迟推入销售数组:

```
$interval(function() {
  var hour = $scope.salesData.length + 1;
  var sales = Math.round(Math.random() * 100);

  $scope.salesData.push({hour: hour, sales: sales});
}, 1000, 10);
```

为了在新数据被推送时立即更新图表，我们需要用更新的数据重新绘制图表。必须在指令中使用集合观察器来观察集合数据的变化。当对集合进行任何更改时，将调用观察器。该图表将在观察器中重新绘制。

```
scope.$watchCollection(exp, function(newVal, oldVal) {
  salesDataToPlot = newVal;
  redrawLineChart();
});

function redrawLineChart() {

  setChartParameters();
  svg.selectAll("g.y.axis").call(yAxisGen);
  svg.selectAll("g.x.axis").call(xAxisGen);

  svg.selectAll("." + pathClass)
     .attr({
       d: lineFun(salesDataToPlot)
     });
}
```

完整的演示可以在[这里](http://codepen.io/sravikiran/pen/qwgbl)找到。

## 结论

AngularJS 和 D3 对于在 web 上构建丰富的商业应用程序是非常有用的库。我们讨论了如何一起使用它们来创建一个简单的图表。您可以扩展这些知识，为您的应用程序创建图表。

## 分享这篇文章