# 如何从自定义数据集创建交互式 JavaScript 图表

> 原文：<https://www.sitepoint.com/javascript-charts-custom-data-sets/>

图表是快速有效地可视化复杂数据的好方法。无论你是想确定一种趋势，强调一种关系，还是进行比较，图表都可以帮助你以一种精确而有意义的方式与你的听众交流。

在我的上一篇文章—[AnyChart 入门:10 个实用示例](https://www.sitepoint.com/getting-started-anychart-examples/) —中，我介绍了 any chart 库，并展示了它如何非常适合您的数据可视化需求。今天，我想更深入地了解一下 AnyChart 的数据映射特性，它允许您以最少的麻烦从自定义数据集创建漂亮的图表。

我还想了解定制 AnyChart 以满足您的需求的许多方法，以及如何通过使用主题来改变 AnyChart 图表的外观和感觉。目前有 17 个现成的主题可供选择，或者您可以创建自己的主题。如果你没有最好的设计眼光，为什么不买我们的书来帮你一把。

作为 AnyChart 的 R&D 负责人，我可以花一整天来谈论这个图书馆，但现在是时候进入正题了。

## 任意图表中的数据映射

为了便于将定制数据源集成到图表应用程序中，AnyChart 有一个特殊的对象，叫做 *[数据集](https://api.anychart.com/latest/anychart.data.Set)* 。这些对象充当数据的中间容器。当数据存储在数据集中时，AnyChart 可以跟踪对它的更改，分析它，并以更健壮和有效的方式处理这些数据。简而言之:交互式 JavaScript 图表从未如此简单！

无论您有一个对象数组、一个数组数组还是一个`.csv`文件，您都可以使用数据集来:

*   确保对创建的系列进行完全和明确的控制
*   定义哪一列是参数(x 轴)
*   定义哪些列包含哪些系列的值
*   过滤数据
*   排序数据

### 数据映射的基础

了解数据映射在 AnyChart 中如何工作的最好方法是看一个例子。让我们想象一个具有以下自定义数据集的数组:

```
var rawData = [
  ["A", 5, 4, 5, 8, 1, "bad"],
  ["B", 7, 1, 7, 9, 2, "good"],
  ["C", 9, 3, 5, 4, 3, "normal"],
  ["D", 1, 4, 9, 2, 4, "bad"]
]; 
```

这里没有什么太离谱的事情——这种定制数据结构在许多现有应用程序中很常见。但是现在你想在任何图表中使用这个数组。对于许多其他图表库，您将被迫将数据转换成该库可以处理的格式。嗯，有了 AnyChart，事情就简单多了——看看我们能做些什么。首先，将数组加载到数据集中:

```
var rawData = [
  ["A", 5, 4, 5, 8, 1, "bad"],
  ["B", 7, 1, 7, 9, 2, "good"],
  ["C", 9, 3, 5, 4, 3, "normal"],
  ["D", 1, 4, 9, 2, 4, "bad"]
];

var dataSet = anychart.data.set(rawData); 
```

然后，一旦数据被加载到数据集中，真正的奇迹就开始了:您现在可以创建所谓的[视图](https://api.anychart.com/latest/anychart.data.View)。这些数据集来自其他数据集。

```
var rawData = [
  ["A", 5, 4, 5, 8, 1, "bad"],
  ["B", 7, 1, 7, 9, 2, "good"],
  ["C", 9, 3, 5, 4, 3, "normal"],
  ["D", 1, 4, 9, 2, 4, "bad"]
];

var dataSet = anychart.data.set(rawData);

var view1 = dataSet.mapAs({x: 0, value: 1});
var view2 = dataSet.mapAs({x: 0, value: 2});
var view3 = dataSet.mapAs({x: 0, high: 3, low: 4});
var view4 = dataSet.mapAs({x: 0, value: 5, meta: 6}); 
```

您会注意到，在定义视图时，您决定包含原始数组中的哪些列以及这些列的名称。然后，您可以使用它们来创建您喜欢的任何类型的图表。例如，下面是如何从第 5 列中的自定义数据创建饼图。

**注意:** AnyChart 只需要`x`和`value`字段来创建一个饼图，但是视图还包含一个`meta`字段，其中包含第 6 列的数据。您可以映射任意数量的可选字段，并随意使用它们。例如，这些字段可以包含显示为标签或工具提示的附加数据:

```
anychart.onDocumentLoad(function() {
  var rawData = [
    ["A", 5, 4, 5, 8, 3, "Bad"],
    ["B", 7, 1, 7, 9, 5, "Good"],
    ["C", 9, 3, 5, 4, 4, "Normal"],
    ["D", 1, 4, 9, 2, 3, "Bad"]
  ];

  var dataSet = anychart.data.set(rawData);
  var view4 = dataSet.mapAs({x: 0, value: 5, meta: 6});

  // create chart
  var chart = anychart.pie(view4);
  chart.title("AnyChart: Pie Chart from Custom Data Set");
  chart.labels().format("{%meta}: {%Value}");
  chart.container("container").draw();
}); 
```

这就是我们最终得到的结果:

在 [CodePen](https://codepen.io) 上通过 SitePoint ( [@SitePoint](https://codepen.io/SitePoint) )从数据集中看到 Pen [AnyChart 饼状图。](https://codepen.io/SitePoint/pen/JJWVzm/)