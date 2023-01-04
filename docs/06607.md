# Chrome 28 有什么新功能

> 原文：<https://www.sitepoint.com/chrome-28-whats-new/>

谁需要一个六周大的 Chrome 27？Chrome 28 已经到货，可以下载了。点击工具图标，然后是关于谷歌浏览器的**或前往 google.com/chrome 的。让我们来看看世界上最常用的浏览器[增加了什么](https://sitepoint.com/browser-trends-july-2013/)**

 **## 再见 Webkit，你好 Blink

Chrome 28 是第一个使用谷歌新的 Blink 渲染引擎的版本。在地址栏输入 [chrome://version/](//version/) 就会看到 Blink 版本 537.36。它被[歌剧 15](https://sitepoint.com/opera-15-launched/) 击败了，但是浏览器朋友之间的一周算什么？

Blink 是 Webkit 引擎的一个分支，您不太可能发现任何重大差异。许多 CSS3 和 JavaScript 属性仍然需要-webkit-前缀，呈现是相同的，我还没有发现新的 Web Inspector 特性。甚至用户代理也保持不变(不是说用户代理字符串有实际用途)…

```
Mozilla/5.0 (Windows NT 6.2; WOW64) AppleWebKit/537.36 (KHTML, like Gecko)
Chrome/28.0.1500.71 Safari/537.36
```

由于新的线程化 HTML 解析器和流水线页面加载，您可能会获得一点速度提升，但我怀疑这在现实世界中是否会引人注目。

## @支持支持

@supports 规则允许您测试 CSS3 中的浏览器特性支持，例如

```
>@supports (text-shadow: 0 0 5px #000) {
	.blur-text {
		color: transparent;
		text-shadow: 0 0 5px #000;
	}
}
```

该规则在 Firefox、Opera 12 和 Opera 15 中可用，因此它最终成为 web 开发人员的一个实用选项。

## 丰富的通知

或许最重要的新功能是 Chrome 应用和扩展的丰富通知。警告窗口可以直接在弹出窗口中显示格式化的文本、图像和动作按钮——更多信息请参考 API 文档和[示例应用程序](https://github.com/GoogleChrome/chrome-app-samples/tree/master/rich-notifications)。

该功能目前在 Windows 上可用，很快将支持 Mac 和 Linux。至于它是否会用于 Gmail 之外的其他用途，则是另一回事了。不过，对于一个 RSS 阅读器来说，这太棒了，谷歌！

## Android 上的 HTML5 全屏 API

你现在可以在你的手机版 Chrome 上启用一个浏览器[全屏 API](https://sitepoint.com/html5-full-screen-api/) 。这可能比桌面浏览器更有意义，对 HTML5 游戏开发者来说是一个额外的奖励。

除了 16 个已修复的安全问题，Chrome 28 就这些了。对于 web 开发人员来说没有什么特别令人兴奋的，但是看看 Blink 在接下来的几个月里如何发展会很有趣。

## 分享这篇文章**