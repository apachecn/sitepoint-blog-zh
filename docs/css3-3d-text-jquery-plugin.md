# 如何为 jQuery 创建一个 CSS3 3D 文本插件

> 原文：<https://www.sitepoint.com/css3-3d-text-jquery-plugin/>

在我之前的文章中，我们使用多重文本阴影在 CSS3 中创建了 [3D 文本。这可能是有效的，但产生这种效果是一个乏味的试错练习。](https://www.sitepoint.com/css3-3d-text/)

我们将通过一个 jQuery 插件让生活变得更加简单。它将完成艰巨的工作并生成冗长的 CSS3 代码，例如:

```
 text-shadow:
	-1px 1px 0 #ddd,
	-2px 2px 0 #c8c8c8,
	-3px 3px 0 #ccc,
	-4px 4px 0 #b8b8b8,
	-4px 4px 0 #bbb,
	0px 1px 1px rgba(0,0,0,.4),
	0px 2px 2px rgba(0,0,0,.3),
	-1px 3px 3px rgba(0,0,0,.2),
	-1px 5px 5px rgba(0,0,0,.1),
	-2px 8px 8px rgba(0,0,0,.1),
	-2px 13px 13px rgba(0,0,0,.1)
	; 
```

**note:** Is this wise?

我们的 jQuery 插件使用 JavaScript 来应用 CSS3 样式。你通常会避免这样做，因为这样会比较慢，而且没有 JavaScript，用户看不到效果。

然而，3D 不太可能是必不可少的，插件将节省大量的开发时间。如果你仍然觉得脏，将生成的文本阴影属性从 Firebug 复制到你的静态 CSS 文件中。

前往 [**插件演示页面**](https://blogs.sitepointstatic.com/examples/tech/css3-3d-text/text3d.html) 查看示例并下载代码。

## 如何使用插件

jQuery 和插件应该包含在您的页面中——理想情况下就在结束的

```
 <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.6.4/jquery.min.js"></script>
<script src="jquery.text3d.js"></script> 
```

要应用默认的 3D 效果，请为任何 HTML 元素设置一个“text3d”类，例如

```
 <p class="text3d">3D Text</p> 
```

或者，您可以为 JavaScript 中任意数量的元素指定自定义 3D 效果，例如

```
 <script>
$("#custom").text3d({
	depth: 6,
	angle: 135,
	color: "#aaa",
	lighten: -0.1,
	shadowDepth: 30,
	shadowAngle: 45,
	shadowOpacity: 0.2
});
</script> 
```

其中:

*   **深度**是 3D 拉伸的像素数
*   **角度**是 3D 挤压角度，即 90°垂直向下
*   **颜色**是 3D 挤出的颜色
*   **变亮**是挤出顶部和底部的颜色差异，例如-0.1 表示颜色将变暗 10%
*   **shadowDepth** 是阴影从文本中延伸出来的大概像素数
*   **阴影角度**是阴影落下的角度
*   **shadowOpacity** 是介于 0(无阴影)和 1(深黑色)之间的初始不透明度。请注意，多个阴影相互重叠，因此很少需要大于 0.4 的数值。

## jQuery 插件代码

任何编写或使用过 jQuery 插件的人都会熟悉我们的 JavaScript 模板:

```
 (function($) {

	// jQuery plugin definition
	$.fn.text3d = function(opts) {

		// default configuration
		var config = $.extend({}, {
			depth: 5,
			angle: 100,
			color: "#ddd",
			lighten: -0.15,
			shadowDepth: 10,
			shadowAngle: 80,
			shadowOpacity: 0.3
		}, opts);

		// to radians
		config.angle = config.angle * Math.PI / 180;
		config.shadowAngle = config.shadowAngle * Math.PI / 180;

		// ... main code ...

		// initialization
		this.each(function() {
			TextShadow(this);
		});

		return this;
	};

})(jQuery); 
```

config 对象是用一组可以被覆盖的默认属性定义的。**角度**和**阴影角度**假定为度，因此它们被转换为弧度。jQuery 选择器中的每个 HTML 元素都传递给一个 TextShadow()函数…

```
 // create text shadow
function TextShadow(e) {

	var s = "", i, f, x, y, c;

	// 3D effect
	for (i = 1; i <= config.depth; i++) {
		x = Math.round(Math.cos(config.angle) * i);
		y = Math.round(Math.sin(config.angle) * i);
		c = ColorLuminance(config.color, (i-1)/(config.depth-1)*config.lighten);
		s += x+"px "+y+"px 0 "+c+",";
	}

	// shadow
	for (f = 1, i = 1; f <= config.shadowDepth; i++) {
		f = f+i;
		x = Math.round(Math.cos(config.shadowAngle) * f);
		y = Math.round(Math.sin(config.shadowAngle) * f);
		c = config.shadowOpacity - ((config.shadowOpacity - 0.1) * i/config.shadowDepth);
		s += x+"px "+y+"px "+f+"px rgba(0,0,0,"+c+"),";
	}

	e.style.textShadow = s.substr(0, s.length-1);		
} 
```

该函数使用定义的参数构建文本阴影字符串，并将其应用于当前元素。请注意，3D 拉伸颜色是使用 ColorLuminance()函数生成的——更多信息，请参考[如何在 JavaScript](https://www.sitepoint.com/javascript-generate-lighter-darker-color/) 中计算更亮或更暗的十六进制颜色。

最后，下面的代码包含在我们的插件文件的末尾，用“text3d”类将默认的 3D 效果应用于所有元素。

```
 jQuery(function() {
	jQuery(".text3d").text3d();
}); 
```

希望你觉得有用。如 [**演示页面**](https://blogs.sitepointstatic.com/examples/tech/css3-3d-text/text3d.html) 所示，也可以采用 CSS3 转场来制作 3D 动画。

如果你创造了任何有趣的例子，请在下面的评论区张贴你的网址。

## 分享这篇文章