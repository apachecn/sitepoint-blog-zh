# 如何在 CSS3 中创建一个响应性的 CSS 居中图像

> 原文：<https://www.sitepoint.com/css3-responsive-centered-image/>

图片库灯箱已经存在很多年了。在您尝试加载比视窗尺寸更大的图像之前，它们通常会提供很好的用户体验。开发人员经常忘记这个重要的检查，呈现给你的是一个很难去掉的居中图像。这个问题在移动设备上可能会加剧。

直到最近，图像灯箱需要实现几个方程来确定视口和图像尺寸，然后相应地调整大小和居中。幸运的是，我们现在可以依靠 CSS3 媒体查询和转换来完成这项艰巨的工作。

[**查看演示……**](http://cssdeck.com/labs/responsive-image)

## HTML

这里没什么可看的—我们有一个`img`，我们将分配一个“ri”(响应图像)类:

```
<img src="https://lorempixel.com/600/450/" class="ri" />
```

请记住删除任何高度和宽度属性。

## 后备 CSS

IE6/7/8 不理解媒体查询或转换，所以我们的图像会出现在错误的位置。有一些垫片和专有属性可以解决这个问题，但是在我看来，它们通常会带来更多的问题，比如页面重量增加、性能下降、维护困难等等。

因此，下面的代码为传统浏览器提供了一个合理的回退，应该可以在大多数横向桌面屏幕上工作:

```
img.ri
{
	position: absolute;
	max-width: 80%;
	top: 10%;
	left: 10%;
	border-radius: 3px;
	box-shadow: 0 3px 6px rgba(0,0,0,0.9);
}
```

`border-radius`和`box-shadow`也不会被老 ie 理解，但是会优雅地降级。

## 定位图像

为了用 CSS 使图像居中，我们将它的左上角移动到视口的中心。为了将它移回到真正的中心，我们使用了一个适当的变换(对于较旧的浏览器使用前缀):

```
img.ri:empty
{
	top: 50%;
	left: 50%;
	-webkit-transform: translate(-50%, -50%);
	-moz-transform: translate(-50%, -50%);
	-ms-transform: translate(-50%, -50%);
	-o-transform: translate(-50%, -50%);
	transform: translate(-50%, -50%);
}
```

注意选择器:`img.ri:empty` — `empty`是一个结构化的伪类，它只匹配没有子元素的元素(一个图像不应该有任何子元素)。这是一个 CSS3 选择器，因此 IE8 及以下版本不会解析该声明。我们可以使用一个替代方法，比如条件注释，但是这似乎是一个更有效的解决方案，并且只需要六个额外的字符。

## 使图像具有响应性

我们的图像必须响应视窗尺寸，以确保它不会与边缘重叠:

*   如果视口的高度大于宽度，则必须定义最大宽度，并且
*   如果视口的宽度大于高度，则必须定义最大高度。

我们可以使用媒体查询定向属性来为我们完成这项艰巨的工作:

```
@media screen and (orientation: portrait) {
  img.ri { max-width: 90%; }
}

@media screen and (orientation: landscape) {
  img.ri { max-height: 90%; }
}
```

简单——所有这些都不需要任何复杂的 Java 编写。 [**查看演示……**](http://cssdeck.com/labs/responsive-image)

请使用代码，因为你喜欢自己的项目。它可以作为你自己的现代轻型灯箱的基础。除非你想让我写一个并提供一个方便的教程？请将您的请求转发给 [@craigbuckler](http://twitter.com/craigbuckler) 。

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如[Jump Start Responsive Web Design](https://learnable.com/books/jump-start-responsive-web-design?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)。

对本文的评论已经关闭。对 CSS3 有疑问？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?53-CSS-amp-Page-Layout?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章