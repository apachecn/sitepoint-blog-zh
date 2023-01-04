# 动画关键帧捕获

> 原文：<https://www.sitepoint.com/animation-keyframe-gotcha/>

我是 CSS3 动画的忠实粉丝。尽管开始时是试探性的，并且有些人担心它们是否侵犯了 JavaScript 的行为责任，但 CSS3 动画得到了广泛的支持，易于使用，并且可以实现流畅的效果，如果几年前没有 Flash，这是不可能的。

不幸的是，并不是所有的 CSS3 花园都是美好的，浏览器供应商经常在你的土地上喷洒肥料。考虑以下发光元素动画:

```
div
{
	width: 8em;
	font-weight: bold;
	text-align: center;
	padding: 0.5em 0;
	margin: 50px auto;
	color: #fff;
	background-color: #00c;
	border-radius: 3px;
	animation: glow 3s ease-in-out infinite;
}

@keyframes glow {
	50%  { box-shadow: 0 0 20px #aaf; }
}
```

[查看演示页面……
![animation](img/a31541c618a611b42889ef39ddb90db8.png)](http://cssdeck.com/labs/css-animation-defaults) 

*(注意:-目前 Chrome、Safari 和 Opera 15+需要动画和关键帧属性的 webkit 前缀，但上面的代码中没有显示。它们已被添加到演示页面。)*

该动画在最新版本的 Firefox、Chrome、Safari 和 Opera 中运行良好，但在 IE10 或 IE11 中则不然。

在你发起一场*“IE6 又来了”*的抨击之前，让我们仔细看看 [W3C CSS3 动画工作草案文档](https://www.w3.org/TR/css3-animations/)。在[第四节。关键帧](https://www.w3.org/TR/css3-animations/#keyframes)你会发现以下语句:

> **问题 1** 需要描述如果一个属性没有出现在所有关键帧中会发生什么。

我们只在 50%定义了一个关键帧，但是没有明确定义 0%和 100%的属性。我怀疑 Webkit/Blink 和 Gecko 浏览器默认为`box-shadow: 0px 0px 0px rgba(0,0,0,0);`。然而，IE 的三叉戟引擎似乎默认为`box-shadow: none;`——而且不可能从*《一无所有》*动画到*《有事》*。

严格来说，没有一个浏览器是在做正确的假设，因为规范是不完整的。也许 Webkit/Blink 和 Gecko 团队做出了更有用的选择，但这并不意味着它是正确的。还没有。

也就是说，如果在`div`选择器中定义了`box-shadow: 0px 0px 0px rgba(0,0,0,0);`，你会希望 IE 启动动画。该规范规定:

> 如果未指定“0%”或“from”关键帧，则用户代理将使用正在设置动画的属性的计算值构建一个“0%”关键帧。如果未指定“100%”或“到”关键帧，则用户代理会使用正在制作动画的属性的计算值来构造一个“100%”关键帧。

公平地说，CSS3 动画规范正在发展，所以发现实现差异并不奇怪。幸运的是，您可以通过遵循一些简单的规则来避免跨浏览器动画不一致:

1.  始终包括从 0% /到 100% /的关键帧。也不要忘记“%”符号——与其他属性不同，0 和 0%是不等价的。
2.  始终定义每个关键帧内的每个动画属性，0%和 100%尤其重要。
3.  经常用各种浏览器测试！

为了确保发光动画在任何地方都能工作，我们必须更改关键帧定义:

```
@keyframes glow
{
	0%   { box-shadow: 0 0 0px rgba(0,0,0,0); }
	50%  { box-shadow: 0 0 20px #aaf; }
	100% { box-shadow: 0 0 0px rgba(0,0,0,0); }
}
```

没有任何保证，但是不管应用程序或 CSS3 动画规范有任何更新，这段代码都应该继续工作。

这个问题让你困惑了吗？你找到解决办法了吗？还是放弃了，怪浏览器？！

## 分享这篇文章