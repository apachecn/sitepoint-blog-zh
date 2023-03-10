# 带有 HSV 和透明 png 的皮肤和调色板

> 原文：<https://www.sitepoint.com/skinning-and-color-palettes-with-hsv/>

我在为[钱德勒服务器(又名 Cosmo)](http://chandlerproject.org/Developers/WebHome) 开发 Ajaxy Web UI，这是一个与[钱德勒](http://chandlerproject.org/)个人信息管理器(PIM)一起工作的开源服务器。你可以在我们的免费在线服务 [Chandler Hub](https://hub.chandlerproject.org/) 上创建一个账户，看看网络界面。

随着我们开始在 Web UI 中实现越来越多的日历功能，我最终需要一组调色板来用于每个用户的日历，这样他们就可以一眼就区分它们。(下一步是让用户像 Chandler Desktop 那样为日历选择颜色。)

用户的每个日历都需要一个类似事件颜色的调色板，用漂亮的渐变来使事件块在 UI 中突出一点。由于这是一个非常重 JavaScript 的 Web UI，我需要尽可能以最轻量级的方式来做这件事。由于需要大量的下载，一堆不同颜色的图片并不理想。

我最终使用了两个技巧，给了我大量不同的颜色和阴影，并且只使用了两个图像。

## 用于颜色渐变的透明 png

我的好友杰里米·爱泼斯坦向我建议的第一个技巧是，在彩色背景上使用透明的 PNG 文件来创建颜色渐变。(IE6 中的透明 png 存在问题，但众所周知的 [alpha transparency hack](http://www.satzansatz.de/cssd/tmp/alphatransparency.html) 使事情相对运行良好。)

以下是白色背景下的图像:

![PNG transparent gradient on white background](img/d8905ff8daffe8b0c8635a444254140b.png)

正如你所看到的，它看起来不太像，但是当你在它后面添加一个漂亮的饱和颜色时，PNG 中的透明度使它看起来像一个实际的颜色渐变。下面是 Chandler Server 中日历事件的平铺渐变背景图:

![Chandler Server screengrab](img/ebd46e487011bba419ad998fa5931f27.png)

现在，有了这种技术，你所要做的就是改变它所在的框的 CSS 背景色属性，你就可以用一个图形拥有基本上无限不同的颜色渐变。相当漂亮。

## 用于创建调色板的 HSV

由于日历上的事件框不仅仅只有一种颜色，所以我还需要一种方法来生成一组相关的颜色，所有这些颜色都基于该日历的同一个基色。

第二个技巧让这变得非常简单——我使用了 [HSV 颜色](http://en.wikipedia.org/wiki/HSV_color_space)，并通过改变*饱和度*和*值*来制作我的相关颜色。所以我的蓝色的基础色调为 210，我可以得到浅蓝色(饱和度:10，值:100)，深蓝色(饱和度:100，值 80)，等等。将基本色调改为 120 可以得到类似的绿色色调，色调为 0 可以得到红色。

当然，CSS 目前不支持 HSV 颜色值——你必须指定十六进制或 RGB 值。但是颜色转换是许多 JavaScript 库的特性，所以很容易将 HSV 颜色转换成浏览器可以呈现的颜色。

Chandler Server 使用 Dojo 工具包中的`hsv2rgb`函数。(我们使用的是 Dojo 0.4——看起来颜色转换代码在 0.9 中还没有找到归宿。)HSV 到 RGB 的转换器也在我维护的 [Fleegix.js JavaScript 工具包](http://js.fleegix.org/)的插件中[可用。](http://js.fleegix.org/plugins/color/convert)

要将一组 HSV 值转换为 RBG 以便在您的 CSS 中使用，您应该这样做:

```
var rgbArray = fleegix.color.convert.hsv2rgb(210, 20, 20);
var rgbString = rgbArray.join(',');
someDiv.style.backgroundColor = 'rgb(' + rgbString + ')';
```

要调整蓝色的阴影，更改传递给`hsv2rgb`的第二个和第三个值。要更改基色，请更改第一个参数。或者，打开 GIMP 或 Photoshop，尝试不同的 HSV 颜色，直到找到让你满意的颜色。

## 更多可能性

这两种技术一起打开了各种可能性，包括用最少的努力为你的应用换肤的能力。一旦你设置好 HSV 色调，你所要做的就是改变基本色调，得到一套完全不同的颜色。你甚至可以让你的用户用一个简单的滑块来选择他们的颜色。

这让你可以用最少的下载成本创建几乎无限数量的相关颜色调色板，以及一些非常漂亮的渐变效果。你的用户可以得到一些好看的东西——而不必整天坐在那里等待它从服务器上下来，这对于一个大量使用 JavaScript 的 Web 应用程序来说非常重要。

如果你对 JavaScript 或 Ajax UI 开发感兴趣，一定要来看看 chandlerproject.org 的 Chandler Server (Cosmo ),或者在 Freenode 的#cosmo 的 IRC 上给我们留言。

## 分享这篇文章