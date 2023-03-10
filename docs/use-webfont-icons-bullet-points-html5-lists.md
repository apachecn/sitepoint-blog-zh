# 如何在 HTML5 列表中使用网络字体图标作为项目符号

> 原文：<https://www.sitepoint.com/use-webfont-icons-bullet-points-html5-lists/>

HTML 无序列表很无聊。你可以定义一个圆盘、圆形或正方形，但要改变它们的大小或颜色并不容易。你可以使用`list-style-image`来定义一个图像，但它不一定要匹配字体大小，定位很困难，而且在旧版本的 IE 中有很多错误。

如果我们可以使用任何颜色的图标来代替标准的项目符号，那不是很好吗？

[**查看网页字体图标要点演示页面……**](https://blogs.sitepointstatic.com/examples/tech/icon-bullets/index.html)

## 精彩的网络字体

我喜欢网络字体图标。它们是定义为字体文件中的字符的矢量图形，可以在 HTML、CSS 和 JavaScript 中加载和使用。它们可以缩放到任何大小，提供出色的浏览器兼容性(IE6+支持)，单个字体通常比多个图像加载更快。

有几个在线工具可以帮助你选择合适的图标，然后将它们打包成跨浏览器的字体文件。我用的是 [Fontello](http://fontello.com/) ，但是 [IcoMoon](http://icomoon.io/) 和 [Flaticon](http://www.flaticon.com/) 做类似的工作。

## 网络字体图标替换

首先，我们需要一个标准的 HTML 列表。我们将向`ul`标签添加一个`icon`类，并且，可选地，我们可以向每个`li`标签应用不同的类来设置不同的图标:

```
<ul class="icon">
<li class="star">item 1</li>
<li class="info">item 2</li>
<li class="help">item 3</li>
</ul>
```

接下来，我们将设置`list-style-type`到`none`来删除标准的项目符号。然后，我们将在每个`li`上设置一个负的`text-indent`，将每个列表项的第一行向后移动。我使用的是 1.4em，但是您可以根据需要更改它，使图标之间的间距变大或变小:

```
ul.icon
{
	list-style-type: none;
}

ul.icon li
{
	text-indent: -1.4em;
}
```

为了添加图标，我们从伪元素`li:before`的`content`属性中的图标集中选择一个。注意，它向左浮动，宽度与上面设置的`text-indent`匹配——这将把第一行推回到它的原始位置:

```
ul.icon li:before
{
	font-family: bullets;
	content: "\e800";
	float: left;
	width: 1.4em;
}
```

最后，我们可以根据`li`类名设置不同的 webfont 图标，例如

```
ul.icon li.info:before { content: "\e800"; }
ul.icon li.star:before { content: "\e803"; }
ul.icon li.help:before { content: "\e801"; }
ul.icon li.bulb:before { content: "\e804"; }
ul.icon li.hand:before { content: "\e805"; }
```

[**查看网页字体图标要点演示页面……**](https://blogs.sitepointstatic.com/examples/tech/icon-bullets/index.html)

也可以设置不同的`color`、`font-size`和间距属性。图标将出现在您想要的位置，并与列表文本的大小相匹配。

使用网络字体图标是一个长期存在的问题的简单而优雅的解决方案…设计师从来不需要标准的要点。它适用于从 IE8 开始的所有浏览器，并且在 IE6 和 IE7 中降级良好。希望你觉得有用。

## 分享这篇文章