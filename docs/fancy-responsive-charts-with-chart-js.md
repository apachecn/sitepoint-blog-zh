# 使用 Chart.js 制作精美、反应灵敏的图表

> 原文：<https://www.sitepoint.com/fancy-responsive-charts-with-chart-js/>

数据就在我们身边。虽然搜索引擎和其他应用程序使用基于文本的数据表示效果最佳，但人们发现可视化表示的数据更容易理解。今年早些时候，SitePoint 发表了奥雷利奥的文章，介绍了 Chart.js 。本教程将快速回顾该介绍，然后更深入地了解 [Chart.js](http://www.chartjs.org/) 的特性。

## 入门指南

Chart.js 是一个基于 HTML5 canvas 的反应灵敏、灵活的轻量级图表库。该库支持六种不同的图表类型，每种图表类型都带有大量的自定义选项。如果这还不够，您还可以创建自己的自定义图表类型。

Chart.js 中的所有六种核心图表类型都只有 11kb，并且是 gzip 格式的，这个库是模块化的，所以您可以通过只包含您实际需要的图表类型来进一步减少文件的请求大小。下面是包含它的 cdnjs 链接:

```
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/1.0.2/Chart.min.js"></script>
```

## 可用的配置选项

Chart.js 允许您更改图表的几乎所有方面——从工具提示到动画。在这一节中，我将修改一些设置来演示 Chart.js 能够创建什么。下面是我们将要开始的 HTML:

```
<canvas id="canvas"></canvas>
```

在第一次演示中，我将创建一个折线图。为了使图表有意义，您需要设置一些基本选项。折线图需要一个由`labels`和`datasets`组成的数组。标签出现在 X 轴上。我已经用一些虚拟数据填充了折线图。数据被分解成一组数据集。每个数据集的填充、线条和点都有一种颜色。

在这种情况下，我将`fillColor`设置为透明。如果不设置`fillColor`的值，它将被设置为黑色或灰色。其他价值观也是如此。颜色是使用 RGBA、RGB、hex 或 HSL 格式定义的，类似于 CSS。

```
var lineData = {
  labels: ['Data 1', 'Data 2', 'Data 3', 'Data 4', 
           'Data 5', 'Data 6', 'Data 7'],
  datasets: [{
    fillColor: 'rgba(0,0,0,0)',
    strokeColor: 'rgba(220,180,0,1)',
    pointColor: 'rgba(220,180,0,1)',
    data: [20, 30, 80, 20, 40, 10, 60]
  }, {
    fillColor: 'rgba(0,0,0,0)',
    strokeColor: 'rgba(151,187,205,1)',
    pointColor: 'rgba(151,187,205,1)',
    data: [60, 10, 40, 30, 80, 30, 20]
  }]
}
```

### 设置全局选项

我已经包含了设置一些全局值的代码。`animationSteps`决定动画持续时间。还有很多选项可以根据需要修改，比如`scaleLineColor`和`scaleIntegersOnly`。我建议你浏览一下 [Chart.js 文档](http://www.chartjs.org/docs/)，看看这个库还能提供什么。

```
Chart.defaults.global = {
  animationSteps : 50,
  tooltipYPadding : 16,
  tooltipCornerRadius : 0,
  tooltipTitleFontStyle : 'normal',
  tooltipFillColor : 'rgba(0,160,0,0.8)',
  animationEasing : 'easeOutBounce',
  scaleLineColor : 'black',
  scaleFontSize : 16
}
```

### 设置图表特定选项

除了全局选项之外，还有针对单个图表类型的配置选项。我将在此折线图中设置一些选项，让您了解一下:

```
var ctx = document.getElementById('canvas').getContext('2d');
var lineDemo = new Chart(ctx).Line(lineData, {
  responsive: true,
  pointDotRadius: 10,
  bezierCurve: false,
  scaleShowVerticalLines: false,
  scaleGridLineColor: 'black'
});
```

Chart.js 生成的图表默认不响应。将`responsive`设置为 true(如上所述)会使它们做出响应。如果您需要使每个图表都具有响应性，我建议您改为全局设置，如下所示:

```
Chart.defaults.global.responsive = true;
```

下面你可以看到折线图的演示:

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的 Pen [Chart.js 折线图演示](http://codepen.io/SitePoint/pen/mJRrKw/)。