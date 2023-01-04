# 用 D3 可视化数据

> 原文：<https://www.sitepoint.com/visualizing-data-d3/>

D3.js 是 JavaScript 工具箱中相对较新的内容。三个 D 代表*数据驱动文档*。您可能听说过 D3 只是另一个 JavaScript 图形库，但这只是部分正确。事实上，D3 确实可以生成优秀的图形，但是它的真正价值在于它能够动态地响应数据的变化。

在本文中，我们将快速浏览一下 D3，并重点关注一些使 D3 成为基于 JavaScript 的图形的有趣方法的基本思想。我们将查看足够多的代码片段，让您了解 D3 库是如何工作的。

## 基础知识

许多人说 D3 学习曲线很陡，但这完全取决于你的视角。学习任何复杂的库都是困难的，但是如果你已经学习了 jQuery，你会学到很多在 D3 中使用的相同概念。而且，如果您熟悉 SVG(可缩放矢量图形)格式，那么您的旅程将会更进一步。

作为一个例子，考虑这一行 D3 代码，看看您是否能猜出它做了什么:

```
d3.selectAll("p").style("color", "red");
```

如果您认为它与下面的 jQuery 语句做的事情本质上是一样的，那么就给自己一个鼓励吧！

```
$("p").css("color", "red");
```

`selectAll()`函数选择所有匹配给定模式的元素，而`style()`函数对选择的元素进行样式更改。

那么 D3 和 jQuery 的区别在哪里呢？首先，它非常擅长动态创建元素——不仅仅是 HTML 元素和 CSS 属性，它还可以构建和浏览 SVG 元素。例如，以下代码选择一个 ID 为`test`的`div`元素，并附加一个具有特定宽度和高度的 SVG 元素:

```
var testBox = d3.select("#test")
  .append("svg")
  .attr("width", 400)
  .attr("height", 150);
```

这段代码在浏览器上切出一个框，并为 SVG 保留它。注意这些命令是如何链接在一起的，类似于 jQuery。然而，与 jQuery 不同，D3 的一些链接命令返回对新元素的引用，而不是最初选择的元素。在前面的例子中，`append()`函数创建了一个新的 SVG 元素，并返回对它的引用。后续的链接命令使用该元素来应用其属性。

现在您已经有了对新 SVG 框的引用，您可以在里面画些东西了。

```
testBox.append("circle")
  .style("stroke", "black")
  .style("fill", "green")
  .attr("r", 50)
  .attr("cx", 100)
  .attr("cy", 75);
```

您可能已经推断出，前面的代码在坐标空间中绘制了一个半径为 50、偏移量为(100，75)的圆。这个圆是用黑色笔画的，用绿色填充。

## D3 —它是数据驱动的！

D3 在实现数据驱动图形方面大放异彩。不幸的是，这是困难的部分开始。作为一名 D3 程序员，你必须理解数据是如何进入 D3 应用程序的，以及一旦数据到达那里它会做什么。此外，您必须考虑数据是如何离开应用程序的。

让我们回到上面创建的`testBox` SVG 元素。把这个盒子想象成一个系统，它会根据你放进去的数据自动调整。数据使用三种机制之一来处理盒子:

1.  数据进入盒子。
2.  数据在盒子里就能更新。
3.  数据离开盒子。

这些概念可以用函数`enter()`、`update()`和`exit()`来概括。

把上面的`testBox`想象成一个定位器，以圆圈的形式显示数据。每个圆圈代表一个数据点，每个数据点有三个属性。这三个属性可以表示为 x 轴上的位置、y 轴上的位置和半径。数据集可能看起来像这样:

```
var bubbleChart = [[43, 54, 23], [97, 15, 14], [114, 100, 20]];
```

显然，这个例子缺乏真实世界的感觉。更实际的是，我们会将数据包含在某种 JSON 结构中，看起来像真实数据库的输出。但是，我们将保持这个例子的简单，坚持使用这个三列矩阵。稍后，我们将在程序运行时在矩阵中添加和删除行。D3 包含一些处理数据的强大机制，包括从外部源查询数据的能力。这在跟踪动态值如天气、股票市场、地震等时非常有用。

让我们从上面的`testBox`例子开始。我们将去掉我们画的圆圈，取而代之的是让数据为我们画圆圈。

```
var bubbleChart = [[43, 54, 23], [97, 15, 14], [114, 100, 20]];
var testBox = d3.select("#test")
  .append("svg")
  .attr("width", 400)
  .attr("height", 150);
var tb = testBox.selectAll("circle").data(bubbleChart);

tb.enter()
  .append("circle")
  .style("stroke", "black")
  .style("fill", "green")
  .attr("cx", function(d) { return d[0]; })
  .attr("cy", function(d) { return d[1]; })
  .attr("r", function(d) { return d[2]; });
```

![](img/76618371bbecd6b3e170ca7e030f79a9.png)

你可以在`bubbleChart`数组中看到数据的声明，而`testBox`变量只是简单地切割出一个维数为 400×150 的 SVG 空间。当我们定义`tb`变量时，数据与 SVG 发生“连接”:

```
var tb = testBox.selectAll("circle").data(bubbleChart);
```

这一行看起来很奇怪，因为我们还没有定义任何名为`circle`的选择，所以最初看起来选择是空的。嗯，这并不完全正确，因为随后的`data()`函数调用告诉 D3 将所有圆形选择加入到`bubbleChart`数据中。

请记住，当应用程序最初运行时，盒子中没有任何数据。当连接发生时，包含在`bubbleChart`中的数据突然“进入”盒子。之后，调用`enter()`函数。`tb.enter()`调用将圆形元素附加到 SVG 框中，并用笔画和填充颜色对每个元素进行样式化。

接下来，数据结构的单个行被分解为每个圆。例如，y 位置信息由这个`attr()`函数调用设置:

```
.attr("cy", function(d) { return d[1]; })
```

这个函数有两个参数:被设置的属性的名称(在本例中是 y 位置)和该属性的值。因为这个元素已经与一个数据结构连接，所以第二个属性由一个函数调用组成，该函数调用自动作用于该数据结构的成员。D3 实现了声明式编程风格，所以您实际上并没有自己编写循环——数据结构中的每个第一级元素都会调用`enter()`函数。在这种情况下，我们有一个二维矩阵，所以在每次迭代中，不同的数组被传递给函数调用。我们所要做的就是取出数组中的各个元素，并用它们来设置每个圆的 x，y 和半径。

## 力学

到目前为止，我们已经看到了基于数据的图形渲染，但是还没有看到 D3 的动态方面。如前所述，数据进入、更新或离开系统。在上面的例子中，有三列的矩阵表示数据。D3 认为矩阵就是数据，其中矩阵的每一行都是一个额外的数据元素。为了说明数据是如何变化的，我们必须将上述大部分逻辑封装在一个函数中，然后在每次数据发生变化时运行该函数。

例如，随着函数的每次运行，我们为`bubbleChart`中的行选择新的随机值。为了更进一步，每次修改时，我们要么添加行，要么从`bubbleChart`中删除行。当添加行时，调用`enter()`函数来处理新信息。当删除行时，调用`exit()`函数来确保它们被删除。最后，当一个元素改变它的值时，调用`update()`函数来处理更新的信息。注意，本身没有`update()`功能。当调用`data()`函数将数据与图形元素连接起来时，它返回一个指向更新函数的指针。

最终的 JavaScript 代码如下所示。注意，`update()`函数(简单地说就是`tb`)将圆圈涂成红色，而`enter()`函数将新的圆圈涂成绿色。`exit()`功能简单地从图形元素中移除圆圈。另请注意，添加了一个“run”按钮，这样每次按下按钮都会生成新数据。

```
var root = d3.select("#test");
var testBox = root.append("svg")
  .attr("width", 400)
  .attr("height", 150);    
var runCircles = function() {
  var bubbleChart = [];
  var numCircles = Math.floor(Math.random() * 11); // select 0 - 10 circles

  for (var i = 0; i < numCircles; i++) {
    bubbleChart.push([Math.floor(10 + Math.random() * 390), Math.floor(10 + Math.random() * 140), Math.floor(10 + Math.random() * 40)]);
  }

  var tb = testBox.selectAll("circle").data(bubbleChart);

  tb.style("stroke", "black").style("fill", "red")
    .attr("cx", function(d) { return d[0]; })
    .attr("cy", function(d) { return d[1]; })
    .attr("r", function(d) { return d[2]; })
    .attr("opacity", .5);

  tb.enter()
    .append("circle")
    .style("stroke", "black")
    .style("fill", "green")
    .attr("cx", function(d) { return d[0]; })
    .attr("cy", function(d) { return d[1]; })
    .attr("r", function(d) { return d[2]; })
    .attr("opacity", .5);

  tb.exit().remove();
};
root.append("button").text("run").on("click", runCircles);
```

在下图中，您可以看到两次后续运行之间发生了什么。在第一次运行中，`bubbleChart`中有四个元素，因此，屏幕上有四个圆圈。一个红色的圆圈是上一次运行的更新，有三个新的数据元素，用绿色表示。

![](img/a9a5921dd7cf2a753fbeb882ac0b5bd6.png)

在下一次运行时，前面的四个元素现在显示为红色。它们已经改变了位置和大小，但它们仍然是更新的，所以它们以红色显示。与此同时，数据库中又增加了四个新元素，以绿色显示。

![](img/d9abba270e8d9ef56d536c6784c45444.png)

最后一点，D3 提供了一些奇特的方式来制作数据转换的动画。因此，上面的例子可能在现有图形元素更新时将它们从一种状态淡入和/或移动到另一种状态，而新元素可能已经淡入。通过 [D3](https://d3js.org/) 网站上的教程可以获得许多令人印象深刻的过渡效果。

## 结论

D3.js 是一个强大的 JavaScript 图形库。然而，它不是简单地渲染图形，而是可以将数据集与一组图形元素连接起来，并提供真正的数据驱动的图形环境。这篇文章触及了 D3 的一些主要概念。尽管 D3 的学习曲线相当陡峭，但是如果您已经熟悉 jQuery 和 SVG，您会发现 D3 非常容易学习。你可以在 [D3](https://d3js.org/) 网站上找到完整的细节和一些有用的教程。

## 分享这篇文章