# 如何在 HTML5 页面中使用鼠标滚轮事件

> 原文：<https://www.sitepoint.com/html5-javascript-mouse-wheel/>

支持鼠标滚轮可以进一步增加 HTML5 网页的交互性。您可以执行不同的操作，如放大或缩小，而不是滚动页面。

[**查看鼠标滚轮演示页面……**](https://blogs.sitepointstatic.com/examples/tech/mouse-wheel/index.html)

大多数浏览器支持任何元素的“mousewheel”事件。您可以注册一个传递给事件对象的处理函数。这公开了一个`wheelDelta`属性；正值表示向上滚动，负值表示向下滚动。值越大或越小，移动越大。

不幸的是，有一个浏览器不支持“鼠标滚轮”事件。你错了:*是火狐*。Mozilla 实现了一个“DOMMouseScroll”事件。这传递了一个带有`detail`属性的事件对象，但是与`wheelDelta`不同，它为向下滚动返回一个正值。所以，在 Mozilla 采用这个事件之前，我们有一个奇怪的情况，为 IE6 编码实际上更容易一点！这可不是你每天都能听到的话。

你还应该注意到，苹果在 Safari 中禁用了滚轮，但是 webkit 引擎提供了支持，所以你的代码不会引起任何问题。

## 添加鼠标滚轮事件处理程序

让我们在网页上添加一个图像，它将随着鼠标滚轮的移动而放大和缩小:

```
 <img id="myimage" src="myimage.jpg" alt="my image" /> 
```

我们现在可以附加鼠标滚轮事件处理程序:

```
 var myimage = document.getElementById("myimage");
if (myimage.addEventListener) {
	// IE9, Chrome, Safari, Opera
	myimage.addEventListener("mousewheel", MouseWheelHandler, false);
	// Firefox
	myimage.addEventListener("DOMMouseScroll", MouseWheelHandler, false);
}
// IE 6/7/8
else myimage.attachEvent("onmousewheel", MouseWheelHandler); 
```

## 跨浏览器鼠标滚轮事件处理函数

我们的 MouseWheelHandler 现在可以确定滚轮移动增量。在这种情况下，我们将反转 Firefox 的`detail`值，返回 1 表示向上，返回-1 表示向下:

```
 function MouseWheelHandler(e) {

	// cross-browser wheel delta
	var e = window.event || e; // old IE support
	var delta = Math.max(-1, Math.min(1, (e.wheelDelta || -e.detail))); 
```

我们现在可以相应地调整图像的大小。下面的代码将宽度设置在 50px 和 800px 之间，但是您可以[确定图像的自然尺寸](https://www.sitepoint.com/html5-responsive-design-image-dimensions/)并使用它。

```
 myimage.style.width = Math.max(50, Math.min(800, myimage.width + (30 * delta))) + "px";

	return false;
} 
```

最后，我们返回 false 来取消通常会滚动页面的标准事件。

[**查看鼠标滚轮演示页面……**](https://blogs.sitepointstatic.com/examples/tech/mouse-wheel/index.html)

这些代码可以在任何浏览器中运行，包括 IE6、7 和 8，但是 Safari 用户不会看到任何事情发生。

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如真实世界的 [HTML5 & CSS3。](https://learnable.com/courses/html5-css3-for-the-real-world-1484?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)

对这篇文章的评论已经关闭。对 HTML 有疑问？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?52-(X)HTML?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章