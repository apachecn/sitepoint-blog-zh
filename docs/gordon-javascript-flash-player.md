# 介绍 Gordon:用 JavaScript 编写的 Flash 播放器

> 原文：<https://www.sitepoint.com/gordon-javascript-flash-player/>

在过去的几年里，JavaScript 已经被用于许多不寻常的项目。我们有过 [NES 仿真器](https://www.sitepoint.com/jsnes-javascript-nes-emulator/)、[频谱仿真器](http://matt.west.co.tt/spectrum/jsspeccy/)，甚至[阿米加仿真器](http://www.chiptune.com/)。但是一个 Flash 播放器？…在浏览器中模拟浏览器插件有什么意义？

没有听起来那么离奇。基于 JavaScript 的播放器有一个很好的理由:Flash 插件并非在所有平台上都可用。最值得注意的是，你不能在 iPhone 或[苹果的新 iPad](https://www.sitepoint.com/apple-ipad-transitional-or-trashy/) 上运行 Flash。

“ [**戈登**](http://github.com/tobeytailor/gordon) ”是由[托拜厄斯·施耐德](http://github.com/tobeytailor)设计的一个巧妙命名的项目，希望能扭转这种局面。它将 Flash SWF 文件转换为 iPhone 支持的[可缩放矢量图形](https://www.sitepoint.com/9-reasons-why-svgs-are-important-for-the-web/)。项目[可以在大多数现代浏览器](http://wiki.github.com/tobeytailor/gordon/browser-support-table)上运行，除了 Internet Explorer(它不提供[原生 SVG 支持…但](https://www.sitepoint.com/internet-explorer-svg/))。

Gordon 目前支持 SWF 1.0 格式，SWF 2.0 正在开发中。有几个可用的演示，虽然它们很简单，但它们工作得很好，并展示了潜力。

当然，如果真正的 Flash 插件出现在 iPhone 上，所有这些工作都可能是徒劳的。但这似乎不太可能——史蒂夫·乔布斯不太重视 Flash，并表示它在设备上运行太慢。它也是标准 iPhone/iPad 应用平台的竞争对手。

然而，Gordon 已经在 iPhone 上进行了测试，即使它是基于解释的 JavaScript 代码构建的，它也运行得足够快。如果这个项目成功，人类蜈蚣 rapidshare 将无法阻止 Flash 在设备上运行。这能为开发者提供另一个理由让 T2 放弃 iPhone 应用程序吗？

你试过戈登吗？在不受支持的浏览器上，它能成为 Flash 插件的可行替代品吗？Adobe 应该立即雇佣开发人员吗？！

## 分享这篇文章