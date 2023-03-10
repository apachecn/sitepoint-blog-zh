# 如何在 SVG 中创建复杂的路径

> 原文：<https://www.sitepoint.com/svg-path-element/>

**我们已经看了[什么是 SVGs】和](https://www.sitepoint.com/svg-101-what-is-svg/)[基本绘图元素](https://www.sitepoint.com/svg-drawing-basics/)。但是大多数图像超越了简单的直线、矩形和椭圆，这就是知道如何在 SVG 中创建复杂路径变得重要的时候。**

SVG [`path`元素](https://developer.mozilla.org/Web/SVG/Element/path)要高级得多。可以说它是你唯一需要的元素，因为它可以画出你想要的任何形状。然而，有一个缺点，最好用一个例子来说明:

```
<path d="M500,500 L500,20 A480,480 0 0,1 968,607 z" /> 
```

它看起来很复杂，很难理解。

path 元素需要一个单独的`d`属性，其字符串值包含编码的绘图指令列表。它没有正则表达式复杂，但很容易迷失在数字丛林中。

绘图字符串由一系列由大写或小写字母标识的命令组成。该字母后跟零个或多个适用于该特定命令的参数。例如，以下路径使用一个`M`命令将 *SVG 笔*移动到起点 500，500，而不画线:

```
<path d="M500,500" /> 
```

SVG `viewBox`属性影响画笔在图像本身中的位置。使用上面的`500,500`示例，笔应该是:

*   当 SVG 使用`viewBox="0 0 1000 1000"`时，水平和垂直居中
*   `viewBox="500 500 1000 1000"`时位于左上方
*   `viewBox="-500 -500 1000 1000"`时位于右下方
*   `viewBox="0 0 400 400"`时在可视区域外。

类似地，小写的`m`相对于笔的当前位置移动笔，例如，`50`向左移动单位，`100`向上移动单位:

```
<path d="m50,-100" /> 
```

画线类似:`L`相对于绝对坐标画线，`l`(小写`L`)相对于当前位置画线。因此，以下路径是相同的:

```
<path d="M500,500 L100,600" />
<path d="M500,500 l-400,100" /> 
```

`H`和`h`画水平线到相应的绝对或相对`x`位置。例如:

```
<path d="M500,500 H800" />
<path d="M500,500 h300" /> 
```

如果你能猜出`V`和`v`是做什么的，就能加分…

```
<path d="M500,500 V400" />
<path d="M500,500 v-100" /> 
```

最后，`Z`或`z`从当前点到起点画一条线来封闭路径。通常，`Z`会是你的字符串中的最后一个命令，尽管可以用多个子路径创建字符串。例如:

```
<path d="M500,500 L500,200 L800,500 z M400,600 L400,900 L100,600 z" /> 
```

结果如下图。

![SVG paths](img/17664ba38dcb28d62f02df6819d710f2.png)

SVG 路径可以使用 CSS 的[样式，也可以根据需要添加`stroke`、`stroke-width`、`fill`等属性。](https://www.sitepoint.com/svg-styling-with-css/)

这是最简单的选择。它现在变得复杂得令人麻木。考虑`A` / `a`命令，该命令使用以下参数绘制椭圆的弧(截面):

```
A rx ry x-axis-rotation large-arc-flag sweep-flag x y 
```

在本例中:

*   `rx`是椭圆的 x 半径
*   `ry`是椭圆的 y 半径
*   `x-axis-rotation`是椭圆旋转的度数
*   `large-arc-flag`是最小的圆弧`0`还是最大的圆弧`1`
*   `sweep-flag`是正角度的`0`还是负角度的`1`(在线上或线下)
*   `x y`是圆弧的终点

弧线很难可视化，但是这个 [Codepen 弧线工具](https://codepen.io/lingtalfi/pen/yaLWJG)会有所帮助。

但是，弧线的用途有限，因此 SVG 路径还提供:

*   [三次贝塞尔曲线](https://www.sitepoint.com/html5-svg-cubic-curves/)，以及
*   [二次贝塞尔曲线](https://www.sitepoint.com/html5-svg-quadratic-curves/)。

## 分享这篇文章