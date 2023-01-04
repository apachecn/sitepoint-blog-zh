# 给设备测试带来理智和秩序

> 原文：<https://www.sitepoint.com/bringing-sanity-order-device-testing/>

本文是微软网站开发系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

似乎每隔一天，公众就会获得一些新的上网方式。有时候是新的浏览器。其他人则认为这是[的一款新智能手机](http://www.pcadvisor.co.uk/reviews/mobile-phone/3504276/yotaphone-2-review-uk-dual-screen-smartphone/?WT.mc_id=13404-DEV-sitepoint-article28)。或者说[一个平板](http://www.engadget.com/products/microsoft/surface/3/?WT.mc_id=13404-DEV-sitepoint-article28)。或者是电子阅读器。或者[一个视频游戏控制台](https://www.nintendo.com/3ds/internetbrowser/specs/?WT.mc_id=13404-DEV-sitepoint-article28)。或者是智能手表。或者[一台电视](https://html5test.com/compare/browser/samsungsmarttv-2013.html?WT.mc_id=13404-DEV-sitepoint-article28)。或者[一个平视显示器](http://www.techradar.com/us/reviews/wearables/microsoft-hololens-1281834/review?WT.mc_id=13404-DEV-sitepoint-article28)。或者[一辆车](http://www.popularmechanics.com/cars/a13191/the-future-of-car-connectivity-is-a-real-web-browser-in-the-dash-17416796/?WT.mc_id=13404-DEV-sitepoint-article28)。或者一个冰箱。

我参与的一个项目中，客户向我提供了一个电子表格，其中详细列出了访问 m-dot 站点登录屏幕的 1400 个不同的用户代理。两天后！

作为进一步的证据，考虑一下来自非营利组织全国家庭关系委员会的 Jason Samuels 的这篇文章的启发性细节:

*   2008 年，来自“移动”设备的访问仅占其流量的 0.1%左右。2014 年，这一数字飙升至 14.4%。
*   在 2008 年，他们检测到 71 种不同的屏幕分辨率，这已经是一个很大的问题了。然而，到 2014 年，他们每个季度都会看到 1，000 个不同的屏幕分辨率(其中超过 200 个记录了每个季度 10 次以上的访问)。

每当我读到这最后一个数据时，我都会感到震惊。你不能为 200 种不同的屏幕设计，更不用说 1000 种了。这是徒劳的差事。甚至不要想在那么多设备上进行测试。

然而，我们在这里设计的网站可以(并将)去任何地方。我们需要彻底的测试，因为我们不能对用来访问我们内容的浏览器和设备做任何假设。

测试可能是乏味的、耗时的和昂贵的。肯定有办法让它变得更容易。确实是这样的:与其纠结于创造一种需要在每个浏览器上都几乎相同的体验，我们可以更聪明地看待我们如何构建东西，并将体验视为一个连续体。

我们可以建立这样的网站，它们既足够灵活，可以通过慢速网络在低功率设备上运行*又足够智能，可以在机会到来时利用先进的功能和传感器。*

什么？！我们可以鱼和熊掌兼得？是的。是的，我们可以。

## 在坚实的基础上开始

当处理网络设备的疯狂增长和我们网站将走向何方的巨大未知时，后退一步并专注于重要的事情是值得的。我们需要问自己两个简单的问题:

1.  1.这个页面，这个表格，这个界面的目的是什么？
2.  2.实现这个目的最简单的方法是什么？

然后我们需要先建立那个*。通常我们谈论的是文本、一些基本的 HTML、到其他页面的实际链接以及提交给某种后端的表单。这是我们最低可行的产品，它将在任何地方工作。*

 *然后，我们可以寻找机会来增强体验，同时将功能核心保持在体验的中心。

我们可以使用 CSS 为页面添加视觉层次，提供一些视觉趣味，并调整布局，以在各种屏幕尺寸上创建良好的阅读体验。我们应该从我们能想象的最窄的屏幕尺寸开始，[让内容引导我们的断点决定](https://twitter.com/brad_frost/status/191977076000161793)。

我们将使用 JavaScript 向用户提供实时反馈。[我们将劫持](http://domscripting.com/presentations/xtech2006/?WT.mc_id=13404-DEV-sitepoint-article28)到[的表单和链接，延迟加载附加内容](http://www.filamentgroup.com/lab/ajax-includes-modular-content.html?WT.mc_id=13404-DEV-sitepoint-article28)，或者避免整页刷新。见鬼，我们甚至可以接管整个页面，然后[把它转换成一个单页应用](http://nerds.airbnb.com/isomorphic-javascript-future-web-apps/?WT.mc_id=13404-DEV-sitepoint-article28)。

但是我们永远不应该牺牲功能核心。

这种为网络设计的方法被称为[渐进增强](http://alistapart.com/article/understandingprogressiveenhancement?WT.mc_id=13404-DEV-sitepoint-article28),它是应对旧浏览器和设备激增的头号工具。

## 在你的表达中要保守

在处理如此多的设备和浏览器时，渐进式改进之所以如此有用，是因为核心体验总是可用的。没有什么可以阻止我们的用户访问它，即使是在一个蹩脚的 WAP 浏览器或基于文本的浏览器上，比如 Lynx，甚至是一些[还没有出现的会说话的电脑](http://www.theubi.com/?WT.mc_id=13404-DEV-sitepoint-article28)，它只能把网络“看”成文本。

为了确保我们不会意外地将高级功能提供给像这样能力较弱的浏览器，我们只需要聪明地对待如何加载 CSS 和 JavaScript 之类的东西。

一些基本的 CSS——考虑排版、颜色等。—几乎任何人都可以使用，所以我们可以把所有的东西放在一个 CSS 文件中(例如`basic.css`)，并用一个标准的`link`包含它。然后，我们可以将所有的布局规则和其他高级 CSS 放到一个单独的 CSS 文件(例如`advanced.css`)中，通过相关的媒体查询链接到该文件。

```
<link rel="stylesheet" href="default.css" media="all">
<link rel="stylesheet" href="advanced.css" media="only screen">
```

任何不理解媒体查询的[浏览器将完全忽略第二个 CSS 文件](http://www.slideshare.net/bryanrieger/rethinking-the-mobile-web-by-yiibu/106?WT.mc_id=13404-DEV-sitepoint-article28)，只接收线性的移动视图。很容易，IE8 得到了移动布局(一个基本的支持水平，不太可能给你带来任何测试上的麻烦)。

你甚至可以更进一步，在 advanced.css 中使用[@ supports](https://developer.mozilla.org/en-US/docs/Web/CSS/@supports?WT.mc_id=13404-DEV-sitepoint-article28)[块](https://developer.mozilla.org/en-US/docs/Web/CSS/@supports?WT.mc_id=13404-DEV-sitepoint-article28)，将某些规则集限制为只支持特定 css 特性的浏览器。

当然，与 JavaScript 相比，CSS 支持问题算不了什么，所以有时最好不要将某些基于 JavaScript 的功能交付给不能处理它的浏览器。这就是[特征(和物体)检测](http://www.quirksmode.org/js/support.html?WT.mc_id=13404-DEV-sitepoint-article28)变得非常有用的地方:

```
if ( 'querySelector' in document ) {
    // We can use querySelector!
}
```

你也可以使用[反向条件注释](http://en.wikipedia.org/wiki/Conditional_comment#Downlevel-revealed_conditional_comment?WT.mc_id=13404-DEV-sitepoint-article28)在第一时间禁止旧版本 IE 获取 JavaScript(这意味着你甚至不需要担心在那里调试 JavaScript)。下面的例子对 IE8 及以下版本隐藏了 main.js，但对 IE9 及以上版本的所有其他非 IE 浏览器都可用。

```
<!--[if gt IE 8]><!-->
    <script src="main.js"></script>
<!--<![endif]-->
```

通过对我们提供给浏览器的内容保持保守，我们[确保了最大程度的支持，但仍然可以为更高级的浏览器进行优化](http://bradfrost.com/blog/mobile/support-vs-optimization/?WT.mc_id=13404-DEV-sitepoint-article28)。这使得测试变得更加容易，因为我们知道老版本的浏览器基本上没问题，而且我们不会尝试使用 JavaScript 特性，除非我们知道它们是可用的。

## 测试，测试，测试

渐进式增强帮助我们在进入项目的测试阶段之前避免了许多渲染和脚本问题，但最终我们确实需要坐下来按照项目的进度运行项目。

为了在开发过程中保持测试的可管理性，最好在一个我们知道具有良好 web 标准支持的浏览器中进行测试。这将为我们的工作提供一个基本的检查。一旦我们确信一切正常，我们就可以开始在各种浏览器和设备上进行更彻底的测试。

我个人在任何时候都会在笔记本电脑上保存几个版本的主要浏览器。我在 Mac 上工作，所以我通常会有几个版本的 Chrome、Firefox 和 Opera。您可以在此获得这些浏览器的旧版本:

*   [铬档案](http://commondatastorage.googleapis.com/chromium-browser-continuous/index.html?WT.mc_id=13404-DEV-sitepoint-article28)
*   [火狐档案](http://ftp.mozilla.org/pub/mozilla.org/firefox/releases/?WT.mc_id=13404-DEV-sitepoint-article28)
*   [戏曲档案](http://www.opera.com/download/?custom=yes?WT.mc_id=13404-DEV-sitepoint-article28)

在现代版的 OS X 上运行旧版本的 Safari 几乎是不可能的，所以我通常只在本地安装最新版本。

为了在上述浏览器的 Windows 版本上进行测试，我通常在任何地方都有 3 到 5 台运行各种 Windows 版本及其相关浏览器版本的虚拟机，此外(通常)还有 Chrome、Firefox 和 Opera 的副本。MS Edge Dev 网站提供[免费 Windows 虚拟机供下载](http://dev.modern.ie/tools/vms/?utm_source=SitePoint&utm_medium=article28&utm_campaign=SitePoint)。如果你只是想检查一下微软最新最好的产品，这里还有[遥控器。IE 服务](https://remote.modern.ie/?utm_source=SitePoint&utm_medium=article28&utm_campaign=SitePoint)，允许你连接到浏览器的虚拟化版本。

如果您在 Windows 或 Linux 上开发，您将需要访问 Mac，或者您将不得不依靠虚拟化在该平台上进行测试。我稍后将讨论虚拟化。

一旦您在各种桌面浏览器中进行了彻底的测试，是时候深入设备世界了。如果你不确定从哪里开始，看看你的分析；但是不要全信。分析会让你做出错误的假设。例如，如果你看到黑莓用户的比例很低，会不会是因为你的网站在黑莓中运行不佳，所以他们不会逗留(或回来)？当心自我实现的预言。

如果你有预算的话，尽一切可能挑选一些设备放在手边进行测试。你可以使用 Adobe 的 Edge Inspect、 [Vanamco 的 Ghostlab](http://vanamco.com/ghostlab/?WT.mc_id=13404-DEV-sitepoint-article28) 或 [Viljami Salminen 的 Remote Preview](https://github.com/viljamis/Remote-Preview?WT.mc_id=13404-DEV-sitepoint-article28) (或以上所有工具的组合)来同步浏览一些设备。其中一些工具还允许对设备进行远程检查，以调试 CSS 和 JavaScript。weinre(Adobe Edge Inspect 使用的)和 [Vorlon.js](http://vorlonjs.com/?WT.mc_id=13404-DEV-sitepoint-article28) 也提供远程检查功能。

如果你足够幸运，附近就有一个，你应该去当地的[开放设备实验室(ODL)](http://opendevicelab.com/?WT.mc_id=13404-DEV-sitepoint-article28) 进行测试。odl 是免费的社区资源，通常由 web 设计工作室或拥有大量设备的个人提供。ODL 的人应该可以帮你挑选测试设备，并向你介绍他们实验室里的测试工具。

如果你附近没有 ODL，你也可以在当地的手机或电子产品商店做一些游击式的测试。只要确保他们有真正的设备…你不会在假塑料上走太远。

如果这些都不是您的选择，总有虚拟化。您可以下载并安装用于各种移动浏览器和设备的[模拟器。此外，像](http://www.mobilexweb.com/emulators?WT.mc_id=13404-DEV-sitepoint-article28) [Browserstack](https://www.browserstack.com/?WT.mc_id=13404-DEV-sitepoint-article28) 和 [CrossBrowserTesting](http://crossbrowsertesting.com/?WT.mc_id=13404-DEV-sitepoint-article28) 这样的服务提供对数百个虚拟桌面和设备的访问，只收取象征性的费用。

虚拟化永远不会给你与手握真实设备相同的体验。性能很少是相同的，并且你不知道设备如何响应你的输入。我曾经偶然发现了一个 Android 2.3 的 bug，每次旋转设备时，生成的内容都会被重新生成和重新插入。我怀疑我会发现使用模拟器。也就是说，仿真器可以帮助您大致了解您的接口是否工作。

无论你通过何种方式获得测试设备，都要尽量覆盖好横截面。挑选一些低端设备，几个旧的高端设备，一些最新的旗舰产品，以及各种各样的屏幕尺寸和分辨率。确保你在操作系统方面也有很好的覆盖——最新的 iOS 和 Android 版本是给定的，但确保你有一两个 Windows 设备，几个黑莓选项，以及一些旧的 Android 和 iOS 版本。然后放入一个古怪的 [或者两个](http://www.windowscentral.com/have-39-spare-then-pick-odd-sylvania-7-netbook-windows-ce?WT.mc_id=13404-DEV-sitepoint-article28)来看看你的接口是否支持。

重要的是要记住，我们永远不可能在每台设备上给每个用户完全相同的体验。我们需要接受这一点——经验是一个连续体。只要我们的用户能在我们的网站上完成他们需要做的事情，他们就会得到很好的服务。

## 加分:拥抱模式

如果我们真的想让我们自己(和我们的团队)的事情变得简单，我们应该考虑在建立我们网站的单个页面之前建立一个[模式库](http://alistapart.com/blog/post/getting-started-with-pattern-libraries?WT.mc_id=13404-DEV-sitepoint-article28)。

将我们的界面分解成离散的、可重复的模式(例如，标签和表单控件、选项卡式界面等。)让我们孤立地看待每一个，并以这种方式进行测试。隔离测试比调试一个有很多活动部件的页面要容易得多。

将我们的模式收集到一个实时的基于网络的模式库中，让我们团队中的任何人都可以像在 smrgs bord 组装盘子一样轻松地收集构建给定界面所需的模式。如果我们真的想简化构建和测试过程，我们甚至可以[将模式导入到现场](http://ianfeather.co.uk/a-maintainable-style-guide/?WT.mc_id=13404-DEV-sitepoint-article28)中，这样一切都保持同步。

## 不要害怕僵尸末日

随着新设备、外形和考虑因素的不断冲击，我们很容易放弃自己的工作，去寻找另一份工作。前进的步伐如此之快，很难跟上，更不用说感觉我们在事物发展的顶端。

然而，值得庆幸的是，一切都没有失去。通过后退一步，专注于重要的事情，将体验作为一个连续体，并仔细考虑我们如何(以及何时)向浏览器提供特定的特性和功能，我们可以避免大多数问题。

这减轻了测试过程中的一些压力(和挫折),让我们可以在更广泛的设备和浏览器上进行测试，这意味着我们将能够为更多的用户提供可靠的体验，无论他们当时碰巧使用了什么样的技术奇迹。

每个人都赢了。

## JavaScript 的更多实践

微软有许多关于开源 JavaScript 主题的免费学习，我们的任务是用 [Microsoft Edge](http://blogs.windows.com/msedgedev/2015/04/29/introducing-microsoft-edge-the-browser-built-for-windows-10/?WT.mc_id=13404-DEV-sitepoint-article28) 创造更多。以下是一些要检查的:

*   【2015 年微软 Edge 网络峰会(关于新浏览器、新网络平台功能和社区演讲嘉宾的完整系列)
*   //BUILD/和 Windows 10 的构建(包括网站和应用的新 JavaScript 引擎)
*   [在不破坏网络的情况下推进 JavaScript](http://channel9.msdn.com/Events/WebPlatformSummit/2015/Advancing-JavaScript-without-breaking-the-web?WT.mc_id=13404-DEV-sitepoint-article28)(Christian heil Mann 最近的主题演讲)
*   [托管网络应用和网络平台创新](http://channel9.msdn.com/Events/Build/2015/2-665?WT.mc_id=13404-DEV-sitepoint-article28)(深入探讨流形等主题。JS)
*   [让你的 HTML/JavaScript 更快的实用性能技巧](http://channel9.msdn.com/Series/Practical-Performance-Tips-to-Make-Your-HTMLJavaScript-Faster/06?WT.mc_id=13404-DEV-sitepoint-article28)(从响应式设计到休闲游戏再到性能优化的 7 部分系列)
*   现代网络平台快速启动(HTML、CSS 和 JS 的基础)

以及一些免费的入门工具: [Visual Studio Code](https://code.visualstudio.com/?WT.mc_id=13404-DEV-sitepoint-article28) 、 [Azure 试用版](http://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=13404-DEV-sitepoint-article28)和[跨浏览器测试工具](http://dev.modern.ie/?utm_source=SitePoint&utm_medium=article28&utm_campaign=SitePoint)——都适用于 Mac、Linux 或 Windows。

本文是微软网站开发技术系列的一部分。我们很高兴与您分享[微软 Edge](http://blogs.windows.com/msedgedev/2015/04/29/introducing-microsoft-edge-the-browser-built-for-windows-10/?WT.mc_id=13404-DEV-sitepoint-article28) 和新的 [EdgeHTML 渲染引擎](http://blogs.windows.com/msedgedev/2015/02/26/a-break-from-the-past-the-birth-of-microsofts-new-web-rendering-engine/?WT.mc_id=13404-DEV-sitepoint-article28)。在 [modern 获得免费虚拟机或在您的 Mac、iOS、Android 或 Windows 设备上进行远程测试。即](http://dev.modern.ie/?utm_source=SitePoint&utm_medium=article28&utm_campaign=SitePoint)。

## 分享这篇文章*