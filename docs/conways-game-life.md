# 康威的“生活游戏”

> 原文：<https://www.sitepoint.com/conways-game-life/>

很多年前，当我第一次学习编程时，我被约翰·康威的生命形式模拟“生命的游戏”迷住了。多年来，在众多平台上出现了许多用不同语言编写的变体。无论选择哪种语言，对任何初露头角的程序员来说，编写自己版本的《人生游戏》都是必经之路。这就是我打算在本文中演示的内容。

然而，随着今天浏览器和相关编程语言的无处不在，我们可以放弃传统的编码环境和语言。相反，我们可以通过编写一个在浏览器中运行的游戏版本来学习一些逻辑编码和数组处理。

## 游戏板

游戏棋盘由格子组成。每个细胞可以是逻辑上的开或关，这意味着该细胞中存在一种生命形式，或者该细胞是空的。网格可以是我们选择的任何大小，但通常 10 x 10 的网格是初学者的起点。

此外，我们将从预先确定的网格或种子生成开始，而不是使用鼠标或键盘输入。下面的网格显示了我们将使用的种子代，这将导致仅 11 代的振荡种群。

![game of life](img/8fce8ffae688daa13ed67168affbfc9a.png)

## 传播规则

传播的规则可以根据您的喜好变得复杂，但是在这个游戏的极简实现中，我们将使用最简单的规则:

*   如果一个单元格为空，并且正好有三个邻居，则填充该单元格。
*   如果某个单元格已填充，并且相邻单元格少于两个或多于三个，则清空该单元格。

就这么简单。

```
adjacent = countAdjacent(i, j);
switch (generationThis[i][j]) {
  case 0:
       if ( (adjacent == 3) ) {
         generationNext[i][j] = 1;
       }
       break;
  case 1:
       if ( (adjacent == 2) || (adjacent == 3) ) {
         generationNext[i][j] = 1;
       }
}
```

这个演示简单的另一个方面是只有一种生命形式。一个以上的生命形式，每一个都有自己的颜色，这将是一个非常有趣的模拟，但会增加编码的复杂性，不适合这个演示。

## 世代

这个生命体模拟有一系列可能的结果:

*   灭绝。
*   稳态人口。
*   人口波动。
*   开放式人口变化。

通过检查用于存储生成数据的数组，可以使用 JavaScript 捕获除最后一项之外的所有内容。实现这一点的一个简单方法是在它们自己的数组中存储三个连续的代。然后，在计算每个新的世代之后，比较寻找这三个状态的数组。

*   如果新的一代完全空虚，生命形式就灭绝了。
*   如果相邻的几代是相同的，种群是稳定的。
*   如果下一代和上一代是相同的，那么种群是振荡的。

这是所需逻辑的基础，所以现在让我们考虑如何在浏览器中显示游戏板。

## 用户界面

HTML、CSS 和 JavaScript 都是生活游戏所需要的。这个例子使用了`CANVAS`元素来显示网格。同样可以使用由`DIV`元素组成的网格，也可以使用`TABLE`，但是让我们通过使用 HTML5 和 CSS3 来更新游戏人生。

游戏板只需要一个画布元素和一个按钮来计算下一代。

```
<form>
<p>Generation: <span id="generation"></span> &nbsp; <span id="status"></span></p>
<canvas id="gameboard"></canvas>
<input type="button" value="generate next" id="btnNext">
</form>
```

页面加载后，可以计算种子代，之后每次按下按钮都会将游戏进行到下一代。作为使用按钮的替代方法，可以使用 JavaScript 的`setTimeout()`函数自动计算每一个连续的世代。然而，这最好留到我们确定代码运行正常，并且我们知道我们可以捕获上面列出的三种生成状态。

```
<script src="gameoflife.js"></script>
<script>
$(document).ready(function() {
  seedGeneration();
  $("#btnNext").click(function(){
    nextGeneration();
    checkStatus();
    copyGrids();
    drawGeneration();
  });
});
</script>
```

这就是在浏览器中最小化实现《生活的游戏》所需要的一切。这篇文章的完整代码，包括`gameoflife.js`文件，可以从下载[。](https://uploads.sitepoint.com/wp-content/uploads/2013/10/gameoflife2.zip)

对界面的改进是提供交互式用户输入来设置种子生成的单元格，但这超出了本演示的范围。

## 进一步阅读

dmoz 开放目录项目的游戏人生
[游戏人生新闻](http://pentadecathlon.com/lifenews/)

## 摘要

在本文中，我们介绍了编写基于浏览器的约翰·康威的“生命游戏”的基本要素。仅仅使用 HTML、CSS 和 JavaScript，我们已经看到了如何创建一个简单的模拟，它在浏览器中本地运行，一个传统上用 BASIC 和 Pascal 等语言编写的模拟。

## 分享这篇文章