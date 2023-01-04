# HTML5 响应式设计:如何在 JavaScript 中确定图像的真实尺寸

> 原文：<https://www.sitepoint.com/html5-responsive-design-image-dimensions/>

响应式设计是过去几年中出现的最有用的网络技术之一。网站布局可以适应设备的屏幕，无论使用什么分辨率，看起来都很漂亮。

响应图像通常没有宽度和高度属性，例如

```
 <img id="myimage" src="myimage.jpg" alt="my image" /> 
```

图像会自己调整大小，我们可以添加一点 CSS 来确保图像不会超过它的实际尺寸。

```
 #myimage
{
	max-width: 100%;
} 
```

经常需要在 JavaScript 中操作这些缩放的图像；也许是为了游戏、灯箱或动画效果。在这些情况下，您需要确定图像的实际高度和宽度。然而，`width`和`height`属性返回当前(调整后的)尺寸:

```
 var myimage = document.getElementById("myimage");
var w = myimage.width;	// current width, e.g. 400px
var h = myimage.height;	// current height, e.g. 300px 
```

幸运的是，现代 HTML5 浏览器提供了另外两个属性:

```
 var rw = myimage.naturalWidth;	// real image width
var rh = myimage.naturalHeight;	// real image height 
```

IE9、Firefox、Chrome、Safari 和 Opera 都支持这些属性。请注意，在尺寸可用之前，必须完全下载图像。为了确保它准备好了，你可以在窗口或图像本身附加一个“load”事件，或者在测试尺寸之前检查图像的`.complete`属性。

## IE6、7 和 8

旧版本的 Internet Explorer 不支持`.naturalWidth`和`.naturalHeight`。我们仍然可以通过将图像加载到内存对象中并检查标准的`width`和`height`属性来确定实际尺寸，例如

```
 var myimage = document.getElementById("myimage");

if (typeof myimage.naturalWidth == "undefined") {
	// IE 6/7/8
	var i = new Image();
	i.src = myimage.src;
	var rw = i.width;
	var rh = i.height;
}
else {
	// HTML5 browsers
	var rw = myimage.naturalWidth;
	var rh = myimage.naturalHeight;
} 
```

我不断惊奇地发现这些新的属性和方法；它们让我们的生活变得更加轻松。

## 分享这篇文章