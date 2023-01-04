# CSS 渐变、变换、动画和遮罩

> 原文：<https://www.sitepoint.com/css-gradients-transforms-animations-and-masks/>

**CSS 工作组似乎在[默默无闻地](https://www.w3.org/blog/CSS)努力确定可能永远无法在现实世界的浏览器中实现的规范的确切措辞，而 [WebKit](http://webkit.org/) 团队正带头通过实现新的 CSS 功能来推动网络向前发展，几个月后你就可以在生产中使用这些功能。**

每个浏览器都包含一个渲染引擎，负责根据构成给定网页的 HTML 和 CSS 代码生成渲染页面。WebKit 是苹果 Safari 浏览器的核心渲染引擎(更不用说最近的诺基亚手机和 Adobe AIR 平台了)。您可以[下载](http://nightly.webkit.org/)WebKit 的最新进展版本来试用。

对 CSS 的未来感兴趣的网页设计者现在有一个很好的理由下载 WebKit。该团队引入了一些令人震惊的新 CSS 功能，苹果计划在今年 6 月的 iPhone 和 iPod touch 移动 Safari 2.0 更新中发布。据推测，我们可以期待 Mac OS X 版和 Windows 版 Safari 的桌面版同时更新。

![](img/fc47030c2319beff3b2c49033532500c.png)

以下是迄今为止发布的功能及其作用:

## CSS 渐变

CSS 渐变允许您用颜色到颜色的渐变填充区域(包括背景、边框和生成的内容),而不仅仅是纯色。

右边的图片展示了一些你可以实现的效果的例子。在上面的图像中，四个径向(圆形)渐变被组合在一个背景中，以产生一种多彩的效果。在底部的图像中，一条线性渐变从蓝色开始垂直延伸，然后渐变到白色，再跳到绿色，最后又渐变到白色。

为了产生渐变，您可以在 CSS 代码中任何可能包含图像的地方使用`-webkit-gradient` CSS 函数。下面是线性的蓝色和绿色渐变背景是如何产生的:

```
background: -webkit-gradient(linear, left top, left bottom,
    from(#00abeb), to(#fff), color-stop(0.5, #fff),
    color-stop(0.5, #66cc00));
```

在 WebKit 博客上可以找到这个新特性的完整描述和更多例子。当然，你需要下载 WebKit 来查看它们。

## CSS 转换

CSS 转换的工作方式很像 CSS 相对定位(所有主流浏览器都支持)，因为它允许您在不影响页面布局的情况下修改页面的一部分。使用相对定位，如果您将元素向左移动 500 个像素，该元素将显示在新位置，但它仍将占据页面中它原来所在的空间。

使用 CSS 变换，您可以像使用相对定位一样移动元素，但也可以缩放、旋转甚至倾斜元素。

在这个截图中，我使用 CSS 变换将 sitepoint.com 的整个页面旋转了 2 度。特别值得注意的是，在应用转换后，所有文本仍然是可选的，您仍然可以使用页面顶部的搜索表单。

![](img/11ca4dde4f8bac0efef6849645b8709a.png)

实现这一点的代码很简单:

```
body {
  -webkit-transform: rotate(-2deg);
}
```

如果您下载 WebKit，您可以在任何页面上自己尝试，只需在地址栏中键入以下代码(全部在一行中):

```
javascript:_=document.body.style;_.WebkitTransformOrigin='100%%200%';_.WebkitTransform='rotate(-2deg)'
```

WebKit 博客对这一特性有一个很好的[总结](http://webkit.org/blog/130/css-transforms/)，团队也发布了一个最初的[草案规范](http://webkit.org/specs/CSSVisualEffects/CSSTransforms.html)供 W3C 考虑。

## CSS 过渡

一旦你开始使用 CSS，你很快就会知道当用户用鼠标悬停在超链接上时，如何改变超链接的外观。例如，此代码会将链接的文本从蓝色更改为白色:

```
a:link, a:visited {
  color: #00f;
}
a:hover {
  color: #fff;
}
```

使用一点 JavaScript，您可以随时进行这样的更改，只需更改分配给元素的类。

CSS 转场让你平滑地动画这些变化。链接文本的颜色不是在悬停时从蓝色突然变为白色，而是可以从蓝色逐渐变为浅蓝色，最后变为白色:

```
a:link, a:visited {
  color: #00f;
  -webkit-transition: color 1s linear;
}
a:hover {
  color: #fff;
}
```

您可以将过渡与 CSS 变换结合起来，以实现一些真正令人兴奋的效果。在这个截图中，我修改了 sitepoint.com 的主导航栏，当鼠标悬停在主导航栏上时，我会放大并轻微旋转主导航栏按钮。通过 CSS 转换，当鼠标经过按钮时，按钮会平滑地增长和倾斜，然后当鼠标离开时，按钮会收缩回原位。

![](img/34e7edffa38ad34c965bbacd5db00304.png)

除了看起来非常光滑，这种效果还有一个额外的好处，就是让链接更容易点击。

WebKit 团队已经提交了一份 CSS 转换的[草案规范](http://webkit.org/specs/CSSVisualEffects/CSSTransitions.html)供 W3C 考虑，WebKit 博客有一篇很好的[帖子，里面有一些例子](http://webkit.org/blog/138/css-animation/)，你可以在 WebKit 中尝试。

## CSS 遮罩

![](img/71bc9a1443d6d8673259ccba16ffb422.png)

今天刚刚宣布，WebKit 现在也支持 [CSS 掩码](http://webkit.org/blog/181/css-masks/)。如果你曾经使用过像 Photoshop 这样的图形程序，你会对蒙版的概念很熟悉。本质上，遮罩可以让 HTML 元素的一部分变得半透明，或者完全不可见。

在 WebKit 博客右边的例子中，一个带有黑色边框的红色圆圈的 SVG 图像作为蒙版应用于一张照片。请注意，由于有黑色边框，照片的全部颜色是如何透过蒙版沿圆圈边缘显示的。

标准的 CSS `opacity`属性实际上是一个应用于整个元素的实心遮罩。使用 CSS 遮罩，您可以应用图像、SVG 矢量形状，甚至 CSS 渐变作为遮罩来实现不同的效果。和所有 WebKit 的新 CSS 效果一样，遮罩惊人地强大。您可以将它们应用于任何复杂性的 HTML 元素，包括表单域甚至视频！

## 当今 CSS 的未来

多年来，网页设计者一直在寻找 W3C 规范来告诉他们对未来浏览器的期望。然而，随着 W3C 在这一领域的努力日渐式微，Safari、Opera 和 Firefox 等前沿浏览器已经通过实现新功能让设计人员今天尝试来填补空白。

与任何新功能一样，这些新功能在现实世界中的实用性还没有定论。特别是在动画过渡的情况下，滥用的可能性非常大。对于我们在未来看到的每一个微妙和令人愉悦的效果，我们可能会看到五个或十个[这些](http://tech.no.logi.es/woodshop/3d.html)(需要 WebKit 来查看效果)。

虽然我们确实不能指望上述任何一项功能在短期内就能在大多数浏览器中实现，但几个月后就会有一个现实世界的实现。这超过了 W3C 多年来制定的大多数 CSS3 规范。

为了跟上这些和其他新的 CSS 特性进入 WebKit 的步伐(如 [CSS 变量](http://disruptive-innovations.com/zoo/cssvariables/)、 [CSS 画布绘制](http://webkit.org/blog/176/css-canvas-drawing/)和 [CSS 关键帧动画](http://webkit.org/specs/CSSVisualEffects/CSSAnimation.html))，请访问 WebKit 团队的 [Surfin' Safari 博客](http://webkit.org/blog/)。

## 分享这篇文章