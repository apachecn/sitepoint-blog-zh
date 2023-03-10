# 使用 SVG 图像对网站的性能有好处吗？

> 原文：<https://www.sitepoint.com/svg-good-for-website-performance/>

![SVG for Website Performance](img/abe08194d8f5b33b396de70a1e951718.png)

有几个很好的理由说明为什么可缩放矢量图形，或者说 [SVG](https://www.sitepoint.com/svg-101-what-is-svg/) ，是 web 图形格式的一个很好的选择。拥有相对较小的文件当然是其中之一。然而，这种说法并非没有限制。让我们再深入一点。

*本文是与 [SiteGround](https://www.siteground.com/go/svg-images) 合作创作的系列文章的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

## 矢量图像的优势

光栅图像，例如。JPEG，。PNG 等。由排列成网格的方形像素组成。因此，图像越大，它使用的像素就越多，从而导致文件大小增加。

不仅如此，基于像素的图形缩放效果也不太好。我的意思是。这是一朵花的. JPEG 图像，原始宽度为 300 x 225px 像素:

![Example of raster or bitmap image of a flower](img/a7a67d9306de6e56bf1ae267a9141acc.png)

这是以高得多的分辨率显示的同一幅图像:

![Example of scaled-up raster image of a flower](img/d6dcf1b1f8167186067b2f52aebbae6a.png)

请注意模糊的边缘，模糊的表面，以及图像的整体质量如何大大下降。

随着视网膜屏幕在用户设备上的普及，你的网站上出现这样的光栅图像的风险很高。一种选择是提供高分辨率的图形，这当然会严重影响网络性能。

### `srcset`和`picture`元素

幸运的是，现代 HTML 用**响应图像**来拯救我们，即`srcset`和`picture`元素，在撰写本文时，它们在所有主流浏览器的最新版本中都得到支持，除了 EI11 和 Opera Mini。

响应式图像的目标是为正在使用的设备提供最佳质量的图像。这包括提供不同分辨率的图像，但允许浏览器只加载一个符合访问设备能力的图像。

如果你很想知道这些技术是如何工作的，Saurabh Kirtani 的文章《如何使用 srcset 构建响应性图像》深入探讨了这个话题。

下面是`srcset`在实践中的样子:

```
<img srcset="image_3x.jpg 3x, image_2x.jpg 2x, image_1-5.jpg 1.5x" src="image.jpg" alt="image">
```

下面是`picture`元素的一个例子:

```
<picture>
  <source media="orientation:landscape" srcset="retina-horizontal-image.jpg 2x, horizontal-image.jpg">
  <source media="orientation:portrait" srcset="retina-vertical-image.jpg 2x, vertical-image.jpg">
  <img src="image.jpg" alt="image">
</picture>
```

正如您所看到的，虽然根据访问设备，只有一个映像副本会被提供，但这两种技术都要求您**准备并上传该映像的多个副本**到您的服务器。这对你的网站性能来说不成问题，但是会对你的时间和你的**服务器的带宽**产生负面影响。

### SVG 是独立于分辨率的

缩放是矢量图形的本质，SVG 是一种基于 XML 的矢量图像格式。SVG 由几何图形指令组成，例如形状、路径、线条等。，与像素大小无关。从文件大小的角度来看，图像以多大的尺寸呈现并不重要，因为这些指令保持不变。

独立于分辨率的另一个含义是，您不需要为不同的设备准备相同图像的不同副本；一个尺寸适合所有人，在任何屏幕分辨率下看起来都非常清晰。

也就是说，有些东西会对 SVG 文件大小产生负面影响，例如图像的复杂程度。绘图说明越复杂，文件越大。

## 对高性能 SVG 文件的建议

一般来说，web 使用的 SVG 非常简单，比如徽标、地图、图标等。与光栅图像相比，这种简单的 SVG 图像可能文件较小。

然而，你可以采取一些措施来进一步优化文件大小，并确保你的访问者在你的网站上获得令人敬畏的体验。

以下是给你的一些建议。

### 从一开始就在设计中烘焙性能

你可以自己编写简单的 SVG 图形，或者使用像 [Snap.svg](http://snapsvg.io/) 这样的 JavaScript 库来绘制矢量图形。通常，要完成这项工作，你只需启动一个矢量图形编辑器，如 Adobe Illustrator。

如果您使用图形编辑器，在设计阶段考虑简化 SVG 代码可以降低以后优化时破坏作品的风险。

在这个方向上，您可以采取的第一步是在您选择的图形编辑器中获得画布的**大小。[莎拉·德拉斯纳](https://css-tricks.com/high-performance-svgs/)建议设置为 100 x 100px 像素，上下浮动，取决于你的项目。这可以确保画布不会太小，否则会产生大量小数，你很难在不弄乱图形的情况下在稍后阶段减少这些小数。另一方面，这个尺寸也不算大。事实上，大的画布意味着相应的大量路径点。**

接下来，**减少路径点的数量**对于减少 SVG 文件的字节数至关重要。这意味着尽可能使用形状而不是路径，但也要将多个路径合并成较少的路径，除非您计划单独制作它们的动画。Adobe Illustrator 提供了简化面板来进一步减少路径点。这里有一个快速的[视频教程](http://tv.adobe.com/watch/companion-videos-for-inspire/svg-for-the-web-using-the-simplify-panel-in-illustrator-cc/)来说明它的用法。

### 智能导出 SVG

充分利用图形编辑器的导出功能。不这样做，可能意味着得到一些塞满了专有标记的 SVG 代码，并且膨胀成你不需要的东西。

例如，如果您有最新版本的 Adobe Illustrator(撰写本文时为 CC 2017)，请使用*导出为*选项导出您的 SVG 图形，并选择。svg 文件类型:

![Export As feature in Adobe Illustrator](img/79688bcf0a8ec5288b66c2a04cd2663f.png)

Illustrator 的面板提供了一些设置来帮助您保持文件大小，并输出干净的、几乎可用于 web 的标记。您甚至可以通过单击面板内的“显示代码”按钮来预览代码。

![Options in Save As panel in Adobe Illustrator](img/31ff396f33efd9a508a7b51450d9cdb0.png)

Geoff Graham 所著的《从 Adobe Illustrator 中获取 SVG 的不同方法》是一本很好的读物。

### 使用 SVG 优化工具压缩更多字节

一旦您的 SVG 图形被导出，您仍然可以使用一个优秀的 SVG 优化工具将其进一步缩小。

SVGOMG (和它的[基于网络的 GUI 版本](https://jakearchibald.github.io/svgomg/))和[彼得·科林里奇的 SVG 编辑器](https://petercollingridge.appspot.com/svg-editor)是最受欢迎的工具。

查看 Guillaume Cedric Marty 的[优化 SVG 图像](https://hacks.mozilla.org/2015/03/optimising-svg-images/)，了解更多关于如何使用 SVGOMG 的信息。

### 启用 Gzipped SVG 文件的交付

SVG 优化待办事项列表中的最后一步是在服务器上启用 [gzip](http://www.ietf.org/rfc/rfc1952.txt) 压缩。SVG 只是 XML 标记，因此它可以毫无问题地进行压缩。

文件减少的好处是显著的。[SVG 1.1 规范的附录 J](https://www.w3.org/TR/2011/REC-SVG11-20110816/minimize.html) 显示了未压缩和压缩 SVG 文件示例之间的比较表。结果压倒性地支持压缩，文件大小减少了 77-84%。

## 结论

SVG 图形是网络的绝佳选择。用于简单的图标、标志等。通常比它们的栅格对应物性能更好，尤其是如果您采取了本文中列出的一些预防措施。

我上面提到的建议并不详尽。你可以挑战极限。对于详细的优化技术，下面的资源是必读的:

*   [W3C SVG 1.1 规范](https://www.w3.org/TR/2011/REC-SVG11-20110816/)
*   [网络上的 SVG](https://svgontheweb.com/)
*   Sarah Drasner 著[高性能 SVGs](https://css-tricks.com/high-performance-svgs/)
*   [优化 Web SVG 交付的技巧](https://calendar.perfplanet.com/2014/tips-for-optimising-svg-delivery-for-the-web/)Sara Soueidan。
*   [优化 SVG](https://medium.com/larsenwork-andreas-larsen/optimising-svgs-for-web-use-part-1-67e8f2d4035#.xjvct057x) 作者 Andreas Larsen

你的 SVG 优化工作流程是怎样的？点击评论框分享！

## 分享这篇文章