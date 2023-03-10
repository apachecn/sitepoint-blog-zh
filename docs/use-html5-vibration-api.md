# 如何使用 HTML5 振动 API

> 原文：<https://www.sitepoint.com/use-html5-vibration-api/>

用户越来越多地使用智能手机和平板电脑访问网络。截至 2013 年 12 月，每五次网络访问中就有一次来自移动设备。如果你的网站或应用是移动感知的，这个数字可能会高得多。

多种设备的开发有其挑战性，但也有桌面电脑通常不具备的可能性。考虑振动机制；这是一个简单的触觉反馈装置，可以提醒你有新消息或电话。在听不到声音的嘈杂环境中，或者在可以分散注意力的安静环境中，这种方法尤其有用。

如果您可以在应用程序中使用振动，那不是很好吗？…

*   行走方向可以用一次振动指示左侧，两次振动指示右侧。
*   当有事情发生或者你和某人很亲近时，手机会以某种方式振动。
*   你可以用基于振动的莫尔斯电码发送秘密信息！
*   当你坠毁或被导弹击中时，游戏可以通过振动来增强。

这正是 [HTML5 振动 API](https://www.w3.org/TR/vibration/) 允许你做的！

## 振动还是不振动？

仅仅因为我们可以振动手机，并不意味着我们应该这样做。震动是一种巨大的电池消耗，所以如果电量不足或游戏在当前标签中[不活跃，最好禁用震动。根据您的应用，最好提供一个用户选项，以便他们可以启用、禁用或配置振动标准。](/introduction-to-page-visibility-api/)

## 浏览器支持和检测

该 API 相对较新，目前支持仅限于 Firefox 和 Chrome 的最新版本。早期版本分别需要 moz 和 webkit 前缀。你也应该使用一个有振动机制的设备——你的浏览器里可能有 API，但是没有它你就不知道它在工作！

使用以下检查来检测振动支撑:

```
if ("vibrate" in navigator) {
	// vibration API supported
}
```

要检查和使用带前缀的版本，可以使用如下代码:

```
// enable vibration support
navigator.vibrate = navigator.vibrate || navigator.webkitVibrate || navigator.mozVibrate || navigator.msVibrate;

if (navigator.vibrate) {
	// vibration API supported
}
```

## 振动基础

可以通过将毫秒数传递给`navigator.vibrate`来设置基本振动:

```
// vibrate for one second
navigator.vibrate(1000);
```

或者，您可以传递一个带有以毫秒为单位指定的振动和延迟参数的数组。例如，要振动 500 毫秒，等待 300 毫秒，然后再次振动 100 毫秒:

```
// vibrate for one second
navigator.vibrate([500, 300, 100]);
```

偶数编号的数组项定义振动时间(数组从零开始，因此第一项和第三项是 0 和 2)。奇数编号的数组项定义延迟时间。

振动不堵塞；当设备振动时，您的 JavaScript 代码将继续运行。要停止它，可以将零传递给`navigator.vibrate`。

这个概念在游戏中会很有用。比如，当用户撞了他们的车，你设置`navigator.vibrate(10000)`。但是，如果碰撞效果在 10 秒钟之前结束，您可以设置`navigator.vibrate(0)`来结束它。

## 振动演示

要在您的设备中测试 API

[**查看震动 API 演示……**](https://blogs.sitepointstatic.com/examples/tech/vibration-api/index.html)

查看所有 HTML、CSS 和 JavaScript 的源代码。表单参数构建了一个数组，当点击 START 时，该数组被传递给`navigator.vibrate`。点击停止按钮时，执行`navigator.vibrate(0);`。

祝你在振动 API 上玩得开心，如果你对它有什么有趣的用途，请告诉我。

## 分享这篇文章