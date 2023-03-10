# CSS3 动画属性基础

> 原文：<https://www.sitepoint.com/css3-animation-properties/>

在本系列关于 CSS3 动画的第二部分中，我们将详细了解如何定义关键帧和元素属性。

## 定义关键帧

在[上一课](/css3-animations-101/)中，我们讨论了关键帧；动画中指定已知状态的点。让我们创建一组简单的关键帧，其中`background-color`从蓝色变为红色，绿色变为紫色，然后又变回蓝色。首先，我们需要在样式表的某个地方定义一个`@keyframes`规则(在哪里并不重要):

```
@keyframes colorchange
{
	/* individual keyframes go here */
}
```

我给这个集合起了个名字叫“颜色变化”。您可以使用任何名称；保持描述性，这样你可以很容易地识别它。

接下来，我们在动画的整个持续时间内，在百分点处定义单个关键帧:

```
@keyframes colorchange
{
	0%   { background-color: #00F; /* from: blue */ }
	25%  { background-color: #F00; /* red        */ }
	50%  { background-color: #0F0; /* green      */ }
	75%  { background-color: #F0F; /* purple     */ }
	100% { background-color: #00F; /* to: blue   */ }
}
```

虽然我们只定义了`background-color`，但是您可以列出任意数量的 CSS 属性(假设可以将它们制作成动画)。

0%是起始关键帧，您也可以使用`from`关键词。100%是结束关键帧，您也可以使用`to`关键字。通常情况下，您应该至少编码两个关键帧，尽管如果元素的默认状态是蓝色，您可能会错过开始和结束，例如

```
@keyframes colorchange
{
	25%  { background-color: #F00; /* red        */ }
	50%  { background-color: #0F0; /* green      */ }
	75%  { background-color: #F0F; /* purple     */ }
}
```

因此，您可以在 0%和 100%之间定义至少 101 个关键帧。实际上，你可以用百分比分数来定义更多，比如 12.3%，但是，如果你的动画如此复杂，你就是在自找麻烦！

最后，对于 Chrome、Safari 和 Opera 15+，您(目前)需要使用 webkit 前缀重复整个块:

```
@-webkit-keyframes colorchange
{
	0%   { background-color: #00F; /* from: blue */ }
	25%  { background-color: #F00; /* red        */ }
	50%  { background-color: #0F0; /* green      */ }
	75%  { background-color: #F0F; /* purple     */ }
	100% { background-color: #00F; /* to: blue   */ }
}
```

这通常应该放在无前缀的代码上面。

## 将动画应用于元素

现在，您可以将关键帧应用到任意数量的元素，以查看它们的运行情况。下面的部分描述了标准属性，但是记住也要包括带-webkit 前缀的版本。

## 动画名称

`animation-name`属性定义了您想要应用哪组已命名的动画关键帧，例如使用我们的“颜色变化”关键帧:

```
#myelement
{
	animation-name: colorchange;
}
```

## 动画-时长

与`transition-duration`一样，`animation-duration`以秒(s)或毫秒(ms)为单位指定动画周期，也就是千分之一秒。以下持续时间是相同的:

```
#myelement p.one
{
	animation-duration: 3s;
}

#myelement p.two
{
	animation-duration: 3000ms;
}
```

## 动画-计时-功能

与`transition-timing-function`一样，`animation-timing-function`决定了动画在关键帧之间的速度**如何变化——而不是贯穿整个动画(也就是说，除非您正在移动元素，否则很难察觉)。有许多可能的值:**

*   **缓和** —默认；动画开始缓慢，然后快速加速。在中间点，它以相同的速度减速并减速到停止。
*   **缓入缓出** —类似于缓出，但加速和减速更微妙。
*   **缓入** —缓慢启动，但突然加速并停止。
*   **减速** —快速启动，但减速至停止。
*   **线性** —动画全程匀速；通常最适合用于颜色或不透明度的变化。

最后，还有`cubic-bezier`可以让你定义自己的计时功能。参考 [CSS3 过渡:贝塞尔计时函数](/css3-transitions-cubic-bezier-timing-function/)获得深入描述。

## 动画延迟

与`transition-delay`一样，`animation-delay`指定动画开始前的时间，单位为秒或毫秒。以下是相同的:

```
#myelement p.one
{
	animation-delay: 0.5s;
}

#myelement p.two
{
	animation-delay: 500ms;
}
```

## 动画-迭代-计数

`animation-iteration-count`指定动画播放的次数。默认值为 1，但您可以设置任何正整数值(零或负整数永远不会播放动画)。或者，您可以使用`infinite`关键字来永远播放动画。

```
#myelement p.one
{
	animation-iteration-count: 5;
}

#myelement p.two
{
	animation-iteration-count: infinite;
}
```

## 动画方向

`animation-direction`决定动画应该如何重复。可以使用以下关键字:

*   `normal` —默认；从 0%到 100%向前播放动画关键帧
*   `reverse` —从 100%到 0%向后播放动画关键帧
*   `alternate` —向前播放动画，然后反向重复播放。
*   `alternate-reverse` —与`alternate`类似，只是先播放反向动画。

## CSS 速记符号

让我们来看一个完整的动画声明:

```
#myelement
{
	animation-name: colorchange;
	animation-duration: 5s;
	animation-timing-function: linear;
	animation-delay: 1s;
	animation-iteration-count: infinite;
	animation-direction: alternate;
}
```

这需要大量的输入，尤其是考虑到-webkit 前缀版本。同样，您可以使用一个简单的`animation`快捷键按照上面显示的顺序定义所有六个值。

```
#myelement
{
	-webkit-animation: colorchange 5s linear 1s infinite alternate;
	animation: colorchange 5s linear 1s infinite alternate;
}
```

[**查看结果……**](https://blogs.sitepointstatic.com/examples/tech/css3-animations/example.html)

这是一个开始，但是试着把它改编成你自己的动画。

## 分享这篇文章