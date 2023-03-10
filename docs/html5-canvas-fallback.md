# 具有回退功能的跨浏览器 HTML5 画布

> 原文：<https://www.sitepoint.com/html5-canvas-fallback/>

Canvas 是一种相对较新的 HTML5 技术，它提供了一种“可脚本化”的图像。您可以向页面添加一个`<canvas>`元素，并使用 JavaScript 命令在它的表面上绘制。它非常快，可以用于动作游戏和动画图表——参见" [3 个伟大的 JavaScript &画布示例](https://www.sitepoint.com/3-great-javascript-canvas-games-examples/)"

可以使用以下代码将 canvas 标记放置在 HTML5 页面上:

```
 <canvas id="mycanvas" width="300" height="300">
Sorry, your browser cannot display this image.
</canvas> 
```

假设支持 canvas，可以用 JavaScript 直接在画布上画图；例如，图像中心有一个半径为 100 像素的黑色圆圈:

```
 var canvas = document.getElementById("mycanvas");
var cxt = canvas.getContext("2d");
cxt.arc(150,150,100,0,Math.PI*2,true);
cxt.fill(); 
```

这很好，但是对于使用没有支持的浏览器的人来说，这不是一个愉快的体验。

有[几个项目](http://code.google.com/p/explorercanvas/)在 Internet Explorer 8.0 及以下版本实现了 canvas 支持，但是无法修复其他浏览器。我们可以退回到文本(如图所示)或`img`，但这需要额外的标记，如果浏览器支持 canvas 但禁用了 JavaScript，它就不会出现。在这种情况下，用户会看到一个空框。

为了让每个人都开心，我们将构建一个页面，向[展示一个简单的画布雨滴动画](https://blogs.sitepointstatic.com/examples/tech/canvas/canvas.html)。如果你在英国，它会让你想起一个辉煌的英国夏天。当用户的浏览器不支持`<canvas>`或 JavaScript 时，会出现一个静态图像。

通过代码，我们的 HTML5 `head`包含一个声明 canvas 元素的小脚本。这不会给页面添加任何内容；这是 IE8 和更低版本的一个变通方法，允许我们将 CSS 样式应用到一个`canvas`标签，即使浏览器不支持 HTML5(或者，我们可以使用 [HTML5 JavaScript shiv](https://www.sitepoint.com/5-reasons-why-you-can-use-html5-today/) ):

```
 <!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<title>Canvas Example</title>
<script>
document.createElement("canvas");
</script> 
```

我们现在可以为我们的`canvas`标签定义 CSS 样式了。当页面加载时，元素被赋予一个每个人都能看到的背景图像(rain.jpg)。#mycanvas.active 声明删除了这一背景，但只有当我们的脚本成功运行时，它才会激活:

```
 <style>
#mycanvas
{
	float: left;
	width: 300px;
	height: 300px;
	margin: 0 20px 0 0;
	background-image: url(rain.jpg);
	border: 1px solid #000;
}

#mycanvas.active
{
	background-image: none;
}
</style> 
```

我们现在可以在页面上放置一个`canvas`标签。不需要提供回退文本，因为当不支持静态背景图像时，用户会看到它:

```
 </head>
<body>

<h1>HTML Canvas Example with Image Fall Back</h1>

<canvas id="mycanvas" width="300" height="300"></canvas> 
```

我们现在准备添加一些 JavaScript 魔法——假设用户启用了脚本。前几行检查是否支持`canvas`，并对元素应用“active”类以移除静态背景:

```
 <script>
var canvas = document.getElementById("mycanvas");
if (canvas.getContext) {

	// canvas supported
	canvas.className = "active"; 
```

我们的雨滴动画代码现在可以执行:

```
 // start animation
	var cxt = canvas.getContext("2d");
	cxt.fillStyle = "rgba(255,255,255,0.5)";

	setInterval(function() {
		var	x = Math.round(Math.random()*canvas.width),
			y = Math.round(Math.random()*canvas.height),
			e = 20 + Math.round(Math.random()*30),
			s = 0;

		(function() {
			s++;
			if (s <= e) {
				setTimeout(arguments.callee,s);
				var c = 255-(e-s)*3;
				cxt.strokeStyle = "rgb("+c+","+c+","+c+")";
				cxt.beginPath();
				cxt.arc(x,y,s,0,Math.PI*2,true);
				cxt.fill();
				cxt.stroke();
			}
		})();
	},100);

}
</script>

</body>
</html> 
```

这是一个简单的演示，但是它展示了如何使用新的 HTML5 技术，同时保持对旧浏览器的支持。相同的概念可以用于其他应用中；例如，您可以提供一个动画图表，当 canvas 或 JavaScript 不可用时，它会退回到服务器生成的静态图像。

## 分享这篇文章