# 5 个有用的基本 jQuery 语法

> 原文：<https://www.sitepoint.com/5-basic-jquery-syntax/>

## **1)** **隐藏/显示**

这用于隐藏或显示没有效果的 html 元素。如果您想快速隐藏或显示一些 html 元素，请使用此选项。

*   ***语法:*** *【隐藏()】、显示()*

示例:

```
$(document).ready(function(){
	$(".btn1").click(function(){
		$("p").hide();
	});
	$(".btn2").click(function(){
		$("p").show();
	});
});
```

现场演示:
[http://www.w3schools.com/jquery/tryit.asp?filename = tryjquery _ eff _ show _ hide](http://www.w3schools.com/jquery/tryit.asp?filename=tryjquery_eff_show_hide)

## **2)**淡入/淡出

这涉及到隐藏和显示 html 元素使用漂亮的褪色效果。当你想以一种好的方式显示或隐藏一个 div 和它的内容时，它是很好的选择。

*   ***语法:****【fadeIn()】，fadeout()*

示例:

```
$(document).ready(function(){
	$(".btn1").click(function(){
		$("p").fadeOut()
	});
	$(".btn2").click(function(){
		$("p").fadeIn();
	});
});
```

现场演示:
[http://www.w3schools.com/jquery/tryit.asp?filename = tryjquery _ eff _ fade out _ fade in](http://www.w3schools.com/jquery/tryit.asp?filename=tryjquery_eff_fadeout_fadein)

 *## **3)** **停止**

如果你想停止当前运行的动画，只需使用这个。

*   ***语法:*** *stop()*

示例:

```
$(document).ready(function(){
	$("#start").click(function(){
		$("div").animate({height:300},3000);
	});
	$("#stop").click(function(){
		$("div").stop();
	});
});
```

现场演示:
[http://www.w3schools.com/jquery/tryit.asp?filename=tryjquery_eff_stop](http://www.w3schools.com/jquery/tryit.asp?filename=tryjquery_eff_stop)

## **4)** **禀**

一个很好的语法，用 CSS 样式将元素从一种状态改变到另一种状态。它只是逐渐改变 CSS 属性值，这将导致动画效果。

*   ***语法:*** *【禀()】*

示例:

```
$(document).ready(function()
{
	$("#btn1").click(function(){
		$("#box").animate({height:"300px"});
	});
	$("#btn2").click(function(){
		$("#box").animate({height:"100px"});
	});
});
```

现场演示:
[http://www.w3schools.com/jquery/tryit.asp?filename = tryjquery _ eff _ animate](http://www.w3schools.com/jquery/tryit.asp?filename=tryjquery_eff_animate)

## **5)** **滑动切换**

这种语法在各种使用 jQuery 的网站中被大量使用。理想情况下，它只是隐藏和显示一个 html 元素，但在一个非常好的滑动效果，就像一个典型的 WordPress 网站的管理员区左侧栏菜单。

*   ***语法:*** *slideToggle()*

示例:

```
$(document).ready(function(){
	$("button").click(function(){
		$("p").slideToggle();
	});
});
```

现场演示:
[http://www.w3schools.com/jquery/tryit.asp?filename = tryjquery _ eff _ slide toggle](http://www.w3schools.com/jquery/tryit.asp?filename=tryjquery_eff_slidetoggle)

## 分享这篇文章*