# 如何使用 HTML5 全屏 API

> 原文：<https://www.sitepoint.com/html5-full-screen-api/>

**更新:**此文现已过时。请看更新版本，[如何使用 HTML5 全屏 API(再次)](https://www.sitepoint.com/use-html5-full-screen-api/ "How to Use the HTML5 Full-Screen API (Again)")。

Flash 提供全屏模式已经很多年了，但直到现在，浏览器供应商一直抵制这一功能。主要原因:安全。如果你可以强制一个应用全屏运行，用户就失去了浏览器、任务栏和标准操作系统控件。他们可能无法关闭窗口，或者更糟的是，邪恶的开发者可能会模仿操作系统，诱骗用户交出密码、信用卡信息等。

在撰写本文时，HTML5 全屏 API 已经在 Firefox、Chrome 和 Safari 中实现。Mozilla 提供了很好的跨浏览器细节，但值得注意的是规范和实现细节可能会改变。

与按 F11 让浏览器全屏显示不同，API 将单个元素设置为全屏显示。它是为使用画布元素的图像、视频和游戏而设计的。一旦一个元素全屏显示，会临时出现一条消息，通知用户可以随时按 ESC 键返回窗口模式。

主要的属性、方法和样式有:

**element . request full screen()**
将单个元素全屏显示，例如 document . getelementbyid(" my video ")。请求全屏()。

**document . cancel full screen()**
退出全屏模式，返回文档视图。

如果浏览器处于全屏模式，则返回 true。

**:全屏**
一个 CSS 伪类，应用于全屏模式下的元素。

## 令人烦恼的供应商前缀

不要尝试使用这些 API 名称。CSS 和 JavaScript 属性都需要供应商前缀:

| 标准 | Chrome/Safari | 火狐浏览器 |
| 。请求全屏() | 。webkitRequestFullScreen() | 。mozRequestFullScreen() |
| 。cancelFullScreen() | 。webkitCancelFullScreen() | 。mozCancelFullScreen() |
| 。全屏 | 。webkitIsFullScreen | 。moz 全屏 |
| :全屏 | :-WebKit-全屏 | :-moz-全屏 |

Internet Explorer 或 Opera 还不支持，但我建议您使用“ms”和“o”前缀以备将来使用。

我在 [**演示页面**](https://blogs.sitepointstatic.com/examples/tech/full-screen/index.html) 中开发了一个功能，为您处理前缀诡计:

```
 var pfx = ["webkit", "moz", "ms", "o", ""];
function RunPrefixMethod(obj, method) {

	var p = 0, m, t;
	while (p < pfx.length && !obj[m]) {
		m = method;
		if (pfx[p] == "") {
			m = m.substr(0,1).toLowerCase() + m.substr(1);
		}
		m = pfx[p] + m;
		t = typeof obj[m];
		if (t != "undefined") {
			pfx = [pfx[p]];
			return (t == "function" ? obj[m]() : obj[m]);
		}
		p++;
	}

} 
```

然后，我们可以通过附加一个处理函数来使任何元素全屏显示，该函数可以确定它是否已经处于全屏模式，并相应地采取行动:

```
 var e = document.getElementById("fullscreen");

e.onclick = function() {

	if (RunPrefixMethod(document, "FullScreen") || RunPrefixMethod(document, "IsFullScreen")) {
		RunPrefixMethod(document, "CancelFullScreen");
	}
	else {
		RunPrefixMethod(e, "RequestFullScreen");
	}

} 
```

## CSS

一旦浏览器进入全屏模式，您几乎肯定会想要修改元素及其子元素的样式。例如，如果你的元素通常有 500 像素的宽度，你可以把它改为 100%，这样它就可以使用可用的空间

```
 #myelement
{
	width: 500px;
}

#myelement:full-screen
{
	width: 100%;
}

#myelement:full-screen img
{
	width: 100%;
} 
```

然而，**您不能使用厂商前缀选择器列表**:

```
 /* THIS DOES NOT WORK */
#myelement:-webkit-full-screen,
#myelement:-moz-full-screen,
#myelement:-ms-full-screen,
#myelement:-o-full-screen,
#myelement:full-screen
{
	width: 100%;
} 
```

出于某种奇怪的原因，您必须在它们自己的块中重复这些样式，否则它们不会被应用:

```
 /* this works */
#myelement:-webkit-full-screen	{ width: 100% }
#myelement:-moz-full-screen		{ width: 100% }
#myelement:-ms-full-screen		{ width: 100% }
#myelement:-o-full-screen		{ width: 100% }
#myelement:full-screen			{ width: 100% } 
```

奇怪。

[**在 Firefox、Chrome 或 Safari 中查看演示页面……**](https://blogs.sitepointstatic.com/examples/tech/full-screen/index.html)

这项技术效果很好。我发现的唯一问题是双显示器桌面上的 Safari 它坚持使用第一个显示器进行全屏模式，即使浏览器在第二个屏幕上运行？

虽然使用全屏模式可能还为时过早，但游戏开发者和视频制作者应该密切关注进展。

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com/)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如真实世界的 [HTML5 & CSS3。](https://learnable.com/courses/html5-css3-for-the-real-world-1484)

对本文的评论已经关闭。有关于 HTML5 的疑问？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?52-(X)HTML?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章