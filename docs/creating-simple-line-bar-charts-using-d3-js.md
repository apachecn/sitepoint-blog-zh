# 使用 D3.js 创建简单的折线图和条形图

> 原文：<https://www.sitepoint.com/creating-simple-line-bar-charts-using-d3-js/>

在[之前的文章](https://www.sitepoint.com/simple-bubble-charts-using-d3-js/)中，我们学习了如何使用 [D3.js](http://d3js.org/) 实现气泡图，这是一个用于创建数据驱动文档的 JavaScript 库。D3.js 有助于使用 HTML、SVG 和 CSS 来可视化数据。在本文中，我们将看到如何使用 D3.js 实现折线图和条形图。在继续之前，您应该下载 [D3.js](http://d3js.org/) 并熟悉我的[上一篇文章](https://www.sitepoint.com/simple-bubble-charts-using-d3-js/)中的材料。

## 创建折线图

首先，我们需要一些数据来绘图。我们将使用以下数据。

```
var lineData = [{
  x: 1,
  y: 5
}, {
  x: 20,
  y: 20
}, {
  x: 40,
  y: 10
}, {
  x: 60,
  y: 40
}, {
  x: 80,
  y: 5
}, {
  x: 100,
  y: 60
}];
```

我们还需要一个`<svg>`元素来绘制我们的图表。

```
<svg id="visualisation" width="1000" height="500"></svg>
```

接下来，我们需要创建 x 和 y 轴，为此我们需要声明一个域和范围。域定义了图表上显示的最小值和最大值，而范围是我们将要覆盖的 SVG 的数量。两个轴都需要根据`lineData`中的数据进行缩放，这意味着我们必须相应地设置域和范围。绘制轴的代码如下所示。

```
var vis = d3.select('#visualisation'),
    WIDTH = 1000,
    HEIGHT = 500,
    MARGINS = {
      top: 20,
      right: 20,
      bottom: 20,
      left: 50
    },
    xRange = d3.scale.linear().range([MARGINS.left, WIDTH - MARGINS.right]).domain([d3.min(lineData, function(d) {
      return d.x;
    }), d3.max(lineData, function(d) {
      return d.x;
    })]),
    yRange = d3.scale.linear().range([HEIGHT - MARGINS.top, MARGINS.bottom]).domain([d3.min(lineData, function(d) {
      return d.y;
    }), d3.max(lineData, function(d) {
      return d.y;
    })]),
    xAxis = d3.svg.axis()
      .scale(xRange)
      .tickSize(5)
      .tickSubdivide(true),
    yAxis = d3.svg.axis()
      .scale(yRange)
      .tickSize(5)
      .orient('left')
      .tickSubdivide(true);

vis.append('svg:g')
  .attr('class', 'x axis')
  .attr('transform', 'translate(0,' + (HEIGHT - MARGINS.bottom) + ')')
  .call(xAxis);

vis.append('svg:g')
  .attr('class', 'y axis')
  .attr('transform', 'translate(' + (MARGINS.left) + ',0)')
  .call(yAxis);
```

在这段代码中，我们为图形定义了`WIDTH`、`HEIGHT`和`MARGINS`。`xRange`和`yRange`变量代表各自轴的域。我们根据左边距和右边距设置轴的范围。

接下来，由于域是我们将在图上显示的数据，我们需要从`lineData`中获取最小值和最大值。这是使用`d3.max()`和`d3.min()`方法完成的。

接下来，我们根据`xRange`和`yRange`变量创建我们的轴。对于两个轴，我们分别将 X 轴和 Y 轴的比例定义为`xRange`和`yRange`。然后我们简单地将两个轴附加到 SVG 上，并应用变换。现在，如果我们看一下 Y 轴，它需要向左。因此，我们对`yAxis`应用了向左的方向。我们已经变换了两个轴，保留了定义的边距，这样轴就不会接触到 SVG 的边距。

这里有一个上面代码的[演示](http://codepen.io/jay3dec/pen/fIbkd)，展示了两个轴。

接下来，我们需要将`xRange`和`yRange`应用于坐标，将它们转换到绘图空间，并在绘图空间上画一条线。我们将使用`d3.svg.line()`来绘制我们的线图。为此，我们需要创建一个线生成器函数，它从我们的数据中返回 x 和 y 坐标来绘制线。这就是我们如何定义线生成器函数:

```
var lineFunc = d3.svg.line()
  .x(function(d) {
    return xRange(d.x);
  })
  .y(function(d) {
    return yRange(d.y);
  })
  .interpolate('linear');
```

`interpolate('linear')`调用告诉 D3 画直线。

接下来，我们需要将 SVG 路径的`d`属性设置为 line 函数返回的坐标。这是使用下面的代码完成的。

```
vis.append('svg:path')
  .attr('d', lineFunc(lineData))
  .attr('stroke', 'blue')
  .attr('stroke-width', 2)
  .attr('fill', 'none');
```

我们已经使用`stroke`设置了线条颜色。使用`stroke-width`定义线条的宽度。我们将`fill`设置为`none`，以避免填充图形边界。这是使用`linear`插值的线图的[演示](http://codepen.io/jay3dec/pen/glLxK/)，这是使用`basis`插值的相同曲线图[演示](http://codepen.io/jay3dec/pen/wKLBx/)。

## 创建条形图

接下来，我们将看看如何创建条形图。因为，我们已经创建了我们的轴，我们不需要重新发明轮子。然而，我们将修改现有的代码一点。首先，创建图表坐标轴的示例数据和代码:

```
function InitChart() {

  var barData = [{
    'x': 1,
    'y': 5
  }, {
    'x': 20,
    'y': 20
  }, {
    'x': 40,
    'y': 10
  }, {
    'x': 60,
    'y': 40
  }, {
    'x': 80,
    'y': 5
  }, {
    'x': 100,
    'y': 60
  }];

  var vis = d3.select('#visualisation'),
    WIDTH = 1000,
    HEIGHT = 500,
    MARGINS = {
      top: 20,
      right: 20,
      bottom: 20,
      left: 50
    },
    xRange = d3.scale.linear().range([MARGINS.left, WIDTH - MARGINS.right]).domain([d3.min(barData, function(d) {
        return d.x;
      }),
      d3.max(barData, function (d) {
        return d.x;
      })
    ]),

    yRange = d3.scale.linear().range([HEIGHT - MARGINS.top, MARGINS.bottom]).domain([d3.min(barData, function(d) {
        return d.y;
      }),
      d3.max(barData, function (d) {
        return d.y;
      })
    ]),

    xAxis = d3.svg.axis()
      .scale(xRange)
      .tickSize(5)
      .tickSubdivide(true),

    yAxis = d3.svg.axis()
      .scale(yRange)
      .tickSize(5)
      .orient("left")
      .tickSubdivide(true);

  vis.append('svg:g')
    .attr('class', 'x axis')
    .attr('transform', 'translate(0,' + (HEIGHT - MARGINS.bottom) + ')')
    .call(xAxis);

  vis.append('svg:g')
    .attr('class', 'y axis')
    .attr('transform', 'translate(' + (MARGINS.left) + ',0)')
    .call(yAxis);
}

InitChart();
```

[这里是之前代码的演示](http://codepen.io/jay3dec/pen/BdkIr)。如果你看一下 Y 轴，刻度从 5 开始。这个最小值来自我们的样本数据，其中 5 是最小值。因此，我们需要从 0 开始缩放 Y 轴。为此，我们需要修改`InitChart()`函数中`yRange`的域，如下所示:

```
yRange = d3.scale.linear().range([HEIGHT - MARGINS.top, MARGINS.bottom]).domain([0,
  d3.max(barData, function(d) {
    return d.y;
  })]);
```

在条形图的情况下，我们将使用`ordinal`标度，而不是`linear`标度。序数标度有助于维持一个离散的定义域。有关更详细的信息，请参考[关于序数音阶的官方文件](https://github.com/mbostock/d3/wiki/Ordinal-Scales)。

我们还将使用`rangeRoundBands`来划分图表条的宽度。我们将使用`ordinal`刻度和`rangeRoundBands`来修改`xRange`，如下所示。请注意，我们还将条形之间的间距设置为 0.1。

```
xRange = d3.scale.ordinal().rangeRoundBands([MARGINS.left, WIDTH - MARGINS.right], 0.1).domain(barData.map(function(d) {
  return d.x;
}));
```

接下来，我们需要为图表数据创建矩形条。我们将把样本数据绑定到矩形，使用 x 和 y 坐标来设置矩形条的`height`和`width`。下面是代码的样子:

```
vis.selectAll('rect')
  .data(barData)
  .enter()
  .append('rect')
  .attr('x', function(d) { // sets the x position of the bar
    return xRange(d.x);
  })
  .attr('y', function(d) { // sets the y position of the bar
    return yRange(d.y);
  })
  .attr('width', xRange.rangeBand()) // sets the width of bar
  .attr('height', function(d) {      // sets the height of bar
    return ((HEIGHT - MARGINS.bottom) - yRange(d.y));
  })
  .attr('fill', 'grey');   // fills the bar with grey color
```

这是我们的条形图的演示。

## 添加事件

为了提高交互性，我们还可以将事件附加到条上。我们可以附加一个事件来突出显示`mouseover`上的栏。这是如何实现的:

```
vis.selectAll('rect')
  .data(barData)
  .enter()
  .append('rect')
  .attr('x', function(d) {
    return xRange(d.x);
  })
  .attr('y', function(d) {
    return yRange(d.y);
  })
  .attr('width', xRange.rangeBand())
  .attr('height', function(d) {
    return ((HEIGHT - MARGINS.bottom) - yRange(d.y));
  })
  .attr('fill', 'grey')
  .on('mouseover', function(d) {
    d3.select(this)
      .attr('fill', 'blue');
  });
```

在这段代码中，`on('mouseover')`添加了一个在鼠标经过时调用的事件处理程序，这使得悬停的条变成蓝色。这里有一个[演示](http://codepen.io/jay3dec/pen/CnAps/)来说明这个效果。

您可能会注意到在`mouseout`条不再变灰。让我们附加另一个事件，使它在鼠标离开时恢复到原来的颜色。更新后的代码如下所示:

```
vis.selectAll('rect')
  .data(barData)
  .enter()
  .append('rect')
  .attr('x', function(d) {
    return xRange(d.x);
  })
  .attr('y', function(d) {
    return yRange(d.y);
  })
  .attr('width', xRange.rangeBand())
  .attr('height', function(d) {
    return ((HEIGHT - MARGINS.bottom) - yRange(d.y));
  })
  .attr('fill', 'grey')
  .on('mouseover', function(d) {
    d3.select(this)
      .attr('fill', 'blue');
  })
  .on('mouseout', function(d) {
    d3.select(this)
      .attr('fill', 'grey');
  });
```

这里是上面代码的一个[演示](http://codepen.io/jay3dec/pen/uJrsb/)。

## 结论

D3.js 是一个非常棒的用于数据可视化的 JavaScript 库。在本教程中，我们着重于创建相当简单的条形图和折线图。如果您对更多的实验感兴趣，可以尝试将 D3 库中的其他可视化技术添加到本文的图表中。

## 分享这篇文章