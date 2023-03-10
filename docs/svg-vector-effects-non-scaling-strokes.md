# 为什么 JPEGs 像麦当劳的苹果派(而 SVG 不像)

> 原文：<https://www.sitepoint.com/svg-vector-effects-non-scaling-strokes/>

上周我们看了一个简单的免费 SVG 编辑器，名为 [Boxy SVG](http://boxy-svg.com/) ( [它在这里，以防你错过](https://www.sitepoint.com/boxy-svg-a-fast-simple-insanely-useful-svg-editor/))。
今天我想接着讲下去。但首先，这里有一个想法需要考虑…

> SVG 不是一种图像格式——它更像是一种图像

**![McDonald's hot apple pie](img/666da7f33efa5f0e3c4e7e8fae4424ea.png)

麦当劳的苹果派——要么接受，要么放弃。

这使得它与我们过去使用的所有基于像素的格式非常不同。让我解释一下。

你可以把我们经典的像素混合图像格式——JPEG、gif 和 PNGs 想象成麦当劳的苹果派。每一种都有精确固定的包装数量。你不能让麦当劳的服务人员给你做一个“家庭大小”的馅饼，或者弄一块“半片”来配咖啡。

嗯，技术上来说，你 ***可以*** 问但是你会得到一个很奇怪的眼神。

麦当劳馅饼对一个人来说是合理的，但如果你需要与 2、3 或更多人分享，你需要进一步扩展你的小馅饼-即每个人得到的更少。事情就是这样，仅此而已。

但是 SVG 就像有一个非常好的苹果派*配方*。

![Baking pies](img/be5e6756590678adb838048d9489a9c3.png)

你需要多少馅饼？

你拿着说明书，想做多少派就做多少派。

当然，食谱里写着数量——8 个苹果，4 杯面粉等等。但是如果你需要养活两倍的人，你只需要加倍测量。如果你想喂一半的人——只需将数量减半。这是*按比例设计的*。

不仅如此——因为你可以控制馅饼的制作方式，你甚至可以根据不同人的需求定制基本的馅饼食谱。也许南娜喜欢在她的馅饼里放一把黑醋栗。你哥哥喜欢蛋羹。对原始食谱进行细微的、深思熟虑的改变既容易又有价值。事情在麦当劳是不可能的。

当然,“SVG”中的“ **S** ”部分代表“可伸缩”,这也是 SVG 对 web 如此有用的原因之一。但是'*可伸缩性*'本身并不总是意味着“更可用”。微小的东西不管多锋利都很难看到。有时我们需要改变我们的布局显示方式。SVG 可以帮助我们。

## 用 SVG 制作图表

这是我用 Boxy SVG 创建的 SVG 图表。它显示了自 1960 年以来,“星际迷航”和“星球大战”这两个词在书中被提及的次数。

![A simple chart made in Boxy SVG](img/4b39ecee2ba77ad192a5951ea9981dd6.png)

用 Boxy SVG 制作的简单图表

你可能通常会在网上看到这种 JPEG 或 PNG 格式的图表——事实上，如果你*希望*将它从 Boxy SVG 导出为 PNG 格式。

### 好吧，那么，为什么不把它变成一个 JPG 呢？

好问题。

## 原因 1:比较文件大小

*   ![JPEG version](img/0713c053eb7de0415a550d2fdf0cd815.png)
    JPEG 版本为 **47kb** 。
*   ![Raw Boxy SVG](img/1831d7f46d9fec4e40dadf491b797381.png)
    来自 Boxy SVG 的 raw SVG 版本是 **14kb** ( [SVG 链接](https://www.sitepoint.com/wp-content/uploads/2016/04/1461826688startrek-vs-starwars-800-v2.svg))。Chrome 和 Firefox 之间的文本定位也有一些变化。
*   ![Raw Boxy SVG](img/df5bf3d508a6b97df6fde18b850316fe.png)
    稍微整理一下，我就搞定了 **10kb** ( [SVG 链接](https://uploads.sitepoint.com/wp-content/uploads/2016/04/1461758582optimised.svg))。清理工作也消除了大部分浏览器文本定位的不一致。

开箱即用的四四方方的 SVG 图表并不完美。[查看来源](https://uploads.sitepoint.com/wp-content/uploads/2016/04/1461758582optimised.svg)你会看到:

*   文本中一些额外的空 TSPANs
*   极端的数字精度让数字变得比实际需要的更长更难用(像 SVGO 这样的工具可以帮你解决这个问题)
*   在编辑器中移动的元素倾向于将变换添加到它们原来的 X & Y 位置。理想情况下，任何重新定位都将在原来的 X & Y 坐标上进行。
*   有许多内联属性可以移动到文档顶部的样式块中——下周会有更多的介绍

所有这些对于 SVG 爱好者来说都很有趣，但是对于想要使用 Boxy SVG 的设计人员来说可能并不太重要。即使不触及这些小问题，这个四四方方的 SVG 文件也比我用过的任何支持 SVG 的编辑器都更干净、更易读。

在清理之前，它的大小几乎是 JPEG 的三分之一。一点点查找和替换就可以在五分钟内解决这些问题。

当然，上面的两个 SVG 文件在 2000px(如果需要的话)下仍然是清晰的，文件大小没有增加。JPEG 当然不会。

尽管如此，网速还是很快——我们不会在朋友之间为了超过 30kb 而争斗，对吧？

## 原因#2: SVG 具有非缩放笔画

![Showing the same image at different scales. The yellow line stays the same thickness regardless of image scaling.](img/f04a82209b64e264a4ed65412a9a1e66.png)

以不同的比例显示同一幅图像。无论图像缩放比例如何
，黄线的粗细保持不变。

这是我昨天玩 Boxy SVG 时才知道的 SVG 技巧。

虽然压缩图像使其适合小屏幕总是很方便的，但是有一点我们的线条可读性很容易出问题。类似地，高度放大的图像线会变得又肥又丑。

不幸的是，在我们的 png 和 jpg 中，这是一个无法解决的问题。我们的像素是刻在石头上的，我们只能忍受它。

### 非缩放笔划

![Animation: Non-scaling strokes in action.](img/8d11e4b98bc27bd5fb9f212757c2e45f.png)

但是 SVG 有一个很酷的内置解决方案来解决这个问题——一个名为`vector-effect= "non-scaling-stroke"`的路径属性。该属性使应用它的任何线条显示为相同的宽度，而不管图像的缩放比例如何。

![](img/df5bf3d508a6b97df6fde18b850316fe.png)

SVG: ↔ Resize your browser window ↔

试着上下调整你的浏览器窗口，你会注意到黄色的星球大战线保持同样的宽度，而它周围的一切都在缩放。试着让你的 JPEG 文件做到这一点。

![Non-scaling stroke checkbox shown in the STROKE panel in Boxy.](img/4402ec35710646e63baf009a0e61745a.png)

为了在 SVG 代码中实现这一点，我们需要手动将“`vector-effect= "non-scaling-stroke"`”添加到正确的“路径”中。

但是令人高兴的是，Boxy SVG 使得在编辑器中使用非缩放笔画变得很容易。

首先选择需要防止结垢的路径。在右边的描边面板中，就在“宽度”设置的下面，你会发现一个小的“非缩放描边”复选框。这就是全部了。

现在，当然，在许多场景中，您可能更喜欢您的行缩放——默认行为仍然是 ***是*** 。但是，正如您可以想象的那样，这是一个特别有用的功能，可用于地图、图形和图表。

在某些方面，这是 Boxy SVG 的一个边缘特性。似乎很少有人(包括我自己)意识到'*非缩放笔画*'是 SVG 规范的一部分。

但事实上，Jarek Foksa——Boxy SVG 的开发者——不仅知道它，而且还优雅地将其构建到他的 Boxy 用户界面中，这给了我很大的信心，他真的知道自己在做什么。我们通过电子邮件交谈过，他有一些很棒的计划。

我真的很期待未来几个月他会把这个应用带到哪里

*原载于 [SitePoint 设计简讯](https://www.sitepoint.com/newsletter/)* 。

## 分享这篇文章**