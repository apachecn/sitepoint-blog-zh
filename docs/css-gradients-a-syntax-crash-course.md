# CSS 渐变:语法速成班

> 原文：<https://www.sitepoint.com/css-gradients-a-syntax-crash-course/>

在过去，大多数网站大量使用图片来创建一个漂亮的用户界面。由于各种 CSS 属性，这种趋势已经改变。本教程将帮助你了解 CSS 渐变。您可以使用渐变来替换各种 UI 元素以及背景中的图像。只要稍加练习，你就可以不用一张图片就能创造出复杂的图案。

CSS 渐变拥有[出色的浏览器支持](http://caniuse.com/#feat=css-gradients)，允许你在两种或多种指定颜色之间创建平滑的视觉过渡。渐变可以让你控制很多设置，比如渐变的大小，角度，颜色停止的位置等等。

在这篇文章中，我将涵盖线性，径向，以及较新的重复梯度。

## 线性渐变

[线性渐变](http://dev.w3.org/csswg/css-images-3/#linear-gradients)是最常用的渐变。如下所示，括号中的值表示值的类型:

```
.example {
  background: linear-gradient(
    [direction], [first-color], [second-color], [more-colors ...]
  );
}
```

如果不指定方向，渐变将从顶部开始，以第一种颜色的最大强度开始，然后在到达底部时平滑过渡到最后一种颜色。

为了更好地控制，您可以指定渐变的方向。你可以通过使用简单的术语如`to left`、`to bottom right`来实现，或者你可以指定角度。下面是创建从左到右的背景的代码片段:

```
.example {
  background: linear-gradient(to right, hotpink, lightpink);
}
```

通过 [CodePen](http://codepen.io) 上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看到钢笔[从左到右的线性渐变](http://codepen.io/SitePoint/pen/KpoJOV/)。