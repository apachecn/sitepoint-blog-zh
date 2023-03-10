# 如何在 HTML5 画布上绘制二次曲线

> 原文：<https://www.sitepoint.com/html5-canvas-draw-quadratic-curves/>

在浏览器中绘制图形曲线从来都不容易。直到最近，如果您想要一个平滑的图表，您要么生成一个图像，要么在服务器上创建一个 SVG。快速创建一条曲线需要忍者 JavaScript 技能、数学学位和极大的耐心。

画布元素改变了一切。我们现在可以用几行代码画出复杂的线条、曲线和形状，并制作出动画。今天我们来看看二次曲线。

## 什么是二次曲线？

很久没上数学课了，请不要期待深入讲解！如果你感兴趣的话，可以看看 WolframMathWorld 网站上的偏头痛诱发方程

*已经回来了？*

![quadratic curve](img/ace84470d1ac0479096cda7c8890ce5b.png)

像任何直线一样，二次曲线有起点(P <sub>0</sub> )和终点(P <sub>2</sub> )。它有一个单独的控制点(P <sub>1</sub> )来决定线条的曲率。这张来自维基百科的[图片提供了一个很好的曲线生成图。](http://en.wikipedia.org/wiki/B%C3%A9zier_curve)

二次曲线非常适合绘制平滑的边缘。正如您从这张图片中看到的，很容易指定一个控制点，在那里方形边缘通常会出现。

## 数学够了——让我们看看代码吧！

canvas 元素支持二次曲线。首先，我们需要一点初始化来获取画布上下文并设置默认的线宽和颜色:

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

quadraticCurveTo()方法现在可用于绘制二次曲线。它需要四个参数:

*   控制点的 x 坐标(P <sub>1</sub>
*   控制点的 y 坐标(P <sub>1</sub>
*   终点的 x 坐标(P <sub>2</sub>
*   终点的 y 坐标(P <sub>2</sub>

最后，我们调用 stroke()方法来完成我们的曲线:

```
 ctx.quadraticCurveTo(250, 100, 400, 250);
ctx.stroke(); 
```

你已经做得很好了，所以我创建了一个 [**交互式画布二次曲线演示页面**](https://blogs.sitepointstatic.com/examples/tech/canvas-curves/quadratic-curve.html) *(抱歉，在 IE8 及以下版本中不起作用)*。拖动控制点或线条的任意一端，它将为您生成代码。

在我的下一篇文章中，我们将使用贝塞尔曲线创建一些更有趣的形状…

## 分享这篇文章