# 初学者的绿袜子(第二部分):GSAP 的时间表

> 原文：<https://www.sitepoint.com/greensock-beginners-part-2-gsaps-timeline/>

![Animating the DOM with GreenSock (GSAP) Part 2](img/c333f7f711bcff0f2460987ccc496267.png)

GreenSock 第二部分的目的是向初学者介绍 GreenSock 的 [TimelineMax](https://greensock.com/docs/TimelineMax) 。你将会学到:

*   为什么你需要时间表
*   如何在时间轴中包含多个补间动画
*   如何将多个时间轴打包到函数中，并将它们嵌套在一个主时间轴中，以获得更大的灵活性。

在本教程结束时，您将能够熟练地使用 GreenSock 的时间轴来操纵和完全控制多个补间动画。

要了解 GreenSock 的基础知识，包括如何使用 TweenMax 对简单动画进行排序和交错，请阅读这篇由多个部分组成的文章的第 1 部分。

GreenSock 文章是 CSS:动态 DOM 动画库系列的一部分。以下是我在以前的文章中提到的内容:

*   [*用 Anime.js 制作 DOM 动画*](https://www.sitepoint.com/animating-the-dom-with-anime-js/) 讲述了如何最好地利用网络上的动画，以及何时可以考虑使用 JavaScript 动画库来代替仅使用 CSS 的动画。然后重点介绍 Anime.js，这是一个免费的轻量级 JavaScript 动画库
*   [KUTE.js 的有趣动画效果](https://www.sitepoint.com/fun-web-animation-effects-with-kute-js/)向您介绍 kute . js，这是一个免费且功能丰富的 JavaScript 动画库
*   [使用 Velocity.js(无 jQuery)使您的网站具有交互性和趣味性](https://www.sitepoint.com/how-to-use-velocity-js-without-jquery/)向您展示如何使用 Velocity.js(一个开源、健壮的免费动画库)来创建高性能的 web 动画
*   [GreenSock for 初学者:网络动画教程(第 1 部分)](https://www.sitepoint.com/web-animation-tutorial-part-1)是 GreenSock 的概述，也称为 GSAP(green sock Animation Platform)，在这里我讨论了库的模块和许可模型。我还向您展示了如何使用 GSAP TweenMax 编写一个简单的补间、补间序列和交错动画。

## 为什么你需要格林斯托克的时间线来编写你的网页动画？

在第 1 部分中，您了解了如何通过创建许多独立的补间动画并使用每个补间动画的`delay`属性来协调它们的时序，从而向一个元素或多个元素添加不同的动画。

默认情况下，一个接一个堆叠补间会导致所有补间同时发生。

然而，更有帮助的是能够控制一个补间相对于其他补间的触发时间，例如，在同一时间，1 秒或半秒之前或之后，等等。

以这个只有两个补间动画的基本例子为例。事情是这样的:

*   补间 1:一个圆形形状随着它在 X 轴和 Y 轴上的旋转而增大和缩小
*   Tween 2:弹出一些文本。

让它工作的 GSAP 片段看起来像这样:

```
// scale down the text 
// and hide it before the animation begins 
TweenMax.set('.example__title', { 
  scale: 0.2, 
  autoAlpha: 0
});

// scale the circle shape down before 
// the animation begins
TweenMax.set('.example__ball', {
  scale: 0.2
});

// tween 1
TweenMax.to('.example__ball', 0.5, {
  rotationX: 360,
  rotationY: 180,
  scale: 1,
  ease: Elastic.easeIn.config(2, 1)
});

// tween 2
TweenMax.to('.example__title', 0.5, {
  autoAlpha: 1,
  scale: 1,
  ease: Back.easeOut.config(4)
});
```

如您所见，两个补间同时发生，这不是想要的效果:

参见钢笔 [GSAP 教程第二部分:为什么在](https://codepen.io/SitePoint/pen/qjzyqB/) [CodePen](https://codepen.io) 上用 SitePoint ( [@SitePoint](https://codepen.io/SitePoint) )制作时间线。