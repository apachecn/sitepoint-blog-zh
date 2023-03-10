# 带有 Sass 的 HSB 颜色

> 原文：<https://www.sitepoint.com/hsb-colors-with-sass/>

你可能会问，为什么我们需要另一种颜色模型？这是一个公平的问题，考虑到我们已经有了 RGB、HSL 和 hex，我们做得很好。为了说服你，我会告诉你为什么 HSB 很棒，以及如何在任何 Sass 项目中轻松实现它。

## 为什么是 HSB？

HSB/HSV 颜色模型实际上是 HSL 的近亲。HSB 代表色调、饱和度和亮度，色调分量等于 HSL 中的分量。

HSB 颜色，就像它们的表亲 HSL 一样，比 RGB 和 Hex 有一些很好的优势——它们允许你通过查看颜色的饱和度和亮度来快速识别颜色， [Chris Coyier 也告诉我们为什么 HSL 更酷，](http://css-tricks.com/yay-for-hsla/)和 [Dudley Storey 有一个关于如何考虑 HSL 颜色的很好的视觉指南](http://demosthenes.info/blog/61/An-Easy-Guide-To-HSL-Color-In-CSS3)。所有这些优点同样适用于 HSB。

此外，Karen Menezes 写了一篇关于如何考虑色彩修改的有用文章，其中有很大一部分是关于 HSL 模型的。HSL 甚至有自己的[母亲网站](http://mothereffinghsl.com/)。

这种易于理解的因素在 RGB 和 hex 中不存在——试着想象一下`rgb(173, 149, 128)`或`#ad9580`是什么样子。

此外，如果你需要某种程度的透明度，十六进制不再是一个选项；你要在 RGBa 和 HSLa(现在还有 HSBa)之间做出选择。

如果您从图形软件中选择/获取颜色，HSB 颜色也比 HSL 颜色更容易使用。像 [GIMP](http://www.gimp.org/) 或者 Adobe 的 Photoshop 和 Illustrator 这样的程序都更喜欢 HSB 颜色模型而不是 HSL。

有了这个基础，让我们看看如何在 Sass 项目中使用 HSB。

## 构建功能

在项目中添加对 HSB 颜色的支持很容易。我们将定义一个 HSB 函数，以便以后我们可以像使用 HSL 或 RGB 颜色一样使用它:

```
color: hsb(333, 84, 76)
```

这个函数将接受 3 或 4 个参数，第四个是 alpha 通道，我们将默认设置为 1(完全不透明):

```
@function hsb($h-hsb, $s-hsb, $b-hsb, $a: 1)
```

在内部，该函数会将 HSB 颜色转换为 HSL(从数学角度来说，这是最简单的转换)。无论您以何种格式提供输入颜色，Sass 都会以其十六进制等效值(或 RGBa，如果颜色具有透明度)输出颜色。其他预处理程序的工作方式相同。

色调组件不需要转换，因为它对于 HSB 和 HSL 是相同的。为了得到其他组件，我们需要一些数学知识。

用一个简单的等式就可以推导出与 HSB 颜色相当的 HSL 亮度:

```
$l-hsl: ($b-hsb/2) * (2 - ($s-hsb/100));
```

这是我们计算 HSL 饱和度的方法:

```
$s-hsl: ($b-hsb * $s-hsb) / if($l-hsl < 50, $l-hsl * 2, 200 - $l-hsl * 2);
```

`if`声明是针对饱和度的适当行为，一旦 HSL 的亮度超过 50%,饱和度就开始降低。[下面是](http://en.wikipedia.org/wiki/Comparison_of_color_models_in_computer_graphics#HSL)更详细的解释。

最后，我们简单地返回新值:

```
@return hsla($h-hsb, $s-hsl, $l-hsl, $a);
```

好了，现在我们有了所有的三个部分:色调、饱和度和亮度。但是我们的代码还不是完美的。如果第三个参数(亮度或`$b-hsb`)是`0`，我们的函数将失败，因为我们不能被零除。令我们高兴的是，在 HSB 中有一个`0`的亮度意味着输出颜色将总是黑色，不管其他两个参数是什么。

因此，解决这个问题的最佳方法是在我们的函数中添加一个条件，如果亮度为零，则自动输出黑色:

```
@function hsb($h-hsb, $s-hsb, $b-hsb, $a: 1) {
 @if $b-hsb == 0 {
 @return hsla(0, 0, 0, $a)
  } @else {
 $l-hsl: ($b-hsb/2) * (2 - ($s-hsb/100));
 $s-hsl: ($b-hsb * $s-hsb) / if($l-hsl < 50, $l-hsl * 2, 200 - $l-hsl * 2);
 @return hsla($h-hsb, $s-hsl, $l-hsl, $a);
  }
}
```

我们结束了。查看包含一些测试案例的演示:

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的带 Sass 的 Pen [HSB Color 函数。](http://codepen.io/SitePoint/pen/fpqDg/)