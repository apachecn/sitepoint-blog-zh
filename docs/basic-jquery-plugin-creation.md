# 基本 jQuery 插件创建

> 原文：<https://www.sitepoint.com/basic-jquery-plugin-creation/>

正如我之前所说，现在有很多 jQuery 插件可用。有些是付费的，有些是免费的。但是没关系，只要你想要一个特定的插件，你会做任何事情来得到它。实际上，您已经下载或计划下载的插件并不是凭空出现的 jQuery 插件，而是由它们的作者或开发人员创建的。它是为开发者的特定目的而开发的。

所以说了这么多，你有没有考虑过创建自己的 jQuery 插件？

这并不难，我们不会讨论高级或复杂的 jQuery 插件，但我们会有一个简单的“中心”插件。

基本上，我们将创建一个插件来调整指定元素的样式，以保持它在页面上水平和垂直居中。

**演示:**
[http://net . tuts plus . com/tutorials/JavaScript-Ajax/coding-your-first-jquery-ui-plugin/](http://net.tutsplus.com/tutorials/javascript-ajax/coding-your-first-jquery-ui-plugin/)

## 1.添加 Javascript 文件

添加一个空白的 JavaScript 文件对于创建一个插件是必不可少的。命名约定规定 jQuery 插件应该命名为“yourpluginname.jQuery.js”。确保在文档中创建对它的引用。

```
<head>

<script src="jquery-1.2.6.pack.js">

</script><script src="center.jQuery.js">

</script></head>
```

## 2.粘贴代码

将以下代码粘贴到它上面。

```
(function($){

	$.fn.center = function(){

		var element = this;

		$(element).load(function(){

			changeCss();

			$(window).bind("resize", function(){

				changeCss();

			});

			function changeCss(){

				var imageHeight = $(element).height();

				var imageWidth = $(element).width();

				var windowWidth = $(window).width();

				var windowHeight = $(window).height();

				$(element).css({

					"position" : "absolute",

					"left" : windowWidth / 2 - imageWidth / 2,

					"top" : windowHeight /2 - imageHeight / 2

				});

			};

		});

	};

# })(jQuery);
```

把这张也贴上。

$ . fn . center = function(){ }；

“中心”只是一个样本名称；您应该用您要使用的名称来替换它。这是为了通知 jQuery 您正在扩展它的方法。现在这段代码什么都不做；我们可以用以下方式调用我们的方法:

```
$(function(){

$("#someElement").center();

});
```

## 3.知道如何使图像居中

首先，你必须已经知道如何手动将图像居中。您的元素必须定位为绝对元素。或者很明显，当我们改变“左”或“右”值时，它不会移动。好的，接下来，图像需要向左移动浏览器窗口宽度的 50%。最后，为了调整图像的宽度，我们需要减去图像宽度的 1 . 5 倍。

```
function changeCss(){

	var imageHeight = $(element).height();

	var imageWidth = $(element).width();

	var windowWidth = $(window).width();

	var windowHeight = $(window).height();

	$(element).css({

		"position" : "absolute",

		"left" : windowWidth / 2 - imageWidth / 2,

		"top" : windowHeight /2 - imageHeight / 2

	});

};
```

这将把图像完美地放置在页面的中央。

## 4.创建监听程序

当浏览器调整大小时，我们需要一个监听器。

```
$(window).bind("resize", function(){

changeCss();

});
```

函数“ChangeCss()”是调整图像顶部和左侧值的函数。每当通过调整窗口大小来调用它时，jQuery 都会重新计算这些值。

## 结论

正如您所看到的，创建 jQuery 插件并不困难，只要您理解自己在做什么以及这些代码的用途。基本上，我们在这里做的事情很简单，你应该不会遇到任何问题。

## 分享这篇文章