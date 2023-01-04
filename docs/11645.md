# 谷歌浏览器框架:技术细节

> 原文：<https://www.sitepoint.com/google-chrome-frame-technical-details/>

![Google Chrome Frame](img/e2f282f05dd0adf651f6e45509d2119f.png) **谷歌 Chrome 框架**是第一个解决[ie 6.0](https://www.sitepoint.com/ie6-upgrades-google-chrome-frame/)问题的现实解决方案。它允许企业用户保留传统系统的 IE，但在新应用中提供增强的功能*(稍后将详细介绍)*。

过去几天一直在测试 Chrome Frame，印象深刻。

## 镀铬框架安装

安装 Chrome Frame 就像安装 Flash、PDF viewer 或任何其他 IE 插件一样简单。完整的 10MB 应用程序在几分钟内下载并安装完毕。这个过程很快，没有痛苦，并且不需要重启浏览器。

IE6、IE7、IE8 都有 Chrome 边框。我不相信许多 IE8 用户会需要它；速度和渲染问题在那个浏览器中比较少见。然而，该插件将允许开发人员使用 HTML5、CSS3、canvas 和 SVG 功能，这在以前技术上是不可能的。

## 在后台

Chrome Frame 作为浏览器辅助对象安装；扩展 IE 功能的 Windows DLL。bho 是一种用于添加工具栏和其他插件的标准方法，所以谷歌使用的是微软自己的文档平台。如果微软想干掉 Chrome Frame，在不影响其他插件的情况下，技术上很难实现。

bho 也被恶意软件和病毒开发者所利用。微软在 XP SP2 中通过沙盒 BHO 代码和引入允许用户禁用不道德代码的附加管理器修复了这个问题。因此，Chrome Frame 在 XP SP2 上至少需要 IE6。

## IE 集成

Chrome Frame 分享了 IE 的许多浏览器功能，比如书签、历史记录、cookies 和密码。这对最终用户来说是必不可少的；无论使用哪种渲染引擎，它都将保留他们的登录凭证。

不幸的是，其他插件可能会造成混乱。虽然 Flash 可能安装在 IE 中，但你也需要在 Chrome 中安装插件。这可能不是一个大问题；大量使用 Flash 的网站不太可能因为转向 Chrome 而受益。不过，我会建议谷歌调查流行插件的检测和自动安装。

## 速度和稳定性

令人困惑的是,《计算机世界》报道称，IE8 使用 Chrome 框架比使用 T1 快 10 倍。他们真正的意思是，与 [SunSpider JavaScript 基准套件](http://www2.webkit.org/perf/sunspider-0.9/sunspider.html)相比，Chrome Webkit 引擎比 IE 的 Trident 引擎快 10 倍。SunSpider 不是对页面渲染速度的真实测试，Chrome Frame 也不会影响 IE 的默认速度。

然而，Chrome 渲染的页面明显更快，它允许你运行 IE 不兼容的代码，比如优秀的 [JSNES JavaScript NES 模拟器](https://www.sitepoint.com/jsnes-javascript-nes-emulator/)。

Chrome Frame 仍处于测试阶段，存在一些问题。例如，我在 GMail 中遇到了渲染问题，尽管它在 IE6 和 Chrome 浏览器中都很好。

## 面向开发者的 Chrome 框架

如果下面的 meta 标签被添加到 HTML `head`中，IE 将切换到 Chrome 框架渲染:

```
<meta http-equiv="X-UA-Compatible" content="chrome=1">
```

此外，可以在 Chrome 框架中测试页面，方法是在完全限定的 URL 的开头添加“cf:”

[cf:https://www . site point . com/](cf:https://www.sitepoint.com/)

Webkit 开发工具包括在内，因此可以右键单击 Chrome 渲染的页面并选择“Inspect Element”。如果同样的工具可以用在 IE 的观点上那就太好了，但也许这要求有点过分！

## 可访问性、渐进增强和 Chrome 嗅探

我要发表一个会让一些开发者不高兴的声明。请坐下。准备好了吗？确定吗？开始了…

**Chrome 边框不允许你掉 IE6 支持！**

很多用户会继续使用没有 Chrome 边框的 IE6。很多公司会屏蔽插件。使用 IE 专用辅助技术的用户可能无法使用 Chrome Frame。

*那么这有什么意义呢？*

Chrome Frame 允许你实现[渐进式增强](https://www.sitepoint.com/progressive-enhancement-graceful-degradation-basics/)技术，支持旧浏览器，但改善现代浏览器的用户体验。例如，您的应用程序可能会生成图表目录。该表在 IE6 中仍然可见，但 Chrome Frame 用户还会看到一个彩色的画布生成的图表。

Chrome Frame 在安装时将“chromeframe”添加到 IE 用户代理字符串中，例如

```
Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; chromeframe)
```

因此，你可以使用服务器端代码来检测 Chrome 的存在，添加 HTML meta 标签，包含增强功能，或者显示插件下载页面的链接。Google 还提供了 [JavaScript Chrome 框架检测代码](http://code.google.com/chrome/chromeframe/developers_guide.html#Detecting_Google_Chrome_Frame)。

## 用户会采用 Chrome 边框吗？

Chrome Frame 仍在开发中，还有一些技术问题需要解决，所以我在现阶段不推荐它。然而，未来是有希望的。公司和私人用户可能不能或不愿意升级他们的浏览器，但是安装一个不显眼的插件要容易得多。

相关阅读:

*   [谷歌 Chrome 框架](http://code.google.com/chrome/chromeframe/)
*   [开发者指南](http://code.google.com/chrome/chromeframe/developers_guide.html)
*   [为什么企业不升级 IE6，Chrome Frame 能帮上什么忙](https://www.sitepoint.com/ie6-upgrades-google-chrome-frame/)
*   [谷歌用 Chrome 边框修复 IE6](https://www.sitepoint.com/google-chrome-frame-fixes-ie/)
*   [渐进增强和适度退化:概述](https://www.sitepoint.com/progressive-enhancement-graceful-degradation-basics/)
*   [渐进增强与优雅退化:做出选择](https://www.sitepoint.com/progressive-enhancement-graceful-degradation-choice/)

## 分享这篇文章