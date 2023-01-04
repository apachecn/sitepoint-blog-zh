# 用 CSS 和 JavaScript 创建动画谷歌地图标记

> 原文：<https://www.sitepoint.com/animated-google-map-markers-css-javascript/>

![Animated Google Maps markers](img/eb2416bdc50d051ec98030bc5df49794.png)

原图: [DeviantArt](http://img07.deviantart.net/e7aa/i/2006/214/2/0/cheshire_cat_by_halo8.jpg)

Google Maps API 允许网络开发者通过他们神奇的内置功能，只用几行代码就能创造出出色的用户体验。

然而，有一个明显的例外:地图标记的灵活性和创造性。

当然，您可以添加一个自定义标记图像、一个[工具提示](https://developers.google.com/maps/documentation/javascript/examples/infowindow-simple)和一个标签。但这些都是静态的、面向文本的交互方式，在有很多点的地图上可能会变得令人不知所措。没有标准的方法来创建响应用户动作的交互式标记。

我只是对此不满意，所以我开始寻找一种方法来创建真正独特的地图。我将向您展示如何在您的地图标记上包含 CSS3 动画，以便您可以让它们跳舞、扭动和隐藏，直到它们几乎跳出屏幕。

如果用户悬停在一个标记上，点击它或使用地图外的切换，您将能够使用任何 CSS 动画来赋予它生命。本指南将重点介绍一个简单的策略，您可以使用它在任何项目中包含动画标记。(作为对比，另外两个例子——瑞安·康诺利和 T2【菲利佩·菲格罗亚——使用了类似的方法。)

这是一个动画标记工作的基本例子。著名的[柴郡猫](https://en.wikipedia.org/wiki/Cheshire_Cat)在马萨诸塞州充当三个独立点的标记，你可以使用右上角的开关来改变他的动画:

在 [CodePen](http://codepen.io) 上用 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [CSS 谷歌地图标记](http://codepen.io/SitePoint/pen/xVaLBM/)。