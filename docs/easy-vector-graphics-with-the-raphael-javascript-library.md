# raphal JavaScript 库提供简单的矢量图形

> 原文：<https://www.sitepoint.com/easy-vector-graphics-with-the-raphael-javascript-library/>

Raphaë是一个小型的 JavaScript 库，由来自 [Atlassian](http://www.atlassian.com/?s_kwcid=atlassianawsearch) 的 Dmitry Baranovskiy 编写，它允许你在网页中创建和操作矢量图形。它使用起来非常简单，并且是跨浏览器兼容的；支持 ie 6.0+，Safari 3.0+，Firefox 3.0+，Opera 9.5+。在内部，拉斐尔在 IE 中使用 VML，在其他浏览器中使用 SVG。

现在，涉及圆形和正方形的演示很好，但我想创建一个示例，演示矢量图形的合法、实际的使用。那么实时统计测量怎么样呢？下面是我当前链轮使用情况线图的截图，它描绘了实时“链轮”使用水平。最重要的是，这很容易做到。

![](img/335c7ed5ef63ce343750a80c4abb8447.png)

HTML 很简单；我们只需要一个标题和容器来容纳我们的画布——一个`div`元素:

```
<h1>Current Sprocket Usage: <span id="readout"></span></h1>
<div id="graph"></div>
```

首先，我们必须生成一个新的图形画布。我总是喜欢将我的所有代码放在一个对象定义中，以便创建一个单独的名称空间，所以我们将从下面的代码开始:

```
var SpGraph = {
  init : function(){
    SpGraph.graph = Raphael("graph", 400, 200);
    SpGraph.graph.rect(0, 0, 390, 110, 10).attr("fill", "#000");
  }
}

window.onload = function () {
  SpGraph.init();
};
```

使用 window.onload 事件，我们调用我们的`SpGraph.init`方法。在这个方法中，我们使用`Raphael("graph", 400, 200)`创建画布。第一个参数是容器元素的 ID，另外两个代表宽度和高度。我们将返回的画布对象存储在我们的`SpGraph.graph`属性中。在下一行中，我们创建了一个矩形并设置了一些属性:

```
SpGraph.graph.rect(0, 0, 390, 110, 10).attr("fill", "#000");
```

`rect`方法允许我们绘制一个矩形，指定 x 坐标、y 坐标、宽度、高度和可选的圆角半径。注意，我们还链接了一个对`attr`方法的调用来设置填充颜色。所有拉斐尔图形对象都支持`attr`方法，您可以设置一系列属性。Raphaë支持链接它的所有方法，我们很快就会利用这一点。到目前为止，我们的努力已经产生了这个可爱的圆角黑色矩形。

![](img/535cbc0eb42c11ef3647ab2bf01e8655.png)

现在让我们添加条纹！为此，我们将以下循环添加到`SpGraph.init`方法中:

```
for(var x = 10; x < 110; x += 10) {
  var c = (x > 10) ? "#333" : "#f00";
  SpGraph.graph.path({stroke: c}).moveTo(0, x).lineTo(390,x);
}
```

循环执行 10 次，每次画一条线；第一个是红线，其他的是灰线。raphal`path`方法初始化绘图的路径模式，返回一个`path`对象。它本身并不画任何东西；你必须使用`path`对象方法，这是可链接的。`moveTo`方法将绘图光标移动到指定的 x 和 y 坐标，`lineTo`方法从光标点到指定点绘制一条直线。结果是下面的条纹背景:

![](img/20f47d3675c8d8ab432be41d40babca4.png)

所以现在我们必须画出实际的图形线。垂直轴(由条纹表示)是百分比使用水平。横轴将以 10 个像素为增量表示时间。在现实世界中，图表的每次更新都是通过 Ajax 调用获得的，比如说每 5 秒一次，但是这里我只是创建随机值，每秒更新一次图表。再一次，我们使用路径方法画一条 5 像素宽的线。

我们初始化路径，并将路径的引用存储在`SpGraph.path`属性中，如下所示:

```
SpGraph.path = SpGraph.graph.path({
    stroke: "#0f0",
    "stroke-width": 5, 
    "fill-opacity": 0
}).moveTo(20, 110);
```

每次更新时，我们使用`lineTo`方法扩展该行，如下所示:

```
SpGraph.path.lineTo(20+SpGraph.updates*10, 110-perf);
```

```
perf is a random value between 0 and 100\. The SpGraph.updates property is a simple counter that allows us to control how many updates before the line is reset. The counter value is also used to plot the location of the line on the horizontal axis. After 35 updates the line is reset by removing it, using the SpGraph.path.remove method, and starting a new one.
所以整个脚本看起来是这样的:

```

```
var SpGraph = {
  init : function(){
    SpGraph.graph = Raphael("graph", 400, 200);
    SpGraph.graph.rect(0, 0, 390, 110, 10).attr("fill", "#000");

    for(var x = 10; x < 110; x += 10) {
      var c = (x > 10) ? "#333" : "#f00";
      SpGraph.graph.path({stroke: c}).moveTo(0, x).lineTo(390,x);
    }
    SpGraph.startPath();
    SpGraph.updateGraph();
  },
  startPath : function() {
    if(SpGraph.path) {
      SpGraph.path.remove();
    }
    SpGraph.path = SpGraph.graph.path({
        stroke: "#0f0",
        "stroke-width": 5, 
        "fill-opacity": 0
    }).moveTo(20, 110);
  },
  updateGraph : function() {
    if(SpGraph.updates++ < 36) {
      // imagine this value comes from an ajax request
      var perf = Math.floor(Math.random() * 100);
      SpGraph.path.lineTo(20+SpGraph.updates*10, 110-perf);
      document.getElementById('readout').innerHTML = perf+'%';
    } else {
      SpGraph.updates = 0;
      SpGraph.startPath();
    }
    SpGraph.timer = setTimeout("SpGraph.updateGraph();",1000);
  },
  updates : 0
}
window.onload = function () {
  SpGraph.init();
};
```

别忘了在演示中看看它是如何工作的。好吧，也许链轮使用图并不完全是我承诺的合法、实用的例子，但至少你看到了你只需一点点努力就可以用 Raphaë实现的东西。网站上的文档并不完整，但无论如何也不难解决。你为什么不自己去试试呢？快速，简单，跨浏览器兼容，网络上的矢量图形从未如此简单。

## 分享这篇文章