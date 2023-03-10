# 10 个著名的 jQuery 事件

> 原文：<https://www.sitepoint.com/10-jquery-events/>

## 什么是 jQuery 事件？

它是 jQuery 的效果或功能在满足特定条件后触发的部分，这可以通过不同的方式完成，如点击、聚焦、模糊等。现在，网页设计者考虑为特定的 jQuery 效果和功能使用正确的 jQuery 事件，这样，只有当某个元素被触发时，效果和功能才会发生，例如，单击登录按钮时出现弹出框，鼠标悬停在窗口上时，窗口会向下切换。

这里列出了 10 个众所周知的 jQuery 事件，它们经常被用来触发 jQuery 函数或动画。

## 1.点击

是的，你没看错；点击事件。它发生在用户点击一个元素时，该元素具有被点击时要完成的功能。

示例:

```
$(document).ready(function(){
	$("button").click(function(){
		$("p").slideToggle();
	});
});
```

现场演示:

[http://www.w3schools.com/jquery/tryit.asp?filename = tryjquery _ event _ click](http://www.w3schools.com/jquery/tryit.asp?filename=tryjquery_event_click)

## 2.鼠标悬停

当分配给一个元素时。当用户鼠标经过它的时候，它会触发它的功能。

示例:

```
$(document).ready(function(){
	$("p").mouseover(function(){
		$("p").css("background-color","yellow");
	});
	$("p").mouseout(function(){
		$("p").css("background-color","#E9E9E4");
	});
});
```

现场演示:

[http://www.w3schools.com/jquery/tryit.asp?filename = tryjquery _ event _ mouse over _ mouse out](http://www.w3schools.com/jquery/tryit.asp?filename=tryjquery_event_mouseover_mouseout)

## 3.准备好的

当 DOM(文档对象模型)和网页正确加载时，就会出现这种情况。这样，只有在网页成功加载元素的情况下，功能才准备就绪，这也可以防止错误。

示例:

```
$(document).ready(function(){
	$("button").click(function(){
		$("p").slideToggle();
	});
});
```

现场演示:

[http://www.w3schools.com/jquery/tryit.asp?filename = tryjquery _ event _ ready](http://www.w3schools.com/jquery/tryit.asp?filename=tryjquery_event_ready)

## 4.集中

当一个元素被聚焦时，它将触发它的指定功能。

示例:

```
$(document).ready(function(){
	$("input").focus(function(){
		$("input").css("background-color","#FFFFCC");
	});
	$("input").blur(function(){
		$("input").css("background-color","#D6D6FF");
	});
});
```

现场演示:

[http://www.w3schools.com/jquery/tryit.asp?filename = tryjquery _ event _ focus _ blur](http://www.w3schools.com/jquery/tryit.asp?filename=tryjquery_event_focus_blur)

## 5.虚化

元素失去焦点时触发函数。

示例:

```
$(document).ready(function(){
	$("input").focus(function(){
		$("input").css("background-color","#FFFFCC");
	});
	$("input").blur(function(){
		$("input").css("background-color","#D6D6FF");
	});
});
```

现场演示:

[http://www.w3schools.com/jquery/tryit.asp?filename = tryjquery _ event _ focus _ blur](http://www.w3schools.com/jquery/tryit.asp?filename=tryjquery_event_focus_blur)

## 6.键击器

当用户按下某个元素的指定键时，该元素的触发器就会起作用。

示例:

```
$(document).ready(function(){
	$("input").keypress(function(){
		$("span").text(i+=1);
	});
});
```

现场演示:

[http://www.w3schools.com/jquery/tryit.asp?filename = tryjquery _ event _ keypress](http://www.w3schools.com/jquery/tryit.asp?filename=tryjquery_event_keypress)

## 7.调整大小

调整浏览器窗口大小时会出现这种情况。

示例:

```
$(document).ready(function(){
	$(window).resize(function() {
		$("span").text(x+=1);
	});
});
```

现场演示:

[http://www.w3schools.com/jquery/tryit.asp?filename = tryjquery _ event _ resize](http://www.w3schools.com/jquery/tryit.asp?filename=tryjquery_event_resize)

## 8.错误

当一个元素不能完成它的主要目的时触发。可用于显示文本，简单地告诉用户发生了错误。

示例:

```
$(document).ready(function(){
	$("img").error(function(){
		$("img").replaceWith("

加载图像时出错！

");
	});
	$("button").click(function(){
		$("img").error();
	});
});
```

现场演示:

[http://www.w3schools.com/jquery/tryit.asp?filename = tryjquery _ event _ error _ trigger](http://www.w3schools.com/jquery/tryit.asp?filename=tryjquery_event_error_trigger)

## 9.绑

一个简单的 jQuery 事件，它在一个元素中绑定了一个或多个函数。

示例:

```
$(document).ready(function(){
	$("button").bind("click",function(){
		$("p").slideToggle();
	});
});
```

现场演示:

[http://www.w3schools.com/jquery/tryit.asp?filename = tryjquery _ event _ bind](http://www.w3schools.com/jquery/tryit.asp?filename=tryjquery_event_bind)

## 10.鼠标移动

当鼠标指针在指定元素中移动时，会出现这种情况。与鼠标悬停事件相同，但它使用指针在元素内逐个像素移动的时刻，所以使用时要小心。

示例:

```
$(document).ready(function(){
	$(document).mousemove(function(e){
		$("span").text(e.pageX + ", " + e.pageY);
	});
});
```

现场演示:

[http://www.w3schools.com/jquery/tryit.asp?filename = tryjquery _ event _ mousemove](http://www.w3schools.com/jquery/tryit.asp?filename=tryjquery_event_mousemove)

## 分享这篇文章