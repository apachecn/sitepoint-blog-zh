# 支持高密度视网膜显示器的 5 种方法

> 原文：<https://www.sitepoint.com/support-retina-displays/>

Brendan Davis 在我最近的文章[“响应式网页设计和滚动条:Chrome 的实现更好吗？”中提出了一个有趣的观点](/rwd-scrollbars-is-chrome-better/):*RWD 断点会受到高像素密度屏幕的影响吗？*

简短的回答是:不会——但我们需要更深入地研究，看看它们可能导致的问题。

## 什么是视网膜？

“视网膜”是苹果公司双密度屏幕的品牌名称，但其他制造商也在制造类似的显示器。该技术用于最近的 iPhones、iPads、MacBook Pros 和其他高端设备。

例如，MacBook Pro 15”的分辨率为每英寸 2880×1800 或 220 像素。在这个比例下，大多数人无法注意到典型观看距离下的单个像素——应用程序和网站会太小而无法使用。

因此，该设备恢复到 1，440×900 的标准分辨率，但增加的像素可用于使字体和图形看起来更平滑。

## 有什么问题？

标准分辨率的位图图像在 Retina 显示屏上看起来会有块状。一张 400 x 300 的照片被放大到 800 x 600 像素，但是没有额外的细节。与平滑字体和其他高分辨率图像相比，这一点非常明显。

## 真实世界的用法

如果你看看网络，你会被原谅认为每个人都有视网膜显示器。目前，它只在高端设备上可用，但这些都是开发者梦寐以求的，所以它导致了不成比例的在线讨论量。在现实世界中，使用类似显示器的人的百分比很低。

让我们把它放在上下文中:如果你不是为 1%的 IE6/7 用户开发，你可能不应该太担心使用 Rentina 的人——特别是因为他们仍然可以查看你的网站。

也就是说，类似视网膜的屏幕最终将移植到所有设备上。现在没什么理由焦虑，但提前做些计划也没什么坏处。让我们按照推荐的顺序来看看选项…

## 1.使用 SVGs 和 CSS3 效果

线索就在名称中，但是可缩放矢量图形是……*可缩放的！*SVG 变得多大并不重要——它总是平滑的，因为它是使用矢量(线条和形状)而不是单个像素来定义的。

SVG 对于照片来说并不实用，但是对于徽标、图表来说却是理想的。主要的缺点是缺乏对 IE8 和更低版本的支持，但是你总是可以提供一个 PNG 后备或者使用一个垫片，比如[raphal](http://raphaeljs.com/)或者 [svgweb](http://code.google.com/p/svgweb/) 。参见:[如何给你的网页添加可缩放矢量图形](https://www.sitepoint.com/add-svg-to-web-page/)。

您也可以完全替换一些图像。例如，定义为图形的标题、渐变、拐角或阴影可以单独使用 CSS3 来再现。它们将呈现更好的质量，导致更少的 HTTP 请求和使用更少的带宽。

## 2.使用 web 字体图标

我使用[网页字体图标](https://www.sitepoint.com/webfont-icons/)越多，我就越喜欢它们。像 SVG 一样，字体是矢量，所以它们是可伸缩的，所以你可以使用包含图标的字体集。它们非常适合小而常用的形状，如电子邮件信封、电话、小工具控件和社交媒体徽标。它们也适用于包括 IE6+在内的所有浏览器。

有大量的商业和免费图标字体集可用:

*   [打字员](http://typicons.com/)
*   [字体牛逼](http://fortawesome.github.io/Font-Awesome/)
*   [标志性的](http://somerandomdude.com/work/iconic/)
*   [基础](http://www.zurb.com/playground/foundation-icons)

或者你可以使用托管字体服务，如[我们喜欢图标字体](http://weloveiconfonts.com/)。

我建议使用在线工具，比如 [Fontello](http://fontello.com/) 或 [IcoMoon](https://www.sitepoint.com/icomoon-webfont-icon-packs/) 来创建你自己的定制图标。

## 3.尽可能使用高分辨率图像

Retina 的像素是标准屏幕的四倍。如果您有一个 400 x 300 的图像(120，000 像素)，您需要使用 800 x 600 的替代图像(480，000 像素)才能在高密度显示器上很好地渲染它。

但是，高分辨率文件的大小不一定要大四倍。每个图像都是不同的，但是如果它包含可以省略的实心色块或细节，那么使用 800 x 600 的图像并在浏览器中缩放它可能是实用的。

实际一点:如果标准图像是 200Kb，高分辨率版本是 250Kb，那么使用图像替换技术的好处可以忽略不计。始终使用更好的版本。

## 4.使用 CSS 图像替换

有时候，高分辨率版本的图像会放大四倍甚至更多。在这些情况下，您可能需要考虑图像替换技术，即在 Retina 显示屏上用更大的替代图像替换标准图像。可以使用以下媒体查询代码:

```
#myimage {
	width: 400px;
	height: 300px;
	background: url(lo-res.jpg) 0 0 no-repeat;
}

@media
screen and (-webkit-min-device-pixel-ratio: 1.5),
screen and (-moz-min-device-pixel-ratio: 1.5),
screen and (min-device-pixel-ratio: 1.5) {
	#myimage {
		background-image: url(hi-res.jpg);
	}
} 
```

缺点:

1.  您将需要创建和维护两组图像。
2.  有些浏览器会下载这两种图像。

请记住，这些用户中的许多人将在较慢的移动网络上使用智能手机或平板电脑。检测连接速度比确定像素密度更有益。

## 5.使用 JavaScript 图像替换

视网膜显示检测可以使用以下代码实现:

```
var isRetina = (
	window.devicePixelRatio > 1 ||
	(window.matchMedia && window.matchMedia("(-webkit-min-device-pixel-ratio: 1.5),(-moz-min-device-pixel-ratio: 1.5),(min-device-pixel-ratio: 1.5)").matches)
); 
```

确定 Retina 显示屏后，您可以:

1.  遍历所有页面图像并提取 URL。
2.  将“@2x”附加到文件名中，并尝试使用 Ajax 加载结果图像 URL。
3.  如果找到，用高分辨率的替代图像替换当前图像。

幸运的是，艰苦的工作已经在 retinajs.com 为你完成了。虽然它只增加了 4Kb 的重量，但高密度显示设备将下载图像两次，尽管第二次将在页面加载后作为后台进程发生。

我的建议是:实用一点，简单一点。不要花费过多的时间试图在用户相对较少的设备上解决小的渲染问题。当然，当你的老板收到他的新 iPad 并开始抱怨图像质量时，这些都不重要…

对本文的评论已经关闭。有一个关于 retina 显示屏的问题？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?52-(X)HTML?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章