# 在 SVG 中创建奇妙的“隐形笔”效果的最佳方式

> 原文：<https://www.sitepoint.com/how-to-create-the-invisible-pen-effect-in-svg-using-vivus-js/>

![Invisible pen ](img/50dbfc3190de8e0c95997845905011bd.png)

从 90 年代早期的 BLINK 标签到今天的现代 CSS3 过渡和动画，在网页/应用程序上移动元素的能力一直是一个诱人的目标。

如今，像 SVG 这样的技术使得创建一种我称之为“隐形笔”的效果变得相对容易。在这里，观众看到一幅插图一行一行地出现，就好像是用一支看不见的笔画的一样。

这个想法并不新鲜。早在 1953 年的电影《疯狂的鸭子》中，一只看不见的手(后来被发现是兔八哥)通过不断擦除和重绘他周围的世界来折磨可怜的达菲鸭。这是一个超现实主义的杰作。

![Scene from Duck Amuck](img/cbc5c0fd11e31978089c591e8b94dea3.png)

《疯狂的鸭子》——1953 年—查克·琼斯导演

快进到 2013 年，[杰克·阿奇博尔德聪明地演示了](https://jakearchibald.com/2013/animated-line-drawing-svg/)如何操纵`stroke-dashoffset`、`stroke-dasharray`和一些 CSS 在任何 SVG 路径上创建这种“魔笔效果”。

Jake 的方法很容易在相对简单的 SVG 线条上实现，但当我们想要创建更复杂的东西时，它并不总是理想的解决方案。对于更复杂的线条动画，有专门构建的工具来帮助你，如 [Vivus.js](http://maxwellito.github.io/vivus/) 。Vivus 是一个轻量级的 JavaScript 库，它允许我们在不使用 CSS 的情况下制作 SVG 元素的动画。

我想你现在想到的主要问题是:*是什么让 Vivus 成为比纯 CSS 方法更好的解决方案？*

在我看来，Vivus 有三个值得一提的主要优势:

1.  易用性:在 Vivus 中，我们在 Vivus 的“构造器对象”和 SVG 文档中控制大部分动画属性。这使得更改更加方便和直观。只需几处调整，我们就能得到完全不同的动画效果。

2.  **自动化**:您可能已经知道，SVG 的`stroke-dashoffset`属性——这个效果的关键——只在`path`元素上可用。如果您的 SVG 图形包含`circle`、`rectangle`、`line`、`polyline`或其他常见的 SVG“非路径”对象，这可能会导致问题。

    幸运的是，Vivus 足够智能，可以自动将所有 SVG 对象转换为路径元素，这使得我们也可以将它们制作成动画。这是一个巨大的优势。

    但是，请注意，我们需要使用 Illustrator、InkScape 等矢量编辑器将所有的`text`元素手动转换为路径。

3.  **灵活性** : Vivus 为**提供了五种**预设动画类型，以及许多操作选项，这给了我们动画行为的极大灵活性。

现在，让我们开始更详细地探索 Vivus，并看看这些优势的实际应用。

**设计师注意:**是的，前面有一些代码，但是 Vivus 是*设计的*供设计师使用。即使你不是一个硬核代码猴子，一点点剪切，粘贴和编辑会让你得到一些非常酷的结果。

## 探索生存

要开始使用 Vivus，我们需要将其包含在我们的 HTML 中，如下所示:

```
<script src="https://cdnjs.cloudflare.com/ajax/libs/vivus/0.3.2/vivus.js"></script>
```

然后，我们可以按以下方式使用内联 SVG:

```
<svg id="canvas">
  <path...>
  <path...>
  <path...>
</svg>
```

剧本是:

```
<script> new Vivus('canvas', {duration: 300}, callback); </script>
```

这里，我们有一个内联 SVG 和一个 Vivus 构造函数，它有三个参数:

*   我们想要与之交互的 DOM 元素的 ID(我们的 ID 是‘canvas’)。

*   一个放置所有动画选项的选项对象。

*   以及在动画结束时调用的可选回调函数。

在我们继续之前，我们需要了解以下内容:

*   默认情况下，Vivus 按照 SVG 文档中定义的顺序绘制元素。

*   所有 SVG 元素都必须具有 stroke 属性，并且不能被填充。

*   我们不能在 SVG 中放置任何隐藏的路径元素。否则动画可能无法正常显示。

*   文本元素不能转换为路径元素。如果你想在我们的动画中使用文本，我们首先需要把它转换成路径(在编辑器中，比如 Illustrator，InkScape)。

## Vivus 的五个绘图选项

在接下来的章节中，我们将探索 Vivus 提供的所有五种动画类型。我们将使用一个非常简单的绘图(四条线)来更清楚地看到可用动画选项之间的区别。

### 延时的

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )制作的 Pen [Vivus demo:带延时](http://codepen.io/SitePoint/pen/zoOJjo/)的简单线条动画。