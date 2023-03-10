# CSS3 过渡:贝塞尔计时函数

> 原文：<https://www.sitepoint.com/css3-transitions-cubic-bezier-timing-function/>

在本系列的第二部分[中，我们查看了 CSS3 **过渡-计时-功能**属性，该属性控制动画在整个过渡期间的速度变化。它接受诸如`ease`、`ease-in`和`linear`这样的关键字值，通常对于要求最苛刻的 CSS 开发人员来说已经足够了。](/css3-transition-properties/)

但是，您可以使用`cubic-bezier`值定义自己的计时功能。这听起来和看起来都很复杂，但可以用一些简单的图表来解释。

![linear transition timing functions](img/afac683439e28b7ef3585169075ea3b9.png)

上图描绘了动画完成的百分比与时间的关系。这条线是`linear`，因此，实际上，动画完成的比例与时间匹配，例如，50%的动画在持续时间的中途完成。

![ease-in-out transition timing functions](img/96989921d1b18a323417060a37101ba7.png)

该图显示了`ease-in-out`曲线:

*   开始很慢；大约 12%的动画是在前 25%的时间内完成的。
*   它慢慢结束；动画的最后 12%发生在最后 25%的时间里。
*   因此，中间 76%的动画必须在 50%的时间内发生；会更快。

从本质上讲，曲线切线越陡，此时动画出现的速度就越快。如果直线是垂直的，动画将在该点上是瞬时的。下图演示了这一点:

![steep transition timing function](img/668f3c61f3d58e8eaa15019fef3a378d.png)

持续时间进行到一半时，动画将从大约 30%完成跳到 70%完成。

我们可以想象所有的转场动画都是从 0，0 点开始；零时间后，动画完成了 0%(在其开始状态下)。同样，它们将在点 1，1 处结束；在持续时间结束时，动画 100%完成(在其结束状态)。

因此，我们可以定义一条介于 0，0 和 1，1 之间的贝塞尔曲线。

## 什么是贝塞尔曲线？

你会看到在图形软件包中使用的贝塞尔曲线。给定一条直线的起点(P <sub>0</sub> 和终点(P <sub>3</sub> ，贝塞尔曲线为每一端定义两个控制点(P <sub>1</sub> 和 P <sub>2</sub> )。我甚至不会开始解释数学，但是，如果你感兴趣，去[维基百科](http://en.wikipedia.org/wiki/B%C3%A9zier_curve)或 [WolframMathWorld](http://mathworld.wolfram.com/BezierCurve.html) 看看令人反胃的方程式。

幸运的是，我们不需要担心复杂性。因为我们的动画线从 0，0 开始，到 1，1 结束，我们只需要在`cubic-bezier`值中定义点 P <sub>1</sub> 和 P <sub>2</sub> ，例如

```
/* cubic-bezier(p1x, p1y, p2x, p2y) */

/* identical to linear */
transition-timing-function: cubic-bezier(0.25,0.25,0.75,0.75);

/* identical to ease-in-out */
transition-timing-function: cubic-bezier(0.420, 0.000, 0.580, 1.000);
```

注意，P <sub>1</sub> 和 P <sub>2</sub> 的 x 坐标表示时间，必须在 0 和 1 之间(包括 0 和 1)。您不能设置负值，因为它会在触发之前开始！同样，你不能设置一个大于 1 的值，因为时间不能前进到，比如说，120%，然后返回到 100%(除非你手头有一个 TARDIS 或通量电容器)。

然而，y 坐标表示动画完成，并且可以是小于零或大于一的任何值，例如

```
transition-timing-function: cubic-bezier(0.5, -0.5, 0.5, 1.5);
```

![bounce effect transition timing functions](img/e3c67e38ca3ef38a241c76e26b5e6765.png)

在持续时间的大约 15%时，动画完成了-10%!因此，如果我们将一个元素从 0px 移动到 100px，那么它在那个时候将是-10px。换句话说，我们有一个反弹效应；前往[cubic-bezier.com](http://cubic-bezier.com/#.5,-0.5,.5,1.5)并点击 GO 观看它的运行。

## 让工具来完成工作

定义贝塞尔曲线可能涉及跟踪和误差，以达到您想要的效果。幸运的是，有许多很好的工具可以帮助您试验并生成正确的代码:

*   [cubic-bezier.com](http://cubic-bezier.com/)
*   [停止](http://matthewlein.com/ceaser/)

在本系列的最后一部分，我们将研究一些高级的转换技术。

## 分享这篇文章