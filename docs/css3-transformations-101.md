# CSS3 转换 101:什么是转换？

> 原文：<https://www.sitepoint.com/css3-transformations-101/>

CSS3 转换是一种在二维或三维空间中转换标准 HTML 元素的方式。把它想象成通过移动、拉伸或挤压一个物体的坐标来塑造它。虽然变换经常被组合起来产生效果，但不要把它们与用于在两种状态之间平滑地激活元素的[过渡](/css3-transitions-101/)混淆。

这是关于 CSS3 2D 和 3D 变换的五部分系列的第一部分。每篇文章可以单独阅读，也可以不按顺序阅读。

## 无聊的盒子

HTML 页面是使用矩形块创建的。你可以使用圆角和一点阴影，但是在表面之下，我们仍然有一个矩形。Photoshop 等图形包中的对象和图层通常也是如此。但是设计者可以用很多方式操作一个项目，比如移动它到其他地方，改变它的大小，旋转它，倾斜它等等。

CSS3 2D 变换模块允许你单独在 CSS 中应用类似图形包的操作效果。值得一提的是，这些效果中有许多用其他方法很难达到；你需要回到图像，使用复杂的 JavaScript 和 canvas 解决方案，或者 Flash 或 Silverlight。这些选项都不允许您直接操作 HTML 元素；你需要用别的东西替换原来的盒子。

## 基本语法和跨浏览器支持

使用传递了函数名和适当数量的参数的`transform`属性来应用转换:

```
transform: function(values);
```

`transform`被 Firefox、IE10 和 Opera 12 理解为不带前缀。

在撰写本文时，Webkit/Blink 浏览器(Chrome、Safari 和 Opera 15+)需要一个-webkit 前缀:

```
-webkit-transform: function(value);
```

最后，IE9 支持带有-ms 前缀的基本 2D 变换:

```
-ms-transform: function(value);
```

我不会在这些演示中包含特定于 IE9 的代码——浏览器应该在几个月内寿终正寝。

然而，要小心跨浏览器的一致性是有缺陷的，尤其是当你把变换和其他属性和动画结合起来的时候。如果你回忆我的 [**基本动画示例**](https://blogs.sitepointstatic.com/examples/tech/css3-animations/css3demo.html) ，你会注意到 Chrome 在动画周期中忘记应用不同的背景颜色和边框半径。虽然转换失败不太可能破坏页面，但还是要尽早测试，并且经常在各种浏览器上测试。

还要注意 IE10 支持 3D 转换，但是它缺少对一个关键属性的支持，这个关键属性会影响你如何处理元素——我们将在本系列的后面发现更多。

## 文档流

理解转换元素对文档流没有影响是很重要的。无论如何，未转换的盒子所需要的空间将保持不变。即使缩小某个元素使其不可见或将其移出屏幕，其原始空间仍会保留。

[**查看翻译转换演示页面……**](https://blogs.sitepointstatic.com/examples/tech/css3-transformations/translate.html)

希望你的胃口已经被激起了！在第二篇文章中，我们将研究常见的 2D 变换函数。

## 分享这篇文章