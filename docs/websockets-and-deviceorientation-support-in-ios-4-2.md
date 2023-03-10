# iOS 4.2 中的 WebSockets 和 DeviceOrientation 支持

> 原文：<https://www.sitepoint.com/websockets-and-deviceorientation-support-in-ios-4-2/>

适用于 iPhone 和 iPad 的苹果 iOS 操作系统最新版本 4.2 增加了对移动 Safari 的 DeviceOrientation 和 WebSockets APIs 的支持。

[DeviceOrientation 事件规范](http://dev.w3.org/geo/api/spec-source-orientation.html)是一个 W3C 规范草案，详细描述了大量与设备方向变化(这并不奇怪)及其运动相关的 JavaScript 事件。Maximiliano Firtman 已经做了一个简单的滚球演示来展示这个功能，所以如果你有一个设备，[去看看](http://www.mobilexweb.com/samples/ball.html)。如果没有，你可以在这里观看视频。所需的代码非常简单，所以即使您不能观看演示，我也建议您查看源代码，看看它是如何完成的。设置加速度和速度变量有些花哨，但其核心是调整球的位置以响应`window.ondevicemotion`事件。

iOS 4.2 中 Mobile Safari 的另一项功能是支持 web sockets。web 开发人员更有可能对此感兴趣，不仅因为它具有更广泛的适用性，还因为套接字在其他浏览器中已经获得了很好的支持。WebSocket API 允许客户端和服务器之间的双向通信。对于当前依赖于某种形式的 AJAX 轮询的实时应用程序来说，这尤其有趣(关于 API 的概要和为什么它如此棒，请查看 Opera 开发者社区博客上的[这篇文章)。WebSockets 已经可以在 Chrome 和 Safari 中使用，也可以在 Firefox 4 和 Opera 11 的当前测试版中找到。这使得 IE9 仍然处于平台预览版，仍然没有支持——尽管总有希望在正式发布之前添加这一功能。在移动方面，当前的 Android 版本(2.2)不包括 WebSockets 支持，但据传 2.3 即将推出，很有可能 WebSockets 将出现在添加列表中。](http://dev.opera.com/articles/view/introducing-web-sockets/)

你可以通过 Remy Sharp 的这个演示来了解 WebSockets 有多有用。并排打开两个浏览器窗口，从其中一个窗口发送聊天信息，并立即在另一个窗口看到它，无需任何持续连接或轮询。演示的后端由一个非常简单的 [node.js 服务器](https://github.com/remy/html5demos/tree/master/server/)提供。

尽管目前的支持水平还不完全，但这些新 API 中的许多肯定会得到 steam 和浏览器的采用，所以在不久的将来，我们就可以开始在跨平台应用程序中使用它们了。

*在[知识共享署名](http://creativecommons.org/licenses/by/2.0/)下使用的缩略图，由 [Calsidyrose](http://www.flickr.com/photos/calsidyrose/) 提供。*

**note:**Want more?

如果你想阅读更多关于 Louis 的文章，请订阅我们每周的科技极客时事通讯， *[《科技时报》](https://www.sitepoint.com/newsletter/)* 。

## 分享这篇文章