# 移动网络应用:开店

> 原文：<https://www.sitepoint.com/mobile-web-apps-setting-up-shop/>

这是即将出版的新书《为智能设备建立移动网站和应用程序》的节选，作者是[厄尔·卡斯尔丁](http://twitter.com/#!/mrspeaker)、[迈尔斯·埃夫托斯](http://twitter.com/#!/madpilot)和[马克斯·惠勒](http://twitter.com/#!/makenosound)。在接下来的几周内，BuildMobile 将独家出版这本书的一个完整章节，关于移动网络应用的章节。享受吧。

## 1。开店

作为将 应用于我们的移动网站的第一步，我们将设置我们的环境，连接几个常见的 DOM 事件，并使用赋予我们的类似本机的特性来获得一些令人满意的“速胜”这些应该有助于我们顺利地创建一个可用的应用程序，我们可以在此基础上构建更多的高级功能。

### 1.1。框架和库

首先，我们需要做出一些艰难的决定。移动 web app 开发版图没有黄砖路；这更像是在一个密集、扭曲的超级酷设备和浏览器的森林中漫步——所有这些设备和浏览器的功能都千差万别。为了帮助我们，用于移动应用开发的新框架和库如雨后春笋般涌现。

一个框架可以通过处理跨设备和跨浏览器的不一致性来极大地简化我们的任务，并提供预构建的 UI 小部件和设计，我们可以将它们作为一个应用程序修补在一起。 [Sencha Touch](http://www.sencha.com/products/touch/) 和 [jQuery Mobile](http://jquerymobile.com/) 是两大玩家，但是还有其他人的分数，目前肯定没有“赢”的框架。所有的大框架都有优点和缺点，所以您需要仔细测试和评估这些选项，看看它们是否满足您对性能、兼容性和定制的需求。为了教你移动网络开发的基本概念，我们将在本书中编写我们自己的 UI 代码。

好吧，但是 DOM 库怎么样呢？我们当然没有必要编写自己的 DOM 操作代码，对吗？在过去的几年里，JavaScript 库改变了我们使用 DOM 的方式。我们可以用一些简洁、优雅的 API 来修改文档，并保持一定程度的信心，我们不会花费大部分工作日来解决桌面浏览器之间的不一致。

那么，把这种便利带到移动端有意义吗？答案当然是“视情况而定。”大多数主要的 DOM 库都非常注意确保它们能在所有桌面浏览器上运行，包括 IE6。但是当在移动网络应用上工作时，IE6 不是问题。同样，你需要评估你的选择。对于本书的其余部分，我们将继续使用 jQuery，因为它是众所周知的，但是所有的概念(和大部分代码)都可以在库之间轻松转换——或者转换成普通的 JavaScript。

### 更精简的 jQuery

如果你喜欢 jQuery，但对它的文件大小犹豫不决，你可能想看看有趣的 [Zepto 项目。](http://www.zeptojs.com/)它有一个 jQuery 兼容的(虽然不是全功能的)API，但是最小化后只有 4k 左右。这是可能的，因为它只针对移动 WebKit 浏览器，而不是浪费数百行代码来获得 IE6。

### 1.2。调试移动 JavaScript

我们已经决定使用 jQuery，所以让我们将我们的库添加到 HTML 文档的底部，尽可能放在最后一个`标签之前。我们还将抛出一个快速警报来验证 jQuery 已经启动并正在运行:`

```
<script src="javascripts/vendor/jquery-1.6.1.min.js"></script>
<script type="text/javascript">
  $(document).ready(function(){
    alert("StarTrackr loaded!");
  };
</script>
```

如果你没有收到警报呢？哪里出了问题？嗯，有几种排除故障和调试的方法。首先是在你的桌面浏览器上测试。Safari 或 Chrome(或另一种基于 WebKit 的浏览器)将在模拟 iPhone 或 Android 手机方面做得很好，它们内置的调试工具将为您省去很多麻烦。

iPhone 上也有一个基本的控制台，尽管默认情况下是禁用的。通过常规手机偏好设置在 Safari 偏好设置屏幕中打开它。控制台不仅会向您显示页面上发生的任何错误，您还可以在其中显示自己的消息——这比在代码中生成警告框要方便得多。我们可以使用几种不同的日志:

```
console.log("General log item");
console.info("Just some information.");
console.warn("Oooh, be careful here…");
console.error("We've got a problem!");
```

**iPhone 控制台**

[![fig_4_iphoneconsole](img/ee307225b4bc60749f92d18b2a6ccf85.png)](https://www.sitepoint.com/wp-content/uploads/2011/06/fig_4_iphoneconsole.png)

我们在这里只是输出字符串，但是你也可以打印变量的值。在 iPhone 上，您只能获得简单类型(字符串、数字等)的值，而不是对象。尽管如此，它仍然非常有用——只是记得在上线之前删除这些消息。如果用户的设备缺少一个`console`对象，它将抛出一个错误并停止你的脚本。

如果你在 Android 设备上测试，你可以在手机上启用 USB 调试，然后使用 Android SDK 附带的 Android 调试桥来记录从你的设备到你的计算机终端的消息。(我们将在第 7 章*介绍 PhoneGap* 中介绍 SDK 的安装。)要查看这些日志，请从 SDK 的`platform-tools`目录运行 **adb logcat** 。

### 构建移动图书

您可以从 Sitepoint 购买《为智能设备构建移动网站和应用程序》一书。阅读完整的[第 4 章。移动网络应用](https://www.sitepoint.com/build-mobile-book/)，独家在 BuildMobile，在下面的部分是免费的。

*   开店
*   [事件](https://www.sitepoint.com/mobile-web-apps-events/)
*   [速赢](https://www.sitepoint.com/mobile-web-apps-quick-wins/)
*   [加载页面](https://www.sitepoint.com/mobile-web-apps-loading-pages/)
*   Ajax
*   [模板化](https://www.sitepoint.com/mobile-web-apps-templating/)
*   我们有一个应用程序！` 

## `分享这篇文章`