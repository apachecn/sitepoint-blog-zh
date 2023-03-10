# 高级 CSS3 动画效果

> 原文：<https://www.sitepoint.com/advanced-css3-animations/>

我们现在已经介绍了 CSS3 动画的大部分属性，但是还有一个很少开发人员知道的属性…

## 暂停和重新启动动画

属性可以暂停或恢复动画。它接受:

*   `running` —默认；动画正常播放
*   `paused` —动画暂停

通常，您可以应用`animation-play-state: paused`来响应用户输入，例如，当鼠标悬停在动画元素上或聚焦在动画元素上时:

```
#myelement:hover, #myelement:focus
{
	animation-play-state: paused;
}
```

一旦鼠标光标/焦点移到别处，动画就从它停止的地方继续。

考虑一下我在 CSS3 中的[星球大战滚动文本。这只是一点乐趣，但一些用户会发现很难以滚动的速度阅读文本。对放映员大喊大叫会让你被赶出电影院，但至少我们可以在用户悬停在文本上时暂停浏览器的滚动:](/css3-starwars-scrolling-text/)

```
#titlecontent:hover
{
	-webkit-animation-play-state: paused;
	animation-play-state: paused;
}
```

[**查看暂停和重启的星球大战片头……**](https://blogs.sitepointstatic.com/examples/tech/css3-animations/starwars.html)

不需要 JavaScript，在创建这段代码的过程中也没有伤害到伊渥克人(尽管我确实打中了一只 womp 鼠和一个冈根人)。

## 多个动画

像过渡一样，您可以将多个动画应用到同一个元素。这些是并发播放的——它们不像在 jQuery 或其他 JavaScript 库中那样排队。不同的动画值由逗号分隔，请记住，如果您忽略它们，这些值将会循环。例如:

```
#myelement
{
	animation-name: colorchange, sizechange;
	animation-duration: 5s, 2s;
	animation-timing-function: linear, ease;
	animation-delay: 0s, 5s;
	animation-iteration-count: infinite;
	animation-direction: alternate;
}
```

这个:

1.  使用线性计时功能运行交替的“颜色变化”动画五秒钟，并立即开始，以及
2.  使用减缓计时功能运行交替的“大小变化”动画两秒钟，但在五秒钟后开始。

[**查看多个动画演示……**](https://blogs.sitepointstatic.com/examples/tech/css3-animations/example2.html)

你会注意到，在调整大小开始前的五秒钟，颜色发生了变化(我还在悬停时添加了`animation-play-state: paused`)。是的，它很丑——我相信你能想出更好的例子！

## 分享这篇文章