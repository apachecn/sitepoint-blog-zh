# 如何在 HTML5 画布上绘制贝塞尔曲线

> 原文：<https://www.sitepoint.com/html5-canvas-draw-bezier-curves/>

在我的上一篇文章中，我们使用几行代码在 HTML5 canvas 元素上创建了二次曲线。如果你检查了[二次曲线演示页面](https://blogs.sitepointstatic.com/examples/tech/canvas-curves/quadratic-curve.html)上的 JavaScript，你可能会注意到几处提到了 béziers——它的更弯曲的表亲。

## 什么是贝塞尔曲线？

你可能见过在桌面出版和图形软件包中使用的贝塞尔曲线。所以，让我们再去一趟狼数学世界，看看这些方程式。

我相信你明白，但我觉得有点恶心。

![bezier curve](img/e583dadce15b4279928b2c30573f4e28.png)

在[最后一篇](https://www.sitepoint.com/html5-canvas-draw-quadratic-curves/)中，我们看到了二次曲线如何有一个起点(P <sub>0</sub> )、终点(P <sub>2</sub> )和一个决定曲率的单一控制点(P <sub>1</sub> )。在画布的情况下，贝塞尔曲线有一个起点(P <sub>0</sub> )、一个终点(P <sub>3</sub> )和两个控制点——线的每一端一个控制点(P <sub>1</sub> 和 P <sub>2</sub> )。再次感谢[维基百科](http://en.wikipedia.org/wiki/B%C3%A9zier_curve)提供的精彩插图。

贝齐尔对形状的控制要大得多。因为曲线的初始部分遵循其控制路径，所以我们可以生成反转方向或自动换行的曲线。它们非常适合于创建类似 Visio 的流程图，在该流程图中，您需要用连接线连接两个形状。

## 继续编码！

像以前一样，我们需要几行 JavaScript 来获取画布上下文并设置默认宽度和颜色:

```
 canvas = document.getElementById("canvas");
ctx = canvas.getContext("2d")
ctx.lineWidth = 6;
ctx.strokeStyle = "#333"; 
```

我们现在定义曲线的起点(P <sub>0</sub> ):

```
 ctx.beginPath();
ctx.moveTo(100, 250); 
```

然后使用 bezierCurveTo()方法绘制曲线。它需要六个参数:

*   曲线开始控制点的 x 坐标(P <sub>1</sub>
*   曲线开始控制点的 y 坐标(P <sub>1</sub>
*   曲线终点控制点的 x 坐标(P <sub>2</sub>
*   曲线终点控制点的 y 坐标(P <sub>2</sub>
*   终点的 x 坐标(P <sub>3</sub>
*   终点的 y 坐标(P <sub>3</sub>

最后，我们调用 stroke()方法来完成我们的曲线:

```
 ctx.bezierCurveTo(150, 100, 350, 100, 400, 250);
ctx.stroke(); 
```

[**交互式画布贝塞尔曲线演示页面**](https://blogs.sitepointstatic.com/examples/tech/canvas-curves/bezier-curve.html) 允许你拖动所有点并生成代码*(抱歉 IE 粉丝——你需要等待 IE9)* 。

我希望这个系列对你有用。你在任何有趣的项目中使用画布曲线吗？

## 分享这篇文章