# jQuery 可以绑定的事件的完整列表

> 原文：<https://www.sitepoint.com/jquery-list-events-bind/>

正如您可能已经知道的，有相当多的**鼠标事件、键盘事件、浏览器事件、DOM 元素事件等，您可以用 JavaScript** 捕获，然后使用一些 jQuery 来执行基于使用 bind()、live()等触发的事件的操作。这里有一份你应该知道的主要事件的清单。

> 模糊、聚焦、聚焦、聚焦于、聚焦于、加载、调整大小、滚动、卸载、单击、dblclick、鼠标按下、鼠标抬起、鼠标移动、鼠标移过、鼠标移出、鼠标进入、鼠标离开、更改、选择、提交、按键、按键、按键、错误

## 鼠标事件

> 滚动、单击、dblclick、mousedown、mouseup、mousemove、mouseover、mouseout、mouseenter、mouseleave、加载、调整大小、滚动、卸载、错误、

**示例用法**

```
(function($) 
{

	$(document).ready(function()
	{
		$('#id).bind('click', function(e)
		{
			//mouse event fired (element clicked)
		});
	});

})(jQuery);
```

## 键盘事件

> 按键、按键、击键

**示例用法**

```
(function($) 
{

	$(document).ready(function()
	{
		$(document).bind('keypress', function(e)
		{
			//keyboard event fired
		});
	});

})(jQuery);
```

## 浏览器事件

> 加载、调整大小、滚动、卸载、出错

**示例用法**

```
(function($) 
{

	$(document).ready(function()
	{
		//browser event fired (document object model loaded)
	});

})(jQuery);
```

## DOM 元素事件

> 模糊、聚焦、聚焦、聚焦、改变、选择、提交

**示例用法**

```
(function($) 
{

	$(document).ready(function()
	{
		$('#id).bind('blur', function(e)
		{
			//dom event fired (input focus)
		});
	});

})(jQuery);
```

## 演示

参见:[找到被按下的键盘键的键码](http://www.jquery4u.com/events/find-keycode-keyboard-key-press/)

## 分享这篇文章