# 如何在 CSS3 中创建模糊的文本效果

> 原文：<https://www.sitepoint.com/css3-blurred-text-effect/>

这是我第一次在 Chris Coyier 的 CSS Tricks 网站上看到的一个很棒的文本效果。通过应用透明文本颜色和文本阴影，可以在 CSS3 中创建模糊文本:

```
 .blur-text
{
	color: transparent;
	text-shadow: 0 0 5px #000;
} 
```

![CSS3 Blurred Text](img/48f43775717d384061ccd7dc24bcdafa.png)

不幸的是，并不是所有的浏览器都支持文本阴影。IE9 和更低版本应用透明颜色，但没有阴影效果，文本变得不可见。我们必须求助于 [Modernizr](http://www.modernizr.com/) 或者[推出我们自己的文本阴影检测代码](https://www.sitepoint.com/detect-css3-property-browser-support/)。

当浏览器支持时，下面的 JavaScript 代码会将“textshadow”类追加到 HTML 元素中。因此，我们可以使用一个 CSS 选择器。文本阴影。模糊文本”以确保它仅在效果不会导致不良行为时应用:

```
 if (document.createElement("detect").style.textShadow === "") {
	document.getElementsByTagName("html")[0].className += " textshadow";
} 
```

**warning:** Opera’s blurry behavior

Chrome 和 Firefox 显示模糊的文本，并且在 IE 中被禁用。然而，歌剧可以是古怪的；它支持文本阴影，但不喜欢将它应用于透明文本。这似乎是一个错误，因为应用 rgba(0，0，0，0)颜色解决了这个问题。

## 模糊的线条

用一点额外的 CSS3 魔法，我们可以让链接在悬停或聚焦时平滑地模糊。对于导航菜单来说，这可能是一种令人愉快的效果。

我们将定义一个可以应用于任何链接的“模糊”类。链接将开始模糊，并返回到正常的焦点时，它是活跃的。类似地，我们将定义“模糊”类，它模糊悬停/聚焦时的文本，即

```
 <a href="#" class="blur in">starts blurred, ends un-blurred</a>
<a href="#" class="blur out">starts un-blurred, ends blurred</a> 
```

我们现在需要适用于所有浏览器的基本 CSS 样式，甚至是那些不支持文本阴影的浏览器:

```
 a.blur
{
	text-decoration: none;
	color: #339;
}

a.blur:hover, a.blur:focus
{
	text-decoration: underline;
	color: #933;
} 
```

下一组样式应用于所有。模糊元素，无论它们是否有焦点:

*   我们删除链接下划线和轮廓
*   将文本颜色设置为透明，然后
*   应用一个 CSS3 过渡，平滑地在无阴影和全文本阴影之间移动。效果在 100 毫秒后开始，400 毫秒后结束。

```
 .textshadow a.blur, .textshadow a.blur:hover, .textshadow a.blur:focus
{
	text-decoration: none;
	color: rgba(0,0,0,0);
	outline: 0 none;
	-webkit-transition: 400ms ease 100ms;
	-moz-transition: 400ms ease 100ms;
	transition: 400ms ease 100ms;
} 
```

最后，我们定义了两种文本阴影状态。第三个文本阴影属性定义了模糊的“数量”。它将在 0 和 4px 之间动画，但如果你想更多或更少的模糊，这可以修改:

```
 /* no blur */
.textshadow a.blur.out,
.textshadow a.blur:hover, .textshadow a.blur:focus
{
	text-shadow: 0 0 0 #339;
}

/* full blur */
.textshadow a.blur,
.textshadow a.blur.out:hover, .textshadow a.blur.out:focus
{
	text-shadow: 0 0 4px #339;
} 
```

[**查看链接模糊演示页面**](https://blogs.sitepointstatic.com/examples/tech/link-blur/index.html) —源码包含所有需要的 CSS 和 JavaScript。

我希望你觉得它有用，但是在使用这个效果的时候要注意可访问性/可见性的问题。我很感激你的评论，如果你在其他地方使用它，请贴上网址。

## 分享这篇文章