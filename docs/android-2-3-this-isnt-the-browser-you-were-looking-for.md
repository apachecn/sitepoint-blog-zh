# Android 2.3:这不是你要找的浏览器

> 原文：<https://www.sitepoint.com/android-2-3-this-isnt-the-browser-you-were-looking-for/>

谷歌昨天[宣布了其 Android 移动操作系统的最新版本:版本 2.3，代号姜饼](http://android-developers.blogspot.com/2010/12/android-23-platform-and-updated-sdk.html)。虽然有许多很酷的新功能可供原生应用程序开发人员使用(如前置摄像头支持、可混合的音频效果和大量新的传感器输入选项)，但我们这些更喜欢在浏览器中工作的人几乎没有收到新玩具。上周，我[写了一篇关于 iOS 4.2](https://www.sitepoint.com/websockets-and-deviceorientation-support-in-ios-4-2/) 的新特性的博客，其中有一些是网络开发者特别感兴趣的:特别是设备定向 API 的实现和对 websockets 的支持。不幸的是，这些功能都没有进入 Android 2.3。

快速浏览一下[发布说明](http://developer.android.com/sdk/android-2.3-highlights.html)发现，在增加对基于浏览器的技术的支持方面很少:没有 websockets，没有加速计，甚至没有 SVG。事实上，基于 webkit 的 Android 浏览器唯一增加的似乎是对 [WebM HTML5 视频](https://www.sitepoint.com/google-announces-new-webm-video-standard-open-sources-vp8/)和 AAC 音频的支持。

鉴于 Android 今年在市场份额上的巨大增长，这种在实现新的浏览器功能方面的滞后对于那些急于利用所有最新的花哨功能推出酷的新网站的网络开发者来说是一种真正的痛苦。此外，由于大多数缺失的功能已经在桌面版 Chrome 中出现，很难理解谷歌为何决定将它们排除在 Android 浏览器之外。当操作系统实际上可供用户使用时，这些浏览器功能中的一些可能会出现，并且它们只是在 SDK 发行说明中被省略，因为它们不会影响本地应用程序开发人员——但似乎更有可能的是，我们必须等到下一个主要版本。

## 分享这篇文章