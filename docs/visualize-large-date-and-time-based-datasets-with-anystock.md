# 用 AnyStock 可视化基于日期和时间的大型数据集

> 原文：<https://www.sitepoint.com/visualize-large-date-and-time-based-datasets-with-anystock/>

在我关于 SitePoint 的前一篇文章[中，我介绍了 AnyChart 产品系列的基础知识。这次我将告诉您 AnyStock 如何帮助可视化股票数据以及任何基于日期和时间的大型数据集(以及如何最好地使用它)。](https://www.sitepoint.com/getting-started-anychart-examples/)

AnyStock 是 AnyChart JavaScript 图表库的一部分，作为一组产品销售:

*   [any chart](https://www.anychart.com/products/anychart/overview/)–常用图表类型，
*   [any map](https://www.anychart.com/products/anymap/overview/)–地理地图和座位地图，
*   [任意甘特图](https://www.anychart.com/products/anygantt/overview/)–项目甘特图、资源图和计划评审技术图，
*   [any stock](https://www.anychart.com/products/anystock/overview/)–基于日期/时间的大数据集的可视化，其特性是本文的主题。

因为 AnyChart 本质上是一个大的 JavaScript 图表库，API 是通用的，所有图表都以几乎相同的方式配置，共享[主题](https://docs.anychart.com/latest/Appearance_Settings/Themes)、设置和[加载数据的方式](https://www.sitepoint.com/getting-started-anychart-examples/)。也就是说，您将在本文中学到的大多数东西和方法都可以很容易地应用于制作其他图表和地图。

AnyStock 股票图表是一种特殊类型的图表，可以有效地可视化[大量基于日期/时间的数据](http://www.anychart.com/products/anystock/overview/#big-data)。它有许多特殊的功能，如滚动用户界面缩放、同步绘图、数据分组等。AnyStock 与所有其他 AnyChart 图表完全兼容，除了拥有所有[的基本股票功能，如分组、流式、滚动等。](http://www.anychart.com/products/anystock/overview/)，AnyStock 股票还内置了[多项技术指标，并能够定制](#conclusion)。

简而言之，在本文中，我将:

*   解释 AnyStock 的基本工作原理；
*   展示数据映射之美；
*   展示如何[将数据直接从 Google Finance 加载到 any stock](#google-finance)；
*   提供一个示例，说明数据映射机制如何帮助[在 AnyStock](#xigniteapi) 中使用来自 Xignite API 的数据。

## AnyStock 快速入门

要在 HTML 页面中使用 AnyStock，你需要三样简单的东西。第一个是指向库的 JavaScript 文件的链接。第二个是块级 HTML 元素；这里有一个你可以使用的 HTML 模板示例:

```
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <script src="https://cdn.anychart.com/js/7.14.3/anystock.min.js"></script>    
    <style> html, body, #container {
      width: 100%;
      height: 100%;
    } </style>    
    <title>AnyStock Basic Example</title>    
  </head>
  <body>
    <div id="container"></div>
    <script> // AnyStock code here </script>
  </body>
</html> 
```

下面是创建一个简单的[日本烛台图](http://www.anychart.com/chartopedia/chart-types/japanese-candlestick-chart/)的基本代码，以及第二个图中的体积和以面积显示体积的滚动条:

```
anychart.onDocumentReady(function() {
    // data
    var data = [
      ['2015-12-24', 511.53, 514.98, 505.79, 506.40, 1050016],
      ['2015-12-25', 512.53, 514.88, 505.69, 505.34, 1050015],
      ['2015-12-26', 511.83, 514.98, 505.59, 506.23, 1050016],
      ['2015-12-27', 511.22, 515.30, 505.49, 506.47, 1250016],
      ['2015-12-28', 510.35, 515.72, 505.23, 505.80, 1250015],
      ['2015-12-29', 510.53, 515.86, 505.38, 508.25, 1250018],
      ['2015-12-30', 511.43, 515.98, 505.66, 507.45, 1250017],
      ['2015-12-31', 511.50, 515.33, 505.99, 507.98, 1250017],
      ['2016-01-01', 511.32, 514.29, 505.99, 506.37, 1250016],
      ['2016-01-02', 511.70, 514.87, 506.18, 506.75, 1250016],
      ['2016-01-03', 512.30, 514.78, 505.87, 508.67, 1250018],
      ['2016-01-04', 512.50, 514.77, 505.83, 508.35, 1250018],
      ['2016-01-05', 511.53, 516.18, 505.91, 509.42, 1050019],
      ['2016-01-06', 511.13, 516.01, 506.00, 509.26, 1050019],
      ['2016-01-07', 510.93, 516.07, 506.00, 510.99, 1050110],
      ['2016-01-08', 510.88, 515.93, 505.22, 509.95, 1350019],
      ['2016-01-09', 509.12, 515.97, 505.15, 510.12, 1350110],
      ['2016-01-10', 508.53, 516.13, 505.66, 510.42, 1350110],
      ['2016-01-11', 508.90, 516.24, 505.73, 510.40, 1350110]
    ];

    // create stock chart
    chart = anychart.stock();
    chart.title("AnyStock: Basic Sample");

    // create data table and load data in it
    table = anychart.data.table();
    table.addData(data); 

    // map data - name fields
    mapping = table.mapAs({open: 1, high: 2, low: 3, close: 4, value: 5});

    // create series to show stock changes
    chart.plot(0).candlestick(mapping).name("Stock name");
    // create second plot and show trading volume there as a line
    chart.plot(1).column(mapping).name("Volume");
    // create series to show trading volume
    chart.scroller().area(mapping);

    // set HTML container and draw chart into it
    chart.container('container').draw();     
}); 
```

### 数据配置

在之前关于 AnyChart 的[文章中，我演示了将数据加载到库中的多种方法，但是一个重要且令人惊讶的特性被忽略了。](https://www.sitepoint.com/getting-started-anychart-examples/)

什么是数据映射？让我们深入研究一下，因为我们已经在基本的 AnyStock 示例中很好地利用了这个特性，并将在本文的每个示例中继续使用它。

正如这篇关于数据映射的文章告诉我们的:

> 在计算和数据管理中，数据映射是在两个不同的数据模型之间创建数据元素映射的过程。数据映射是各种数据集成任务的第一步，包括数据源和目标之间的数据转换或数据中介。

把这个定义翻译成外行话的方法之一:**“数据映射是一种规则，它告诉数据中是什么是什么，并准备一些数据在其他地方使用。”**

我知道这听起来可能有点粗糙和过于简单化，但它仍然可能是有帮助的。

现在，让我们看看上面例子中的数据映射是如何发生的。首先，我们将一个包含数字和日期的数组加载到表中:

```
// data
var data = [
  ['2015-12-24', 511.53, 514.98, 505.79, 506.40, 1050016],
  ['2015-12-25', 512.53, 514.88, 505.69, 505.34, 1050015],
  ['2015-12-26', 511.83, 514.98, 505.59, 506.23, 1050016],
  ['2015-12-27', 511.22, 515.30, 505.49, 506.47, 1250016],
  ['2015-12-28', 510.35, 515.72, 505.23, 505.80, 1250015],
  ['2015-12-29', 510.53, 515.86, 505.38, 508.25, 1250018],
  ['2015-12-30', 511.43, 515.98, 505.66, 507.45, 1250017],
  ['2015-12-31', 511.50, 515.33, 505.99, 507.98, 1250017]
];

// create data table and load data in it
table = anychart.data.table();
table.addData(data); 
```

该数据中没有任何内容可以解释哪些列包含价格或其他内容。开盘价、最高价、最低价和收盘价可以按任何顺序排列。那么 AnyStock 库如何理解什么是什么呢？通过使用数据映射！

在这种情况下，我们只需要一个:

```
// map data - name fields
mapping = table.mapAs({open: 1, high: 2, low: 3, close: 4, value: 5}); 
```

正如我们所看到的，`mapAs()`方法接受一个对象，在那里我们决定一个字段取什么名字，然后我们把这个映射传递给显示系列的函数:

```
// create series to show stock changes
chart.plot(0).candlestick(mapping).name("Stock name");
// create second plot and show trading volume there as line
chart.plot(1).column(mapping).name("Volume");
// create series to show trading volume
chart.scroller().area(mapping); 
```

AnyStock 期望使用`open`、`high`、`low`和`close`字段映射[烛台系列](https://docs.anychart.com/Stock_Charts/Series/Japanese_Candlestick)和`value`区域和[列](https://docs.anychart.com/Stock_Charts/Series/Column)，这允许我们在这种情况下使用一个映射。

现在让我们转到下一个示例，我将展示数据映射如何允许我们在更复杂的情况下做更多的事情。我们的数据是 JSON 格式的，我们无法控制它，这通常是当你可以访问某种 API 时的情况。

我们假设数据如下:

```
var data = [
  {'date': '2015-12-24', 'o': 511.53, 'h': 514.98, 'l': 505.79, 'c': 506.40, 'vol': 1050016},
  {'date': '2015-12-25', 'o': 512.53, 'h': 514.88, 'l': 505.69, 'c': 505.34, 'vol': 1050015},
  {'date': '2015-12-26', 'o': 511.83, 'h': 514.98, 'l': 505.59, 'c': 506.23, 'vol': 1050016},
  {'date': '2015-12-27', 'o': 511.22, 'h': 515.30, 'l': 505.49, 'c': 506.47, 'vol': 1250016},
  {'date': '2015-12-28', 'o': 510.35, 'h': 515.72, 'l': 505.23, 'c': 505.80, 'vol': 1250015},
  {'date': '2015-12-29', 'o': 510.53, 'h': 515.86, 'l': 505.38, 'c': 508.25, 'vol': 1250018},
  {'date': '2015-12-30', 'o': 511.43, 'h': 515.98, 'l': 505.66, 'c': 507.45, 'vol': 1250017},
  {'date': '2015-12-31', 'o': 511.50, 'h': 515.33, 'l': 505.99, 'c': 507.98, 'vol': 1250017},
  {'date': '2016-01-01', 'o': 511.32, 'h': 514.29, 'l': 505.99, 'c': 506.37, 'vol': 1250016},
  {'date': '2016-01-02', 'o': 511.70, 'h': 514.87, 'l': 506.18, 'c': 506.75, 'vol': 1250016}]; 
```

对于许多其他解决方案，您将被迫创建一个脚本来更改数据结构、重命名字段、遍历一个数据集并形成一个新的数据集。这不是任何图表的情况。

首先，我们将数据加载到表中，并告诉它参数(时间戳)在哪里:

```
// create data table and load data in it
table = anychart.data.table('date');
table.addData(data); 
```

然后我们创建映射，在本例中，我们将创建四个映射。三个映射将一些字段视为`value`，一个将‘h’和‘l’字段视为`high`和`low`:

```
// map data
mapping_1 = table.mapAs({value: 'o'});
mapping_2 = table.mapAs({value: 'c'});
mapping_3 = table.mapAs({high: 'h', low: 'l'});
mapping_4 = table.mapAs({value: 'vol'}); 
```

现在我们准备将这些映射传递给四个系列构造函数:

```
// create a series to show open prices line
chart.plot(0).line(mapping_1).name("Open");
// create a series to show close prices line
chart.plot(0).line(mapping_2).name("Close");      
// create a second plot and show trading volume there as a line
chart.plot(0).rangeSplineArea(mapping_3).name("Range");
// create a series in a scroller
chart.scroller().stick(mapping_4); 
```

…并在 CodePen 上享受我们的 [AnyStock 高级映射示例:](https://codepen.io/SitePoint/pen/brKZzV)

参见 [CodePen](https://codepen.io) 上 SitePoint ( [@SitePoint](https://codepen.io/SitePoint) )的 Pen [AnyStock:高级贴图样本](https://codepen.io/SitePoint/pen/brKZzV/)。