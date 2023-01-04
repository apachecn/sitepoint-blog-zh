# CSS 动画硬件加速介绍

> 原文：<https://www.sitepoint.com/introduction-to-hardware-acceleration-css-animations/>

在过去的几年里，我们经常听说硬件加速，以及它如何帮助改善网页上的动画，使它们即使在移动浏览器上也很漂亮和流畅。但我认为很多经验不太丰富的开发人员不知道硬件加速是如何工作的，以及我们如何正确地使用它来使我们的动画发光。

这个术语本身听起来过于复杂，接近高等数学。在本文中，我将阐明这个主题，并演示如何在您的前端项目中利用这种技术。

## 我为什么要在乎？

让我们看一个简单的动画示例，它包含几个堆叠在一起的球(也就是说，在 z 轴上，所以它看起来像一个球)。目标是用动画移动这组球。最简单的方法是调整`left`和`top`属性。我们可以用 JavaScript 来实现，但是我们将使用 CSS 动画来代替。请注意，我排除了任何供应商前缀，但您应该使用类似于 [Autoprefixer](https://github.com/postcss/autoprefixer) 的东西来确保完全兼容。

```
.ball-running {
  animation: run-around 4s infinite;
}

@keyframes run-around {
  0%: {
    top: 0;
    left: 0;
  }

  25% {
    top: 0;
    left: 200px;
  }

  50% {
    top: 200px;
    left: 200px;
  }

  75% {
    top: 200px;
    left: 0;
  }
}
```

下面是一个现场演示，它使用一个按钮通过 JavaScript 启动动画:

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看 Pen [动画制作顶部/左侧属性为](http://codepen.io/SitePoint/pen/WQVxQQ/)的重叠球。