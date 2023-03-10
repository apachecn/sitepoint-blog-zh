# CSS3 动画和 JavaScript API

> 原文：<https://www.sitepoint.com/css3-animations-javascript-api/>

我之前在 2012 年 6 月写了关于 CSS3 动画事件的文章，但是浏览器已经向前发展了，作为本系列的一部分重新讨论这个主题是有意义的。

虽然 CSS3 动画提供了合理的控制，但通常需要添加交互或定义一个接一个播放的关键帧集的特定序列。例如，你可以在 CSS3 中实现[龙穴](http://en.wikipedia.org/wiki/Dragons_lair)；响应于用户的键盘或操纵杆移动，播放动画。

## 事件类型

您可以在动画元素上捕捉三种类型的事件:

*   **animationstart** —当动画第一次开始时触发
*   **动画迭代** —在每次迭代开始时触发(除了第一次)
*   **animationend** —当动画完成时触发(假设它曾经完成)。

## 跨浏览器兼容性

Firefox、IE10 和 Opera 12 支持这三种事件类型。

不幸的是，Chrome、Safari 和 Opera 15+仍然需要一个 webkit 前缀，并加上 camelCasing，让你的生活变得更困难一些: **webkitAnimationStart** 、 **webkitAnimationIteration** 和 **webkitAnimationEnd** 。

因此，您当前必须为两组浏览器定义事件侦听器:

```
// animated element
var anim = document.getElementById("myelement");

// webkit events
anim.addEventListener("webkitAnimationStart", AnimationListener);
anim.addEventListener("webkitAnimationIteration", AnimationListener);
anim.addEventListener("webkitAnimationEnd", AnimationListener);

// standard events
anim.addEventListener("animationstart", AnimationListener);
anim.addEventListener("animationiteration", AnimationListener);
anim.addEventListener("animationend", AnimationListener);
```

## 事件对象

在上面的代码中，每当发生动画事件时，都会调用 AnimationListener 函数。事件对象作为单个参数传递。除了标准属性和方法，它还提供:

*   **animation name**:CSS3 动画@关键帧名称
*   **elapsedTime** :动画开始后的时间，以秒为单位。

相同的属性名称在所有浏览器中都可用。

因此，我们可以做出相应的反应，例如，当“flash”@关键帧动画在#myelement:

```
function AnimationListener(e) {
	var
		t = e.target,
		name = e.animationName,
		end = (e.type.toLowerCase().indexOf("animationend") >= 0);

	if (end && name == "flash" && t.id == "myelement") {
		// do something
	}
}
```

[**查看 CSS3 动画事件演示……**](https://blogs.sitepointstatic.com/examples/tech/css3-animations/animation-api.html)

演示页面显示一个按钮。单击时，会切换“启用”类，启动名为“flash”的动画@关键帧。当动画事件触发时，动画名称、类型和运行时间会显示在控制台中。动画结束后,“enable”类将从按钮中移除，以便可以再次单击它。

如果你已经阅读了这个系列，你应该知道关于 CSS3 过渡和动画的一切。请在下面的评论中发布你的演示链接。

## 分享这篇文章