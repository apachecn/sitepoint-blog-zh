# 无需 JavaScript 即可创建强大的 CSS 动画效果

> 原文：<https://www.sitepoint.com/css-animation-no-javascript/>

在这篇文章中，我们将探索仅使用 CSS 的网络动画世界。我们将首先使用 SVG 和 CSS 的组合创建一个简单的动画。然后我们将只用 HTML 和 CSS 创建另外两个动画。我们还将讨论何时需要调用 JavaScript 以及 CSS 的局限性。

到本文结束时，您将能够使用 CSS 创建有利于使用 JavaScript 的动画。您还将能够确定何时需要使用 JavaScript 来创建动画。

为了从这篇文章中获得最大的收获，我们假设你至少掌握了 CSS 和 HTML 的核心知识。如果你刚刚开始 web 开发，有一些很好的学习和获得帮助的资源，比如 sitePoint 上的[学习资源，非常有用的](https://www.sitepoint.com/premium/library) [SitePoint 论坛](https://www.sitepoint.com/community/)，还有 [freecodecamp](https://www.freecodecamp.org) 。

## 绘图效果动画

这是一部令人印象深刻的动画，看似容易制作。下面是我们的目标截图。

![final animation](img/aeabc4c3ba35c13be86576e1c58b33e8.png)

我们首先需要为我们的徽标创建 SVG:

```
<svg xmlns="http://www.w3.org/2000/svg" width="279.15" height="343.95" overflow="visible" stroke="#000" stroke-width="1">
 <path d="M110.57 248.64c-22.7-21.25-45.06-42.09-67.31-63.06-11.73-11.06-23.32-22.26-34.87-33.51C-2.6 141.35-2.86 128 8.02 117.42 47.67 78.82 87.46 40.35 127.21 1.84c.46-.44 1.03-.77 2.47-1.84 12.52 13.66 25.06 27.34 37.1 40.47-4.44 4.76-10.06 11.31-16.21 17.33-22.69 22.2-45.56 44.22-68.34 66.32-7.89 7.66-7.97 13.48.11 21.07 19.38 18.19 38.85 36.29 58.37 54.33 7.53 6.96 7.75 12.42.32 19.64-10.01 9.72-20.05 19.4-30.46 29.48z"/>
 <path d="M150.02 343.95c-13.41-13.03-26.71-25.97-40.2-39.08 1.23-1.32 2.19-2.44 3.24-3.46 27.8-26.95 55.61-53.89 83.42-80.83 8.32-8.05 8.41-13.92-.01-21.79-19.54-18.27-39.14-36.47-58.77-54.63-6.52-6.04-6.76-12.11-.37-18.33 10.24-9.96 20.52-19.87 31.15-30.16 6.33 5.89 12.53 11.58 18.65 17.37 27.53 26.03 55.07 52.05 82.52 78.16 12.57 11.96 12.66 24.78.33 36.75-38.99 37.85-78.04 75.64-117.07 113.45-.82.79-1.71 1.51-2.89 2.55z"/>
</svg> 
```

这里有一个设置了 SVG 的[笔](https://codepen.io/SitePoint/pen/ZEBEEbw)。请随意选择，这样您就可以按照下面的步骤进行操作。

有了我们的 SVG，我们现在将`fill-opacity`设置为`0`。我们稍后将对此进行动画演示:

```
svg {
  fill-opacity: 0;
} 
```

([更新笔](https://codepen.io/SitePoint/pen/xxRxxOb))

我们现在可以专注于绘制 SVG 了。我们将通过使用 CSS `stroke-dashoffset`和`stroke-dasharray`属性来实现这一点。

`stroke-dasharray`属性控制用于使笔划成为路径的间隙和虚线的模式。例如，将`stroke-dasharray: 10`应用到 SVG 中的路径会创建一个虚线效果，其笔画和间隙长度为 10 个像素:

参见 [CodePen](https://codepen.io) 上 site point([@ site point](https://codepen.io/SitePoint))
的笔 [Stroke-dasharray 演示](https://codepen.io/SitePoint/pen/XWNWWNv)。