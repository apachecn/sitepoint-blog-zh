# 如何使用 HTML5 全屏 API(再次)

> 原文：<https://www.sitepoint.com/use-html5-full-screen-api/>

如果你不喜欢改变，也许 web 开发不适合你。我之前在 2012 年末描述过[全屏 API](/html5-full-screen-api/) ，虽然我声称实现细节有待修改，但我不认为一年后我需要重写！这可能不是最后一次，但非常感谢[大卫·斯托里](http://twitter.com/dstorey)突出了最近的技术过渡…

## 什么是全屏 API？

该 API 允许全屏查看单个元素。与按 F11 强制浏览器全屏不同，该 API 适用于在容器中运行的图像、视频和游戏。进入全屏模式时，会有一条消息通知用户可以随时按 ESC 键返回页面。

全屏 API 现在被所有最新的桌面浏览器支持，包括 IE11。手机上的支持很少，但是这些浏览器通常以几乎全屏的方式运行。不幸的是，我们有微妙的差异、前缀需求和跨浏览器的不一致需要解决…

## JavaScript API

假设我们有一个 ID 为`myimage`的图像，我们想全屏查看它。主要的方法和属性有:

**document . fullscreenabled***(已更改)*
当文档处于允许全屏模式的状态时，该属性返回`true`。它还可用于确定浏览器支持:

```
if (document.fullscreenEnabled) { ... }
```

早期的实现在“Screen”中有一个大写的“S ”, Firefox 仍然需要它。添加前缀会导致跨浏览器代码变得相当长:

```
// full-screen available?
if (
	document.fullscreenEnabled || 
	document.webkitFullscreenEnabled || 
	document.mozFullScreenEnabled ||
	document.msFullscreenEnabled
) {
...
}
```

Opera 12 是唯一不需要前缀的浏览器，但是 Opera 15+使用`webkit`。

**element . request full screen()***(已更改)*
该方法使单个元素全屏显示，例如

```
document.getElementById("myimage").requestFullscreen();
```

“屏幕”又变成了小写字母。跨浏览器代码:

```
var i = document.getElementById("myimage");

// go full-screen
if (i.requestFullscreen) {
	i.requestFullscreen();
} else if (i.webkitRequestFullscreen) {
	i.webkitRequestFullscreen();
} else if (i.mozRequestFullScreen) {
	i.mozRequestFullScreen();
} else if (i.msRequestFullscreen) {
	i.msRequestFullscreen();
}
```

**document . full screen element***(已更改)*
该属性返回当前全屏显示的元素，非全屏显示时返回`null`:

```
if (document.fullscreenElement) { ... }
```

screen 现在是小写字母。跨浏览器代码:

```
// are we full-screen?
if (
	document.fullscreenElement ||
	document.webkitFullscreenElement ||
	document.mozFullScreenElement ||
	document.msFullscreenElement
) {
...
}
```

**document . exit full screen***(已更改)*
该方法取消全屏模式:

```
document.exitFullscreen;
```

同样，我们有一个小写的“屏幕”。它以前被命名为`cancelFullScreen`，现在仍然在 Firefox 中。跨浏览器代码:

```
// exit full-screen
if (document.exitFullscreen) {
	document.exitFullscreen();
} else if (document.webkitExitFullscreen) {
	document.webkitExitFullscreen();
} else if (document.mozCancelFullScreen) {
	document.mozCancelFullScreen();
} else if (document.msExitFullscreen) {
	document.msExitFullscreen();
}
```

**document.fullscreenchange 事件**
该事件在全屏视图之间移动时触发。该事件没有提供任何信息，但是您可以通过检查`document.fullscreenElement`是否不是`null`来确定是否启用全屏。

```
document.addEventListener("fullscreenchange", function() { ... });
```

名称没有改变，但是我们要求 IE 使用跨浏览器前缀和骆驼大小写:

```
document.addEventListener("fullscreenchange", FShandler);
document.addEventListener("webkitfullscreenchange", FShandler);
document.addEventListener("mozfullscreenchange", FShandler);
document.addEventListener("MSFullscreenChange", FShandler);
```

**document.fullscreenerror 事件**
全屏可以失败。例如，没有`allowfullscreen`属性或窗口插件内容的 iframes 可能会被阻止。因此可以触发一个`fullscreenerror`事件:

```
document.addEventListener("fullscreenerror", function() { ... });
```

名称没有改变，但是我们要求 IE 使用跨浏览器前缀和骆驼大小写:

```
document.addEventListener("fullscreenerror", FSerrorhandler);
document.addEventListener("webkitfullscreenerror", FSerrorhandler);
document.addEventListener("mozfullscreenerror", FSerrorhandler);
document.addEventListener("MSFullscreenError", FSerrorhandler);
```

## 全屏 CSS

我们还可以影响 CSS 中的全屏样式…

**:全屏伪类** *(已更改)*
在全屏模式下查看时，您可以将样式应用到元素或其子元素:

```
:fullscreen {
	...
}
```

这之前被命名为`:full-screen`，现在仍然存在于 Webkit 和 Firefox 中。对于跨浏览器代码:

```
:-webkit-full-screen {
}

:-moz-full-screen {
}

:-ms-fullscreen {
}

:fullscreen {
}
```

**::背景** *(新)*
当全屏查看具有不同纵横比的元素时，您可以应用颜色或图像背景:

```
:fullscreen::backdrop {
	background-color: #006; /* dark blue */
}
```

背景是全屏元素后面的一个伪元素，但是在所有其他页面内容之上。IE11 支持，Firefox 和 Opera 12 不支持。Chrome、Safari 和 Opera 15+包含背景元素，但不允许对其进行样式化。目前，您只能针对 IE11，例如

```
:-ms-fullscreen::-ms-backdrop {
	background-color: #006; /* dark blue */
}
```

## 风格差异

在 IE11、Firefox 和 Opera 12 中，全屏元素被设置为 100%宽度和高度。因此，图像被拉伸，纵横比被忽略。在 IE11 中设置宽度和高度会将全屏元素定位到左上角，并带有深色背景(可使用::background 配置)。Opera 12 与 IE11 类似，但显示的是透明背景。Firefox 忽略了维度。在 Chrome、Safari 和 Opera 15+中，元素以黑色背景居中。

如果你想要一些一致性，很容易使 Webkit/Blink 浏览器像 Firefox/IE11 一样伸展:

```
:-webkit-full-screen {
	position: fixed;
	width: 100%;
	top: 0;
	background: none;
}
```

或者，您可以让 IE11 跟随 Webkit/Blink 居中:

```
:-ms-fullscreen {
  width: auto;
  height: auto;
  margin: auto;
}
```

这种方法在 Firefox 中不起作用，Firefox 会忽略上面提到的宽度和高度。要修复它，你需要让父元素全屏显示，并应用适当的大小，如本演示中的 所示的 [**。**](https://blogs.sitepointstatic.com/examples/tech/full-screen/index2.html)

## 准备部署了吗？

HTML5 全屏 API 相对简单，但浏览器差异会导致难看的代码，而且不能保证它不会再次改变。这种情况将会改善，因此在 API 变得更加稳定之前，最好在其他特性上投入时间和精力。

也就是说，全屏对于 HTML5 游戏和视频流量大的网站来说是必不可少的。如果你不想自己维护代码，可以考虑使用像 [screenfull.js](https://github.com/sindresorhus/screenfull.js/) 这样的库，它可以消除漏洞。祝你好运！

## 分享这篇文章