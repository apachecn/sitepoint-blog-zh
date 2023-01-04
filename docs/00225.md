# 构建 WebRTC 视频聊天应用程序

> 原文：<https://www.sitepoint.com/building-webrtc-video-chat-applications/>

随着 WebRTC 的出现和浏览器实时处理点对点通信能力的提高，构建实时应用程序比以往任何时候都更容易。在本文中，我们将了解一下 [SimpleWebRTC](https://www.simplewebrtc.com/) ，以及我们如何使用该平台来实现 WebRTC 技术。我们还将寻找其他可以帮助我们实现相同目标的替代方案。

如果您需要一些关于 WebRTC 和点对点通信的背景知识，我推荐您阅读“[WebRTC 的黎明](https://www.sitepoint.com/the-dawn-of-webrtc/)”和“[getuser media API 简介](https://www.sitepoint.com/introduction-getusermedia-api/)”。

由于构建自定义 WebRTC 应用程序的复杂性，这不是一个关于构建自定义 WebRTC 应用程序的分步教程。相反，我们将着眼于构建您自己的可靠应用程序所需的库和服务器类型。我将提供完整示例应用程序的链接，您可以在构建自己的应用程序时参考。

我们的重点将主要放在作为平台的 SimpleWebRTC 上。稍后，我们将简要介绍其他商业和开源替代方案，它们可能有助于您实现同样的目标。

## 什么是 WebRTC

WebRTC(Web Real Time Communication)是一个开源项目，它允许 Web 浏览器之间的点对点实时通信，以通过网络传输实时视频、音频和数据流。现代桌面和移动浏览器，如 Google Chrome、Mozilla Firefox、Safari、Opera 和其他基于 Chromium 的浏览器已经在本地实现了这项技术。这是一个好消息，因为用户不必安装第三方插件或应用程序来访问该技术。

较旧的浏览器版本和传统浏览器(如 Internet Explorer)没有这项技术。用户将需要使用最新的浏览器。您可以查看[支持的浏览器的完整列表](https://caniuse.com/?search=webrtc):