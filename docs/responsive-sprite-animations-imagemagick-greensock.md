# 带有 ImageMagick 和 GreenSock 的响应式精灵动画

> 原文：<https://www.sitepoint.com/responsive-sprite-animations-imagemagick-greensock/>

CSS 精灵并不新鲜。自从 2004 年在一个列表上被推广以来，不起眼的精灵已经成为许多网络开发者工具箱中的主要技术。但是，尽管精灵提供的速度优势已经很好理解，但它们在现代网络动画中的使用却很少被讨论。原理是一样的:将多个图像组合成一个“主”图形，一次只显示选定的部分。

在本文中，我们将探索一种简单的方法来创建响应式 CSS sprite 动画，这种动画是轻量级的，移动友好的，甚至是交互式的。你不需要任何特殊的图形软件，只需要一点 CSS 和 JavaScript 知识。让我们开始吧。

## 打造精灵

精灵起源于电子游戏世界，所以我们将向他们的传统致敬，并将《街头霸王》中的 [Ryu 制成动画。不用说，所有作品都属于](https://en.wikipedia.org/wiki/Ryu_%28Street_Fighter%29)[卡普空](http://www.capcom.com/)。

我们需要将动画中的每一帧组合成一幅图像。有许多工具可以帮助创建精灵，其中许多甚至会为您生成一个附带的样式表。Compass 内置的 spriting 功能非常强大，而 [SpritePad](http://wearekiss.com/spritepad) 是一个基于网络的生成器的好例子。然而，对于我们的目的，一个简单的命令行方法就可以了。

图像处理的“瑞士军刀”ImageMagick 是一款免费的开源图像处理工具，非常适合将可能变得费力的任务自动化，例如合并图像。ImageMagick 也适用于几乎所有的操作系统。Mac 用户可以通过[家酿](http://brew.sh/)安装，Windows 追随者可以从官网下载一个可执行的安装程序，Linux 爱好者大概不需要我解释什么。

将大小相同的动画帧作为 png 序列保存在文件夹中。打开命令行终端，导航(`cd`)到保存图像的目录，并执行以下命令:

```
convert *.png -append result-sprite.png
```

该命令指示 ImageMagick 垂直连接所有。PNG 文件(因为' * '本质上是一个通配符)合并成一个名为“result-sprite.png”的完整 sprite。这是我们将作为 CSS 中的`background-image`应用到我们想要制作动画的元素的图形，改变它的位置以依次循环每个帧。

## 纯 CSS 动画

我们将简单地开始，用 CSS3 关键帧动画制作我们的精灵。如果你不熟悉基本语法， [Louis Lazaris 关于 Smashing Magazine](http://www.smashingmagazine.com/2011/05/17/an-introduction-to-css3-keyframe-animations/) 的文章是一个很好的介绍。我们希望从 0%到 100%(从上到下)制作元素的垂直`background-position`动画，依次暴露每一帧。

按照默认行为，让动画在一个连续的序列中运行，会暴露我们的精灵的中间帧，破坏幻觉。然而，通过使用`steps()`功能，我们可以控制渲染关键帧的数量。由于定义为百分比时`background-position`的工作方式[，我们需要将步骤数设置为*比动画中的总帧数少一个*。](https://css-tricks.com/i-like-how-percentage-background-position-works/)

尝试下面的代码笔来了解这个想法:

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [MYRKmJ](http://codepen.io/SitePoint/pen/MYRKmJ/) 。