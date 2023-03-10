# 使用 Chart.js 创建漂亮的图表

> 原文：<https://www.sitepoint.com/creating-beautiful-charts-chart-js/>

<q>内容为王</q>。我们都听过或读过这句话，它表达了好的文本是如何因为对搜索引擎的影响而推动转化的。然而，虽然文本对于搜索引擎来说是极好的，但有时人们可以通过查看图像或图表来更好地理解概念。例如，如果你的网站是关于调查的，关于调查的描述是很重要的，但是一个总结调查数据的图表对你的用户来说更有用。

在本文中，我将向您介绍一个名为 Chart.js 的 JavaScript 库，它使用 HTML5 `canvas`元素在页面上绘制图表。在简单介绍了这个库之后，我还将展示两个使用这个库的例子。

## 什么是 Chart.js？

[Chart.js](http://www.chartjs.org/) 是一个 JavaScript 库，允许你使用 HTML5 `canvas`元素绘制不同类型的图表。因为它使用了`canvas`，你必须包含一个 polyfill 来支持旧的浏览器。作者推荐的是 [ExplorerCanvas](https://code.google.com/p/explorercanvas/) ，所以你可能想坚持下去。

这个库没有依赖关系，而且它的重量很轻，当被缩小、连接和压缩后，它的大小只有 11kb。然而，如果您不使用所有六种核心图表类型，您可以通过仅包含您需要的模块来进一步减小大小。所以，让我们说，你只需要在你的网站中画一个条形图，你就可以包括核心和酒吧模块，为你的用户节省带宽。

Chart.js 的另一个很酷的特性是图表是响应性的，所以它们会根据可用空间进行调整。最后，与你能在网上找到的许多库不同，它提供了[广泛而清晰的文档](http://www.chartjs.org/docs)，使得使用它的基本特性和高级选项变得非常容易。

在下一节中，我们将看到如何获得它并将其包含在您的网站中。

## Chart.js 入门

使用该库的第一步是下载并将其包含在您计划绘制一个或多个图表的页面中。下载 Chart.js 有两种可能。第一种是通过访问[的 GitHub 库](https://github.com/nnnick/Chart.js)来下载这个库。第二种可能是使用 Bower，web 的依赖管理器，通过运行以下命令:

```
bower install chartjs --save
```

一旦下载完成，你必须在你的页面中包含 JavaScript 文件。在下一个示例中，我将假设您想要包含全包版本:

```
<script src="path/to/Chart.js"></script>
```

请注意文件的大写字母。库的第一个字母大写是不常见的，所以当你第一次使用它时，你可能会很难理解为什么会出现 404 错误。

一旦完成，库将通过一个名为`Chart`的全局变量提供它的所有方法。有了 JavaScript 文件，我们就可以创建第一个图表了。为此，您需要实例化一个新的`Chart`对象，向其传递您想要在页面中使用的`canvas`的 2d 上下文。为了理解这个概念，假设您的页面中有以下元素，将用于放置图表:

```
<canvas id="skills"></canvas>
```

如果你想创建一个饼图，你必须编写以下语句:

```
var context = document.getElementById('skills').getContext('2d');
var skillsChart = new Chart(context).Pie(data);
```

其中，本例中未定义的`data`是包含要显示的数据的变量。

现在您已经知道了如何开始使用 Chart.js，是时候看一些例子了。为了简洁起见，我将在我的示例中使用该库的全包版本，但是您可以在“src”文件夹中找到所有的模块。

## 使用 Chart.js 创建饼图

在这一节中，我将向您展示如何创建一个饼图来展示一个假想的开发人员的技能(我将包括 Java，这样开发人员就不会是真正的我了！).

正如您可能很容易猜到的，第一步是包含用于绘制饼图的`canvas`元素:

```
<canvas id="skills" width="300" height="300"></canvas>
```

然后，我们需要创建用于绘制图表的数据。在现实世界中，数据是以某种方式从服务器提供的，但现在我们将使用一个固定的例子，不使用服务器。对于饼图，我们必须传递一个对象数组，每个对象可以包含几个属性。但是，为了便于使用和理解，它应该包含每个对象要显示的值、饼图切片的颜色和一个标签，如下所示:

```
var pieData = [
   {
      value: 25,
      label: 'Java',
      color: '#811BD6'
   },
   {
      value: 10,
      label: 'Scala',
      color: '#9CBABA'
   },
   {
      value: 30,
      label: 'PHP',
      color: '#D18177'
   },
   {
      value : 35,
      label: 'HTML',
      color: '#6AE128'
   }
];
```

最后，如前一节所述，我们需要获得画布的 2d 上下文，并实例化图表:

```
var context = document.getElementById('skills').getContext('2d');
var skillsChart = new Chart(context).Pie(pieData);
```

将本节中列出的所有代码片段放在一起会得到下面的输出，也是作为 JSfiddle 可用的[:](https://jsfiddle.net/fq6hy784/)

[https://jsfiddle.net/fq6hy784/embedded/](https://jsfiddle.net/fq6hy784/embedded/)

## 绘制条形图

我们要构建的下一个示例是一个条形图，显示在一些国家，一家假设公司的客户数量在 2014 年与 2010 年相比的变化情况。正如我们对上一个示例所做的那样，我们必须在页面中想要显示图表的位置放置一个`canvas`元素:

```
<canvas id="clients" width="500" height="400"></canvas>
```

接下来，我们需要创建这个假公司的数据:

```
var barData = {
    labels: ['Italy', 'UK', 'USA', 'Germany', 'France', 'Japan'],
    datasets: [
        {
            label: '2010 customers #',
            fillColor: '#382765',
            data: [2500, 1902, 1041, 610, 1245, 952]
        },
        {
            label: '2014 customers #',
            fillColor: '#7BC225',
            data: [3104, 1689, 1318, 589, 1199, 1436]
        }
    ]
};
```

如您所见，对于条形图，您必须提供一个包含名为`labels`的属性的对象，该属性指定了您想要比较的实例的名称。在我们的例子中，这些实例是意大利、英国、美国、德国、法国和日本。此外，我们必须提供一个定义对象数组的`dataset`属性，每个对象包含我们想要比较的数据。在我们的例子中，我们比较的是 2014 年和 2010 年，所以我们只需要两个对象。在这些对象中，我们必须指定一个标签来直观地提示数据是关于什么的，每个对象的值被指定为`data`属性的值，还可以选择条形的颜色。

完成后，我们就可以创建图表了:

```
var context = document.getElementById('clients').getContext('2d');
var clientsChart = new Chart(context).Bar(barData);
```

将本节中报告的所有片段放在一起会产生以下输出，也可以作为 JSfiddle 使用[:](https://jsfiddle.net/6bjy9nxh/)

[https://jsfiddle.net/6bjy9nxh/embedded/](https://jsfiddle.net/6bjy9nxh/embedded/)

## 结论

在本文中，我向您介绍了 Chart.js，这是一个可以轻松创建漂亮图表的 JavaScript 库。如您所见，这个库非常简单，在本文中我展示了一些例子。Chart.js 还为您提供了几个选项，我没有涉及到，但是您可以通过阅读[官方文档](http://www.chartjs.org/docs)来了解。

你曾经使用过这个图书馆吗？你的体验如何？如果没有，你用过不同的吗？

## 分享这篇文章