# CSS 中的长度单位

> 原文：<https://www.sitepoint.com/look-at-length-units-in-css/>

衡量事物和其他方面一样，是网页设计的重要方面。CSS 中至少有 10 种不同的度量单位，这个事实本身就说明了问题。

每个单元都有其特定的用途，并使网页对各种设备更加灵活。一旦你熟悉了所有这些单位，你将能够更精确地确定你的元素的大小。在本教程中，我们将看看 CSS 中的不同单元，我将讨论在什么情况下使用哪些单元，以及如何使用它们。

## 绝对长度单位

[绝对单位](http://dev.w3.org/csswg/css-values-3/#absolute-lengths)是物理世界中实际测量值的数字表示。这些单位与屏幕大小或分辨率无关。因此，绝对长度单位不适用于数字设备或分辨率未知的情况。这些单位更适合于为物理媒体(如印刷品)进行设计。

绝对单位包括:

*   厘米(厘米)
*   毫米
*   英寸
*   个人电脑(十二点活字)
*   pt(点数)
*   像素

请注意，该规范的编辑草案也包括四分之一毫米(q)单位，但这是新的，似乎没有任何浏览器支持。

您可能会注意到，在使用绝对长度时，不同屏幕上特定单位的相同值之间存在差异。这是因为屏幕的 DPI(每英寸点数)不同。与分辨率较小的屏幕相比，分辨率较高的屏幕具有较高的 DPI，从而使图像或文本看起来较小。

所有绝对单位中使用最广泛的是像素(px)。一个像素通常被理解为屏幕上的一个点，尽管在技术上它比 T1 更复杂。它是最小的度量单位，通常是其他单位的基准。

其他单位如英寸、毫米和厘米代表这些单位的物理尺寸。点(pt)单位代表 1/72 英寸，pica (pc)代表 1/6 英寸。下面是一些利用了六种常见绝对单位的 CSS:

```
p {
  border-top: 0.5in solid blue;
  border-bottom: 18mm solid green;
  border-left: 1cm solid red;
  border-right: 40px solid black;
  letter-spacing: 0.4pc;
  font-size: 20pt;
}
```

这个 CodePen 演示展示了上面的代码:

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )使用 CSS 中不同绝对单位的 Pen [演示。](http://codepen.io/SitePoint/pen/ZGmGVR/)