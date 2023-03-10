# jQuery 悬停高亮脚本

> 原文：<https://www.sitepoint.com/jquery-hover-highlight-script/>

嗨，伙计们，我写了这个 jQuery 脚本，它可以高亮显示页面上的任何元素(通过改变背景颜色)。这是一个聪明的小脚本，如果你有很多需要鼠标悬停事件的元素，它会节省你很多时间。

## 特征

*   指定当鼠标悬停在元素上时悬停的背景颜色
*   当鼠标离开元素时保留背景色

## 使用

## 演示

以下 div 已被赋予 class="displayItem "并以橙色背景悬停。

**透明背景(仅在 FireFox 中)**

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nunc eu nulla leo. Proin ligula nunc, lacinia pellentesque bibendum ac.Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nunc eu nulla leo. Proin ligula nunc, lacinia pellentesque bibendum ac.Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nunc eu nulla leo. Proin ligula nunc, lacinia pellentesque bibendum ac.

**彩色背景(所有浏览器)**

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nunc eu nulla leo. Proin ligula nunc, lacinia pellentesque bibendum ac.Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nunc eu nulla leo. Proin ligula nunc, lacinia pellentesque bibendum ac.Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nunc eu nulla leo. Proin ligula nunc, lacinia pellentesque bibendum ac.

## jQuery.hoverHighlight()

我已经在不同的浏览器上测试了该脚本，并修复了 IE6 和 IE7 的错误(它们不支持 jQuery.data 方法，所以我为那些不支持它的浏览器编码了一个默认值)。所以现在它可以在所有浏览器上运行。

你还需要这个[脚本将颜色从 RGB 转换成十六进制](http://www.jquery4u.com/jquery-functions/jquery-convert-rgb-hex-color/)。

```
/*
 *	Create Hover Backgound Highlight for any element. 
 *	Retains original background-color.
 */
$.fn.extend({
	hoverHighlight: function (colour) 
	{
		$(this).live('mouseenter', function() 
		{
			/*save original background colour*/
			if ($(this).css('background-color') && $(this).css('background-color') !== 'transparent')
			{
				$(this).data('bgColour',rgb2hex($(this).css('background-color')));
			}
			else {
				$(this).data('bgColour','transparent');
			}
			$(this).css('background-color',colour);
		}).live('mouseleave', function() 
		{ 
			var defaultBg = 'transparent';
			var changeBg = ($(this).data('bgColour') !== null) ? $(this).data('bgColour') : defaultBg;
			$(this).css('background-color',changeBg);
		});
		return this; /*enable jQuery chaining*/
	}
});
```

## 以上演示的完整代码

## 分享这篇文章