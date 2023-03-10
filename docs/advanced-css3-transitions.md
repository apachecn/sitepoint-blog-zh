# 高级 CSS3 过渡效果

> 原文：<https://www.sitepoint.com/advanced-css3-transitions/>

在本系列关于 CSS3 过渡的最后一部分，我们将研究几个更高级的过渡主题。

## 可选的反向转换

在我们前面的例子中，我们应用了一个单一的转换属性，当从开始状态改变到结束(悬停)状态以及从结束状态反转到开始状态时使用，例如

```
p#animate
{
	color: #ff6;
	transition: all 3s ease-in-out 0.5s;
}

p#animate:hover
{
	color: #0f0;
	transform: scale(4);
}
```

*(注意-webkit 前缀没有显示，但是当前需要用于过渡和转换。)*

在本例中，当您将鼠标悬停在该元素上至少 0.5 秒时，它将增长到其大小的 4 倍，并在三秒内改变颜色。当您将光标从元素上移开时，它将再等待 0.5 秒，然后也在三秒钟后返回到其开始状态。

但是如果我们想让元素立即回复原状呢？幸运的是，我们可以应用两种不同的转换；一个用于开始和结束状态之间的动画，一个用于结束和开始状态之间的反向动画:

```
p#animate
{
	color: #ff6;
	transition: all 0.5s ease-in-out;
}

p#animate:hover
{
	color: #0f0;
	transform: scale(4);
	transition: all 3s ease-in-out 0.5s;
}
```

[**查看过渡倒车演示页面……**](https://blogs.sitepointstatic.com/examples/tech/css3-transitions/reversing.html)

这说明了一个元素缓慢增长到结束状态，但很快返回到开始状态。您应该注意到,`transition`属性被应用到它要移动到的状态。因此，我们的:悬停(结束)状态持续时间为三秒，而正常(开始)状态持续时间为 0.5 秒。

## 多重过渡

单个 CSS 属性可以应用不同的过渡效果。最好用一个例子来说明这一点:

```
p#animate
{
	width: 10em;
	background-color: #F00;
	border-radius: 5px;
	transition-property: width, border-radius, background-color;
	transition-duration: 1s, 2s, 5s;
	transition-timing-function:  ease, ease-out, linear;
}

p#animate:hover
{
	width: 20em;
	background-color: #00F;
	border-radius: 50%;
}
```

[**查看多重过渡效果演示页面……**](https://blogs.sitepointstatic.com/examples/tech/css3-transitions/multiple.html)

实质上，当我们将鼠标悬停在元素上时，我们已经定义了三个独立的转换:

1.  使用 ease timing 功能，`width`在 1 秒内从 10em 加倍到 20em
2.  使用放松计时功能，`border-radius`在 2 秒内从 5px 变为 50%,以及
3.  使用线性计时功能，`background-color`在 5 秒内从红色变为蓝色

您会注意到，在其他动画完成至少三秒钟后，背景颜色会继续变化。

值循环，如果您省略它们，例如

```
p#animate
{
	transition-property: width, border-radius, background-color;
	transition-duration: 1s, 2s;
	transition-timing-function:  ease;
}
```

1.  使用缓时功能，`width`动画播放时间超过 1 秒
2.  使用缓时功能，`border-radius`动画播放时间超过 2 秒，并且
3.  使用缓时功能，`background-color`会播放超过 1 秒的动画

如果您已经阅读了这个系列，那么您现在已经了解了关于 CSS3 转换的几乎所有内容。希望你能设计出比我在这里展示的例子更好的例子！

## 分享这篇文章