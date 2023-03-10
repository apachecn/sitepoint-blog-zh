# 用 HTML5 和 SVG 度过僵尸末日

> 原文：<https://www.sitepoint.com/surviving-the-zombie-apocalypse-with-html5-and-svg/>

随着浏览器对 HTML5 的不断采用，开发人员正在寻找越来越多的选项来创建优雅、高响应性的 ui。在可伸缩矢量图形(SVG)的例子中，旧的又是新的，而且非常巧妙。

SVG 规范已经存在了一段时间。自 1999 年以来，该规范，或者更准确地说是一系列规范，描述了一种基于 XML 的矢量图形文件格式。SVG 块定义了矢量图像的各个组成部分，如路径、形状、填充、笔画和其他特征。这为某些类型的图形提供了一个轻量级选项，否则这些图形可能会在 Adobe Illustrator 或 Inkscape 中创建，然后导出为光栅格式。它还承诺了大量动态操作图像的潜在功能——这就是 SVG 的“可伸缩”部分。

随着 HTML5 标准的广泛采用，浏览器正在将 SVG 带入新的方向，例如强调内联 SVG 的<svg>标签，使用 CSS 进行样式化，以及开放 DOM 以便可以在客户端使用 JavaScript 创建或操作 SVG 图像。</svg>

为了证明这一点，这一系列演练旨在以两种方式造福人类。首先，它将涵盖跨浏览器兼容的 SVG 示例，同时指出在采用 SVG 方面的一些差异。第二，它将或多或少地准确预测一个人在僵尸末日中幸存的几率。

## 第一步:准备

在开始之前，知道哪个浏览器将是目标是有帮助的。尽管所有现代浏览器都在某种程度上采用了 SVG，但它的实现仍然在许多方面有所不同——有些是次要的，有些是重要的。例如，同步多媒体集成语言(SMIL)规范描述了如何通过<animate>元素激活 SVG。这给了开发人员一个机会来添加用户交互性和独立的属性变化，而不需要离开<svg>元素。不幸的是，并不是所有的浏览器都能识别 SVG/SMIL 动画(尽管 SVG 动画的替代形式<a href="http://msdn.microsoft.com/en-us/library/gg193985(v=vs.85).aspx">确实存在</a>)。</svg></animate>

“我可以使用……”http://caniuse.com/对所有主要的桌面和移动网络浏览器进行了全面的对比，并指出哪些版本可以识别各种 HTML5 功能。特别有用的是它对特定 SVG 功能的深入研究，如使用[内嵌 SVG](http://caniuse.com/#feat=svg-html5) 和 [SVG 滤镜效果](http://caniuse.com/#feat=svg-filters)，这些刚刚被[添加到 Internet Explorer 10](http://msdn.microsoft.com/en-us/library/ie/hh673563(v=vs.85).aspx) 中。

## 第二步:创建 SVG 元素

这个特殊的 SVG 僵尸末日生存预测器将使用三个独立的 SVG 元素:一个“城市”窗格、一个风险因素控制面板和一个预测表本身窗格。

首先，创建一个名为“cityBox”的 600×400 根元素:

```
<!DOCTYPE html>

<html lang="en">

<head>

    <meta charset="UTF-8">

    <title>Planning for the Zombie Apocalypse with HTML5 and SVG</title>

    <script>

    </script>

    <style>

    </style>

</head>

<body>

    <svg id="cityBox" width="600" height="400">

</svg>

</body>

</html>
```

瞧。隐形盒子。僵尸世界的冬天到了。可以通过 CSS 样式添加边框，或者使用<svg>元素本身的“style”属性，或者通过在标题中定义样式，因此:</svg>

```
<style>

  #cityBox {

    border: 1px solid #000000;

    float: left;

  }

</style>
```

## 第三步:添加文本

虽然开发人员有许多方法来处理页面上的文本，但是通过 SVG 来处理文本为许多有趣的选项打开了大门，例如改变字符偏移、拉伸文本、旋转句子或使文本适合路径。

但是，在尝试这些方法之前，请向 cityBox 元素添加一个标题:

```
<svg id="cityBox" width="600" height="400">

  <text x="300" y="50">

    Surviving the Zombie Apocalypse

  </text>

</svg>
```

![SVG Element with <text>](img/d5194ddb1818bed9ac799b052af116da.png)

图 1: **SVG 元素和<文本>**

x，y 坐标定义了第一个字符的基线。从这个例子中删除它们，文本几乎从 SVG 元素的顶部消失了。

SVG 文本元素的样式有三种风格:“style”属性、定义的样式或单个 SVG 属性。但是，请注意，根据所使用的方法和浏览器，某些属性似乎会出现问题。例如，定义“字体系列”适用于所有浏览器，不管它在哪里。然而，“字体大小”的实现是不一致的。在

```
<text x="300" y="50" id="titleText" style="font-size: 36;">
```

在两种浏览器中都有效的唯一技术是将“字体大小”定义为<text>标签中的一个属性。因此，为了演示的目的，以及最大的跨浏览器兼容性，这就是我们在这里要做的。</text>

```
<text x="300" y="50" font-family="sans-serif" font-size="36"
  text-anchor="middle" stroke="#000000" fill="#000000">
```

请注意，与 CSS 一样，描边和填充颜色可以使用颜色名称或十六进制值。还要注意“文本锚”属性，它定义了文本相对于 x，y 原点的位置。由于默认为“left ”,本例将其设置为“middle ”,并将原点定义为<svg>元素的中心。</svg>

关于“文本锚”和文本还有一个有趣的怪癖:如果<text>元素超出了<svg>元素的边界，接下来会发生什么取决于浏览器。在 Chrome 中，<svg>元素边缘之外的任何东西都会被切掉。但是，在 Internet Explorer 中，文本超出了<svg>元素的边界。</svg></svg></svg></text>

像其他矢量图像一样，SVG 文本同时具有描边和填充。默认情况下，<text>元素显示黑色填充，没有描边。在本例中，添加了黑色笔画，如图 2 中的**所示。它不仅使文本变粗，而且正如在本系列后面将会看到的，笔画的颜色可以被动画化以获得有趣的效果。**</text>

![<text> element with formatting](img/f0275d15288b86715b42b0effa75507e.png)

图 2: **<带格式**的文本>元素

## 第四步:图片

虽然表面上 SVG 的目的是创建图形，但有时最好将现有的 SVG 图形合并到页面上，而不是从头开始创建。当图形是由矢量设计软件(如 Microsoft Expression Design、Adobe Illustrator 或 Inkscape)创建时，或者是从 SVG 剪贴画库中(如 [Wiki Commons](http://commons.wikimedia.org/wiki/Category:SVG) )获得时，情况尤其如此。

僵尸启示录生存预测器的城市窗格将最终显示僵尸启示录的所有元素。但开始时，它只有一个人。一个孤独、恐惧的人在逃命。本例中的人最初在 Inkscape 中启动，并被保存为一个 SVG 文件(human.svg)。(注:全部。本演练中使用的 svg 图形文件由[http://openclipart.org](http://openclipart.org)提供

元素可以以几种不同的方式工作，其中一种支持跨浏览器，另一种不支持。

第一，最简单的方法。在<svg>元素中，添加以下内容:</svg>

```
<image id="human" x="275" y="175" width="50" height="50" xlink:href="human.svg" />
```

这个标签添加了外部引用的 svg 文件(“human.svg”)，放置在 275，175，这是补偿图像大小后的城市中心。说到尺寸，图像然后被缩放到 50×50。在这种情况下，没有必要知道原始文件的大小。图像会自动转换，使其最大尺寸与指定的参数相匹配，而另一个尺寸会按比例缩放。结果应该是类似于**图 3** 的东西。

![ Auto-Scaled SVG Image on Chrome](img/46cd25bfa92836aef8ed82ea62ba78d0.png)

**图 3。Chrome 上的自动缩放 SVG 图像**

可惜不总是这样出来的。在许多浏览器中，如 Firefox 和 Internet Explorer，宽度和高度属性定义了视图区域，而不是图像大小，结果是从左上角坐标开始裁剪图像，如图**图 4** 所示。

![Auto-Scaled SVG Image on Firefox and Internet Explorer](img/ce6fae96f2b4d031f9ba307217ea7267.png)

**图 4。Firefox 和 Internet Explorer 上的自动缩放 SVG 图像**

有几种方法可以解决这个问题。一种是编辑图像本身，并将其调整到所需的尺寸。但是这有点违背了使用可缩放图形的目的。因此，将应用一个转换。

在文本编辑器中打开“human.svg”文件会显示元数据中的当前图像尺寸，在这种情况下:

```
width="342.70523"

height="482.82114"
```

但是不要误以为这些属性可以手动编辑，一切都会好的。并非如此。由于其余的 XML 数据嵌入在 SVG 文件中，更改宽度/高度元数据将导致图像遮罩根据浏览器表现出不可预测的行为。

相反，使用宽度和高度值创建一个新的元素:

```
<image id="human" x="2750" y="1750" width="343" height="483" transform="scale(.10,.10)"
  xlink:href="human.svg" />
```

在本例中，宽度和高度被设置为图像的实际值(四舍五入)。将最大尺寸(高度:483)缩小 10%会创建一个接近 50×50 的图像。但是，请注意，“转换”属性也会影响元素原点。所以原来的 x，y 坐标 275，175 需要改成 2750，1750(原始值除以刻度量:275 / .1 = 2750)才能保持同样的放置。这产生了图 5 中的结果，取自 Internet Explorer。

![<image> element with transform=scale, as seen in Internet Explorer](img/ea9811d806ca3e58aaccc6a6cfb84f34.png)

**图 5:** **<图片>元素用 transform=scale，如在 ie 浏览器中看到的**

现在惊慌失措的小人类在每个浏览器里看起来都一样。或者至少是针对本演练测试的每个浏览器。

## 第五步:基本形状

现在一些更棘手的东西已经就位，控制面板将回到大多数 SVG 教程开始的基础:简单的形状。SVG 定义了几个形状元素:<line>、<rect>、<circle>、<ellipse>、<polygon>和<polyline>。也可以使用<path>元素创建全新的形状。</path></polyline></polygon></ellipse></circle></rect></line>

预测器控制面板将由三角形“步进”控件组成，用于缩放数量、文本标签和几个带图像的圆圈。首先，创建<svg>元素及其样式(出于开发目的，它现在有一个 1 px 的边框)。</svg>

在

```
#controlPanelBox {

border: 1px solid #000000;

float: left;

}
```

在页面的中添加:

```
<svg id="controlPanelBox" width="400" height="400">

</svg>
```

对于创建三角形，<polygon>和<path>元素都工作得很好。以下两个元素看起来相同，如**图 6** 所示:</path></polygon>

```
<polygon points="50,50 100,25 100,75" stroke="black" stroke-width="1" fill="red" />

<path d="M 50 50 L 100 25 L 100 75 L 50 50" stroke="black" stroke-width="1" fill="red" />
```

![<polygon> (left) and <path> (right)](img/1b8861268e4ef9b57c1147562e2432a3.png)

图 6: **<多边形>(左)<路径>(右)**

使用<polygon>元素，多边形中的每个点都被绝对定义，并且形状被自动填充。对于<path>元素，原点在 data ("d ")属性中被定义为 Moveto ("M ")值。每个后续的 Lineto ("L ")点绘制路径，以原点结束。这最后一点封闭了路径并创建了多边形形状。没有它，红色填充仍然是三角形的形状，但是描边将缺少最后一条边。</path></polygon>

关于路径的一个很好的技巧是 Moveto 和 Lineto 值可以是相对的，只需使用小写而不是大写。这允许简单地通过改变 Moveto 值来方便地移动路径。例如，以下两个元素导致相同的形状，也与图 6 中的**相同:**

```
<path d="M 50 50 L 100 25 L 100 75 L 50 50" stroke="black" stroke-width="1" fill="red" />

<path d="M 50 50 l 50 -25 l 0 50 l -50 -25" stroke="black" stroke-width="1" fill="red" />
```

因此，虽然多边形通常更容易创建，但相对路径对于控制面板更有意义。将使用几个三角形，因此可以创建两个版本(镜像)，然后通过改变 M 值进行复制和重新定位。

每组控件还将有几个<text>元素作为标签和文本字段。后者将根据与控件的交互，在本系列的后面通过 JavaScript 进行修改。</text>

因此，对于控制面板中的第一个参数，即现有人口，元素集应该如下所示:

```
<path id="zombieLess" d="M 50 50 l 50 -25 l 0 50 l -50 -25"
  stroke="black" stroke-width="1" fill="red" />

<text id="zombieLabel" font-size="15" font-family="sans-serif"
  text-anchor="middle" fill="red" x="175" y="25">
  City Population (000s)

</text>

<text id="zombieText" font-size="25" font-family="sans-serif"
  text-anchor="middle" fill="black" x="175" y="60">

  0

</text>

<path id="zombieMore" d="M 300 50 l -50 -25 l 0 50 l 50 -25"
  stroke="black" stroke-width="1" fill="red" />
```

![controlPanelBox element with City Populaton control](img/89b71b68b9748cee68975779348741da.png)

图 7: **带有城市人口控件**的 controlPanelBox 元素

额外的好处是，元素可以嵌套在 Group <g>元素中。该组作为一个整体可以命名，风格和操纵，结果适用于该组的每个成员。在这种情况下，每个控件将被分组，只是为了便于组织。</g>

由于相对路径定义，快速复制和粘贴创建了两个相同控件集的开始。更改 Moveto 点、<text>原点、id 和文本内容后，页面现在有三个步骤控件:</text>

```
<g id="zombieGroup">

  <path id="zombieLess" d="M 50 50 l 50 -25 l 0 50 l -50 -25"
    stroke="black" stroke-width="1" fill="red" />

  <text id="zombieLabel" font-size="15" font-family="sans-serif"
    text-anchor="middle" fill="red" x="175" y="25">

    City Population (000s)

  </text>

  <text id="zombieText" font-size="25" font-family="sans-serif"
    text-anchor="middle" fill="black" x="175" y="60">

    0

  </text>

  <path id="zombieMore" d="M 300 50 l -50 -25 l 0 50 l 50 -25"
    stroke="black" stroke-width="1" fill="red" />

</g>

<g id="mallGroup">

  <path id="mallLess" d="M 50 150 l 50 -25 l 0 50 l -50 -25"
    stroke="black" stroke-width="1" fill="red" />

  <text id="mallLabel" font-size="15" font-family="sans-serif"
    text-anchor="middle" fill="red" x="175" y="125">

    # of Shopping Malls

  </text>

  <text id="mallText" font-size="25" font-family="sans-serif"
    text-anchor="middle" fill="black" x="175" y="160">

    0

  </text>

  <path id="mallMore" d="M 300 150 l -50 -25 l 0 50 l 50 -25"
    stroke="black" stroke-width="1" fill="red" />

</g>

<g id="redneckGroup">

  <path id="redneckLess" d="M 50 250 l 50 -25 l 0 50 l -50 -25"
    stroke="black" stroke-width="1" fill="red" />

  <text id="redneckLabel" font-size="15" font-family="sans-serif"
    text-anchor="middle" fill="red" x="175" y="225">

    Rednecks

  </text>

  <text id="redneckText" font-size="25" font-family="sans-serif"
    text-anchor="middle" fill="black" x="175" y="260">

    0

  </text>

  <path id="redneckMore" d="M 300 250 l -50 -25 l 0 50 l 50 -25"
    stroke="black" stroke-width="1" fill="red" />

</g>
```

![controlPanelBox element with relative <path> elements](img/19c2fd5393ad25d245f28dbdb2b7cb32.png)

图 8: **controlPanelBox 元素与相对<路径>元素**

对于最后一组控件，这个应用程序需要最基本的形状:圆形。一个<circle>元素通过定义三个主要属性来工作:center-x ("cx ")、center-y ("cy ")和 radius ("r ")。默认情况下，填充是黑色的，并且描边不存在。但是，由于这是一个切换控件，当前选择将由一个粗的红色笔划表示，而非活动选择是一个细的黑色笔划。它们都有一个白色的填充，形成了一个可以容纳图像的圆形。或者僵尸。</circle>

```
<circle id="slowCircle" cx="75" cy="325" r="40" stroke="red"
  fill="white" stroke-width="4" />

<circle id="fastCircle" cx="275" cy="325" r="40" stroke="black"
  fill="white" stroke-width="2" />
```

![Zombie Circles](img/a977a39836f018c6516a9b03f029d8f1.png)

图 9: **僵尸圈**

僵尸图像的处理方式与人类图像相同——尺寸被定义为图形的实际尺寸，然后应用“变换”属性将其缩放到原始尺寸的 0 . 16。这个比例也适用于 x，y 原点。此外，因为 SVG 元素是按顺序呈现的，所以图像需要出现在圆圈之后:

```
<circle id="slowCircle" cx="75" cy="325" r="40" stroke="red"
  fill="white" stroke-width="4" />
  <image id="slowZombie" x="375" y="1875" width="175" height="304"
    transform="scale(.16,.16)" xlink:href="zombie.svg" />

<circle id="fastCircle" cx="275" cy="325" r="40" stroke="black" fill="white" stroke-width="2" />
  <image id="fastZombie" x="1630" y="1875" width="175" height="304"
    transform="scale(.16,.16)" xlink:href="zombie.svg" />
```

![Zombie Circles with Zombies](img/9de05df28117579ec458c664b0ea784a.png)

图 10: **有丧尸的丧尸圈**

最后，添加一个标签/文本字段对，并对整个内容进行分组:

```
<g id="speedGroup">
<circle id="slowCircle" cx="75" cy="325" r="40" stroke="red"
  fill="white" stroke-width="4" />
  <image id="slowZombie" x="375" y="1875" width="175" height="304"
    transform="scale(.16,.16)" xlink:href="zombie.svg" />
  <text id="speedLabel" font-size="15" font-family="sans-serif" text-anchor="middle"
    fill="red" x="175" y="315">Zombie Speed</text>
  <text id="speedText" font-size="25" font-family="sans-serif" text-anchor="middle"
    fill="red" x="175" y="350">Slow</text>
<circle id="fastCircle" cx="275" cy="325" r="40" stroke="black"
  fill="white" stroke-width="2" />

  <image id="fastZombie" x="1630" y="1875" width="175" height="304"
    transform="scale(.16,.16)" xlink:href="zombie.svg" />

</g>
```

在其发展的这一点上，僵尸启示录生存预测器应该看起来像图 11 。

![Completed Zombie Apocalypse Survival Predictor (thus far)](img/5c0f4fe1952a7f83927d5cb06fe54f60.png)

**图 11。已完成僵尸启示录生存预测(迄今为止)**

## 下一步是什么

显然，任何使用当前状态下的预测器的人都是死定了。或者不死肉视情况而定。或者只是食物。本系列的下一步将在这里介绍的现有控件的基础上，添加一个用于预测比例的面板、一些动画和一些 JavaScript 来让按钮做一些事情。

同时，到目前为止，该项目可在以下网址查看:

[http://justinwhitney.com/zombies/zombies_part1.htm](http://justinwhitney.com/zombies/zombies_part1.htm)

*本文最初发表于:[http://msdn.microsoft.com/en-us/magazine/jj878298.aspx](http://msdn.microsoft.com/en-us/magazine/jj878298.aspx)，经允许转载。*

## 分享这篇文章