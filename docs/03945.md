# 使用 ZingChart 的角度指令快速制图

> 原文：<https://www.sitepoint.com/rapid-charting-zingcharts-angular-directive/>

*本文由[辛查特](http://www.zingchart.com/?utm_source=sitepoint-angular-article&utm_medium=article&utm_campaign=sitepoint-angular)赞助。感谢您对使 SitePoint 成为可能的赞助商的支持。*

比方说，作为一名前端开发人员，您遇到了一个图表或仪表板，您希望将其可视化地包含在项目中，但是您没有时间做大量繁重的工作并推出自己的图表解决方案。

这种假设的情况对于像 Angular 这样的前端 JavaScript 框架来说是一个很好的例子，因为它允许您用最少的代码量构建一个快速的“生产就绪”的演示。尽管如此，你可能还是会不知所措，尤其是如果你不是角度专家的话。即使使用 Angular 这样的原型工具，构建交互式图表或仪表板也是一项耗时的挑战。谢天谢地，有了 [ZingChart](http://www.zingchart.com/features/javascript-charts/?utm_source=sitepoint-angular-article&utm_medium=article&utm_campaign=sitepoint-angular) Angular 指令，使得用 HTML5 和 JavaScript 构建复杂的数据 Vizzes 成为一项简单易行的任务。

## 入门指南

为了演示使用 Angular 和 [ZingChart](http://www.zingchart.com/blog/2015/03/05/zingchart-angularjs/?utm_source=sitepoint-angular-article&utm_medium=article&utm_campaign=sitepoint-angular%20) 有多简单，我们将使用来自【2015 年 SitePoint 调查最佳 PHP 框架的一些信息创建一个交互式数据集。

但在此之前，让我们快速浏览一个基本的演示，展示如何用 Angular 和 ZingChart 指令建立一个线图。

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看到笔 [Angular + ZingChart](http://codepen.io/SitePoint/pen/gpmYed/) 。