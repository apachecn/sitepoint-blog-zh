# 未来是 SVG——非常好

> 原文：<https://www.sitepoint.com/the-future-is-svg-so-very-good/>

你在左边看到的图片并不特别引人注目——一幅为了在网上展示而出口到 JPG 的精美小插画。不言而喻，JPG 的压缩稍微降低了它对原作的保真度，同时它也没有继承原作的“可编辑性”。

然而，如果你碰巧运行最新的 [Firefox 1.5 beta](http://www.mozilla.org/projects/firefox/) ，你现在可以[在浏览器](https://www.sitepoint.cimg/svg/pedalcar.svg)中查看完全相同的图形，没有插件或扩展。

如果你还没有 1.5 版本，你将会看到*几乎是*构造图像的优雅标记。

看起来是这样的，但是更多:

 `<radialgradient id="XMLID_2_" cx="243.9626" cy="272.9944" r="244.1311" gradienttransform="matrix(1 0 0 -1 0.0376 532.9946)" gradientunits="userSpaceOnUse"></radialgradient>  <path fill="url(#XMLID_2_)" stroke="#990000" stroke-width="5" d="M480,500c0,6.627-5.372,12-12,12H20c-6.627,0-12-5.373-12-12
				V20c0-6.627,5.373-12,12-12h448c6.628,0,12,5.373,12,12V500z"></path>` 

SVG 是一种可爱的可读语言。浏览代码，不难理解它在做什么。

事实上，如果你更新了你的 Firefox，在 Croczilla 上还有更多有趣的 SVG 例子，包括一些简单的动画，可编辑的样条曲线，甚至还有一个非常好用的俄罗斯方块[。](http://www.croczilla.com/svg/samples/svgtetris/svgtetris.svg)

Illustrator 的好处是它不仅允许你导出 SVG 格式的文件(我想是从 8.0 版开始的),而且可以原生读取——这突然看起来比上周这个时候有用多了。干得好，Adobe。我认为你这次搭错了公共汽车(但我们不要提“VRML 的事情”)。实时效果(如投影)被转换为 PNG，因此在重新打开文件时不再可编辑，但 SVG 的行为与 Illustrator 中的任何 AI 文件完全一样。如你所料，文件大小非常好。这里的 SVG 示例的时钟为 15k——JPG 占用的空间小得多，但是重 3k。

当然，Adobe 并不掌握“SVG 巴士”的所有入场券。如果你不使用 Illustrator，有很多应用程序可以导出 SVG，包括 [Gimp](http://www.gimp.org/) ( [通过插件](http://registry.gimp.org/plugin?id=4597))、 [Mayura](http://www.mayura.com/) 和 [Inkscape](http://www.inkscape.org/) 。

毫无疑问，这项技术还处于早期阶段，我们还需要考虑是否在现实世界中开始使用 SVG 以及在哪里使用，但是为打印样式表提供清晰的 SVG 徽标作为第一选择的想法已经在今天早上引起了一些讨论。

未来一些非常有趣的可能性

*注意:在升级之前，请注意插件开发人员通常需要一点时间来赶上进度，因此您的一些扩展可能无法工作。如果您热衷于原生 SVG 和完整的扩展，这里讨论的[变通方法](http://www.projects1.com/firefox/exthacks/FFnightlyextensions.html)可以让您重新启用它们。

## 分享这篇文章