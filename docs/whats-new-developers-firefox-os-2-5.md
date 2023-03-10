# 火狐操作系统 2.5 对开发者来说有什么新功能

> 原文：<https://www.sitepoint.com/whats-new-developers-firefox-os-2-5/>

Firefox OS 2.5 计划于 11 月 2 日发布，为开发者带来了一系列新的可能性。在这篇文章中，我将向你展示下一个版本的 Firefox OS 会是什么样子。

火狐 OS 2.5 的早期版本代号为 3.0。正式发布版本为 2.5

## 介绍附加产品

今天，附加组件是网络浏览器世界中一个熟悉的概念，Mozilla 认为移动平台也应该从这种灵活性中受益。作为 Spark 项目的一部分，附加组件被整合到 Firefox OS 中，以类似于 Firefox 浏览器的方式定制体验。

附加组件是由 JavaScript、CSS 和其他资产组成的应用程序包。然而，它们不能作为独立的应用程序运行。相反，附加组件清单包括特殊的声明来定义将附加组件应用到什么应用程序。当应用程序在安装了附加组件的 Firefox OS 设备上启动时，该附加组件将被注入到任何与过滤器中指定的模式匹配的应用程序中。

附加组件和应用程序以相同的方式打包，因此它们有相似的安装过程，并且都在 [WebIDE](https://developer.mozilla.org/en-US/docs/Tools/WebIDE) 中受支持。虽然他们需要特权应用程序(从市场下载，侧装，安装在开发模式等。)，它们一旦安装就不需要任何特权。

为一个开放的网络平台添加一个插件的想法可能会引发必要性的问题，但这是一个简单的概念。一个 Firefox OS 插件只能扩展一个、几个或全部应用程序。想想 Firefox 的[缓冲插件。它将“缓冲”按钮集成到脸书或 Twitter 等社交网络的界面中，就像它们是原生按钮一样。Firefox OS 中的附加组件可以以类似的方式使用。](https://addons.mozilla.org/en-us/firefox/addon/buffer-for-firefox/)

接下来，我们将看看附加组件的主要组件。我建议你阅读 Mozilla 开发者网络上的[官方文档，了解细节和变化。](https://developer.mozilla.org/en-US/docs/Mozilla/Firefox_OS/Add-ons)

**注意:** Firefox OS 插件使用 [WebExtensions](https://wiki.mozilla.org/WebExtensions) 模型。它很大程度上基于 [Chrome/Blink](http://www.chromium.org/blink) 插件，在互操作性和功能上提供了优势。阅读 [WebExtensions API 文档](https://developer.mozilla.org/en-US/Add-ons/WebExtensions)来跟踪它的状态。

### 显示

让我们从清单开始。以下是一个附加组件清单示例(最简单的示例):

```
{
  "manifest_version": 1,
  "name": "Add-on banner",
  "description": "Firefox OS add-on example",
  "version": "1.0",
  "author": "Chris Mills",
  "content_scripts": [{
    "matches": ["app://system.gaiamobile.org/index.html"],
    "css": ["css/style.css"],
    "js": ["js/index.js"]
  }],
  "icons": {
    "128": "/icons/128.png"
  }
```

### 过滤

过滤器是[正则表达式](https://en.wikipedia.org/wiki/Regular_expression)与加载页面的 URL 相匹配，以确定应用程序是否受到附加组件的影响。

一些例子:

*   **。** *:匹配所有应用和页面。
*   **gaiamobile.org**:只匹配股票盖亚应用，不包括工程应用。
*   **m.facebook.com**:仅匹配脸书应用和网站。

### 启用/禁用

默认情况下，安装后会启用附加组件。它们可以通过*设置 app - >附件*或使用`navigator.mozApps.mgmt.setEnabled()`功能启用/禁用。

### 许可

加载项从其宿主应用程序继承权限。在附加组件清单中请求权限不会有任何效果，也不会公开任何对宿主应用程序不可用的 API

从附加组件写入窗口对象的任何内容对于应用程序代码都是不可用的，因为附加组件仅共享内容窗口的代理版本。由应用程序代码设置的窗口对象上的任何内容都可用于附加组件。DOM 可以像往常一样访问。

当加载项在应用程序启动时注入到应用程序中时，所有加载项文件都会在应用程序中的任何内容初始化之前注入到应用程序中，包括 DOM。请注意，附加组件可以注入到网页中。

目前，插件托管在一个名为 **[Hackerplace](https://github.com/fxos/directory/)** 的回购协议中，因为它们仍处于试验阶段。计划很快通过通用 Firefox 市场发布它们。

这只是触及了火狐操作系统附加组件相关新闻的表面，请前往 Mozilla 开发者网络深入了解这个主题。

## 语音识别

Mozilla 利用 Web Speech API 的力量，在名为 **Vaani** 的语音识别实现上取得了一些进展。没有太多的新闻，因为这是一个混乱的领域，W3C 从 2012 年开始制定 [API 规范，而 T4 从 2014 年](https://dvcs.w3.org/hg/speech-api/raw-file/tip/speechapi.html)开始制定非官方的草案规范。

据 Mozilla 语音识别项目的负责人 Kelly Davis 所说，Firefox OS 2.5 计划推出以下功能:

*   *呼叫【联系人】*
*   *呼叫【号码】*
*   *设置闹钟*
*   *设置定时器*
*   *打开【应用】*
*   语言:从 Firefox Marketplace 上传的英语、西班牙语和法语
*   启用社区贡献的设置(为 Mozilla 的 Amazon cloud 录制带有语言/口音的样本)

Kelly Davis 在 7 月份的 Mozilla Weekend Berlin 上谈到了语音识别的现状，所以如果你想了解更多关于这个过程的信息，可以看看视频。

[https://air.mozilla.org/kelly-davis-speech-recognition-at-mozilla/video/](https://air.mozilla.org/kelly-davis-speech-recognition-at-mozilla/video/)

## 钉网

开发者不太感兴趣，但仍然值得注意的是新的**Pin Web**功能，允许用户抓取网站并将其变成主屏幕图标，在视觉上类似于应用程序。这并不标志着技术上的突破，但用户将从这一新功能中受益。

![Pin the Web](img/c892784f483a188db8864dc6c4f4c75c.png)

[演示幻灯片解释了 <q>Pin the Web</q>](https://docs.google.com/presentation/d/17CGWPwu59GB7miyY1ErTjr4Wb-kS-rM7dB3MAMVO9HU/pub?start=false&loop=false&delayms=3000#slide=id.g98d4de165_0_7) 背后的理念，并提供了如下所示的功能原型:

[https://www.youtube.com/embed/FiLnRoRjD5k](https://www.youtube.com/embed/FiLnRoRjD5k)

## 跟踪保护

假设使用隐私标签的人不想被跟踪，Mozilla 在使用隐私浏览时在 Firefox Developer Edition 42 中加入了跟踪保护。这项功能现在也包含在 Firefox OS 2.5 中。虽然 Firefox(和 Firefox OS)有一个“不要跟踪”功能，告诉网站不要监视用户的行为，但公司并不需要遵守这一功能。Firefox 的跟踪保护功能会主动阻止已知会跟踪用户的域和网站。

## 查看源代码

Firefox OS 2.5 将允许用户查看 web 应用程序的源代码。当您想在不需要调试工具的情况下检查页面的一部分时，这很有用。本期将跟踪此功能的进展。

[https://www.youtube.com/embed/PzDiGC0Re14](https://www.youtube.com/embed/PzDiGC0Re14)

## 狐狸说了什么？

Firefox OS 2.5 被设计成一个完全可破解和可定制的操作系统，这是 Firefox 浏览器在 2004 年取得的进展，引入了一种新的方式来适应用户的喜好。那时，大多数浏览器会以(现在)烦人的工具栏形式提供定制。我很高兴 Mozilla 已经认识到他们过去的优势，并计划在 Firefox OS 中加以利用。

我觉得随着 Chris Beard 宣布的新的 Ignite 战略，Mozilla 将需要迅速行动，同时仍然意识到他们的影响。火狐操作系统是一个理想的移动操作系统，但是让它在实践中发挥作用是困难的(*低成本移动设备，**咳*** )。我确信 **Ignite** 将解决所有这些问题，如果 Mozilla 在下一个版本中没有实现他们的目标，Firefox OS 可能不会有第二次机会。一定要看好这个空间。

*你对 Firefox OS 2.5 有什么期待？*

## 分享这篇文章