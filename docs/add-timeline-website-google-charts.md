# 用谷歌图表给你的网站添加一条时间线

> 原文：<https://www.sitepoint.com/add-timeline-website-google-charts/>

为了吸引读者的注意力，以图表而不是表格的形式显示数据集是一个好主意。如果你在博客上谈论很多事件，在时间线上按时间顺序显示这些事件会以更有效和有趣的方式传达信息。

在本教程中，我将教你如何使用谷歌图表添加专业的时间线到你的网页。

## 加载谷歌图表

因为我将使用 Google API Loader 来加载 Google 图表，所以我将把下面的`script`标记添加到显示时间线的页面的标题中:

```
<script type="text/javascript" src="https://www.google.com/jsapi"></script>
```

我现在可以使用`google.load`方法加载任何 Google API。对于谷歌图表，我需要加载**可视化 API** 以及 API 的`timeline`包。我还将添加一个回调函数来知道 API 何时准备好。

```
google.load('visualization', '1.0', { packages:["timeline"] });
google.setOnLoadCallback(start);

function start() {
  /* This method will be called when the
     Visualization API has been loaded. */
}
```

## 添加元素以保存图表

图表 API 需要一个 HTML 元素来绘制图表。我将在页面主体中添加一个空的`div`，并将其命名为`timelineHolder`。我可以通过设置 CSS 中的`width`和`height`属性来设置时间轴的尺寸。让我们创建一个尺寸为 600 像素 x 300px 像素的时间轴。

```
.timelineHolder {
  width: 600px;
  height: 300px;
}
```

## 定义时间线数据

在本教程中，让我们在时间轴上描绘一个名为爱丽丝的虚构旅行者的一年生活。这是爱丽丝那年做的事:

*   从 1 月 15 日到 2 月 26 日，她在柏林
*   从 4 月 20 日到 7 月 1 日，她在巴黎
*   从 7 月 21 日到 8 月 30 日，她在马德里
*   从 11 月 2 日到 12 月 5 日，她在珀斯

该信息必须被转换成`DataTable`。我们的`DataTable`将有四列:

*   标签
*   地方
*   开始日期
*   结束日期

创建一个名为`prepareDataTable()`的新函数来创建和初始化`DataTable`。我们必须使用`addColumn`方法来添加四列中的每一列。添加完列后，使用`addRow`方法添加所有四个行程。记住，日期必须表示为`Date`对象，月份编号为 0-11(而不是 1-12)。我们的函数应该是这样的:

```
function prepareDataTable() {
  var dataTable = new google.visualization.DataTable();

  // Add columns
  dataTable.addColumn({ type: 'string', id: 'Label'});
  dataTable.addColumn({ type: 'string', id: 'Place'});
  dataTable.addColumn({ type: 'date', id: 'Arrival Date'});
  dataTable.addColumn({ type: 'date', id: 'Departure Date'});

  //Add Rows
  dataTable.addRow(['1', 'Berlin',
                    new Date(2014, 0, 15), new Date(2014, 1, 26)]);
  dataTable.addRow(['2', 'Paris', 
                    new Date(2014, 3, 20), new Date(2014, 6, 1)]);
  dataTable.addRow(['3', 'Madrid', 
                    new Date(2014, 6, 21), new Date(2014, 7, 30)]);
  dataTable.addRow(['4', 'Perth', 
                    new Date(2014, 10, 2), new Date(2014, 11, 5)]);

  return dataTable;
}
```

注意,`DataTable`的第一列总是被图表 API 视为一行的标签。

## 绘制时间线

既然数据已经被转换成 Google Charts API 能够理解的格式，我们就可以绘制时间线了。为此，我们将创建一个`Timeline`类的实例，然后将`DataTable`传递给它的`draw`方法。为此，我们将更新`start`方法:

```
function start() {
  // Pick the HTML element
  var timelineHolder = document.getElementById("timelineHolder");

  // Create an instance of Timeline
  var timeline = new google.visualization.Timeline(timelineHolder);
  var dataTable = prepareDataTable();

  // Draw the timeline
  timeline.draw(dataTable);
}
```

此时，如果您使用浏览器加载您的页面，您将会看到一个类似如下的时间线:

在[码笔](http://codepen.io)上看到 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的笔[。](http://codepen.io/SitePoint/pen/b0522e3f0762ae90ce00c0f92438e8aa/)