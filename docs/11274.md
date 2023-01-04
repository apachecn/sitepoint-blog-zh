# 《星球大战》让 CSS3 动画和变形变得有意义

> 原文：<https://www.sitepoint.com/star-wars-makes-css3-animations-and-transformations-make-sense/>

作为一个 JavaScript 爱好者，我总是对 CSS 动画的整体概念感到不舒服。这是一个有趣的实验，但对我来说，JavaScript 是你的动画应该出现的地方。直到我发现安东尼·卡尔扎迪拉的《T2》这部 AT-AT 沃克动画。

不幸的是，你需要一个基于 WebKit 的浏览器(例如 Safari 或 Chrome)才能看到它。所以，当然这是一个 WebKit 专有的 CSS 扩展——CSS3[动画](https://www.w3.org/TR/css3-animations/)和[转换](https://www.w3.org/TR/css3-transitions/)模块目前正在工作草案中——但实验总是很有趣。这个实验让我开始思考。

但首先，它是如何做到的？动画的每个部分都是独立的；即每条腿的脚、胫骨和大腿。CSS 然后被用来定义动画。这是右前方 D 腿上的脚的动画定义:

```
#atat #leg-d .leg-foot {
	-webkit-animation-name: foot-d;
	-webkit-animation-duration: 7s;
	-webkit-animation-iteration-count: infinite;
	-webkit-transform-origin: 50% 0;
}
```

它有一个名称、一个持续时间(动画持续的时间)、一个迭代次数(动画运行的次数——在本例中是无限的)和一个原点位置。下一个难题是关键帧的信息:

```
@-webkit-keyframes foot-d {
  0% {
    -webkit-transform: rotate(0deg);
  }
  10% {
    -webkit-transform: rotate(-20deg);
  }
  30% {
    -webkit-transform: rotate(0deg);
  }
  100% {
    -webkit-transform: rotate(0deg);
  }
}
```

使用`@-webkit-keyframe` at-rule，您可以定义在动画持续时间的特定阶段应用的样式规则。这里，我们在动画开始时(0%)、10%时、30%时以及结束时(100%)应用规则。在本例中，`-webkit-transform`声明用于应用旋转，尽管可以应用任何 CSS 属性。

通过应用不同程度的旋转，对于动画序列中不同时间的不同动画部分，这种错觉几乎就像木偶。

在`@-webkit-keyframe`块中也可以使用另一种语法；您可以像这样指定`to`和`from`的值:

```
@-webkit-keyframes fade-in {
  from {
    color: #fff
  }
  to {
    color: #000
  }
}
```

你怎么想呢?对我来说，这看起来像是描述动画的一个非常简单的语法。但你知道什么才是真正酷的吗？如果编写了一个 jQuery 插件，可以读取 CSS3 动画语法并执行动画，使其跨浏览器兼容。

现在那会很棒。

你可以在 WebKit 博客上阅读更多关于 CSS3 动画的内容。

## 分享这篇文章