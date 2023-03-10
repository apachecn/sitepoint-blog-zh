# 上线前的跨浏览器测试清单

> 原文：<https://www.sitepoint.com/cross-browser-testing-checklist/>

*本文最初发表于 [LambdaTest](https://www.lambdatest.com/blog/cross-browser-testing-checklist-before-going-live/?utm_source=Sitepoint&utm_medium=blog2&utm_campaign=SitepointAd1&utm_term=Sitepoint) 。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

当你开发一个网站，上线就像梦想成真。但是在发布后，你发现事情变得不对劲，这很快就变成了一场噩梦。我的一个朋友非常兴奋，因为他即将推出他的新网站。但是，当他最终按下闪亮的启动按钮时，一些不寻常和令人担忧的趋势开始出现。在深入研究了谷歌分析的细节后，他发现该网站在移动设备上的跳出率非常高。

移动设备上的网站一团糟，所有的元素都离开了它们的位置，徽标甚至不适合屏幕。那一天给他上了宝贵的一课，他把这一课传授给了我，“在上线之前，检查一下跨浏览器测试的清单”。

在这个数字世界中，跨浏览器测试是非常必要的，因为每个人都在不同的平台、操作系统、浏览器上浏览网页，而你甚至无法事先想到这一点。制定一个完美的[跨浏览器测试策略](https://www.lambdatest.com/blog/cross-browser-testing-strategy-explained-in-three-easy-steps/?utm_source=Sitepoint&utm_medium=blog2&utm_campaign=SitepointAd1&utm_term=Sitepoint)可能会对你有所帮助，但有时即使在那之后，你也需要为一些不受欢迎的错误做好准备。然而，一份合适的清单可能会帮助你避免这些问题，或者在别人之前发现它们。

### 查看核对表前的一些先决条件

在进入检查表之前，您需要确保您知道如何执行跨浏览器测试。

1.  如果你要进行跨浏览器测试，你需要知道你要在上测试什么[浏览器和设备。所以，制定一个合适的跨浏览器测试策略。](https://www.lambdatest.com/blog/5-tips-on-choosing-the-right-browser-list-for-cross-browser-testing/?utm_source=Sitepoint&utm_medium=blog2&utm_campaign=SitepointAd1&utm_term=Sitepoint)
2.  当你有了跨浏览器测试策略，确保在上线之前，你在跨浏览器测试工具上测试了你的本地托管网站或开发网站，比如 T2 的 LambdaTest T3。该平台有一个名为 Lambda Tunnel 的功能，让您可以灵活地连接本地托管的网站或 web 应用，以便使用 SSH 隧道在云中进行跨浏览器测试。跨浏览器的兼容性和跨浏览器的可访问性也会影响网站的 SEO，因此对网站进行彻底测试并与搜索引擎上的网站索引完美兼容总是很重要的。
3.  将移动设备放在手边，或者您也可以设置仿真器或模拟器。或者你可以使用一个能提供你需要的所有设备的平台，比如 LambdaTest，它提供了大量的 iOS 和 Android 移动设备供你测试。

完成先决条件后，下一步是检查清单。

### 上线前跨浏览器测试的终极清单

这份有用的清单将帮助您确保在您的本地环境中投入使用之前，您已经做好了准备，测试了所有的各种元素。

#### 所有浏览器中元素的对齐方式

确保元素处于您希望它们处于的正确位置。

#### 在各种浏览器中验证 SSL

![SSL certificate error in Windows-XP-IE-8](img/a0f623dd454c1697c20cc9c17a0dc63a.png)

如果您遇到此错误，原因之一可能是您的网站的 SSL 证书不支持某些浏览器版本。如果你的用户试图访问你的网站，那么他们可能根本无法访问。所以，在上线之前，在所有浏览器中检查你的网站的 SSL 证书。

#### 不同浏览器中的字体渲染

谁不喜欢他们网站上漂亮的字体，但是如果他们没有正确的渲染，他们会犯错误。字体的呈现受浏览站点的浏览器的影响很大。因此，您需要确保您的字体在每个 web 浏览器中呈现相同的效果。

阅读有关[字体和浏览器兼容性](https://www.lambdatest.com/blog/typography-and-cross-browser-compatibility/?utm_source=Sitepoint&utm_medium=blog2&utm_campaign=SitepointAd1&utm_term=Sitepoint)的更多信息。

#### 媒体元素与不同浏览器的兼容性

视频是当今最受欢迎的媒体形式。网页设计者和开发者一直在利用这一事实，你可以很容易地在网站主页上找到演示视频或一些教程视频。但是，如果你使用不支持的媒体元素，浏览器兼容性会给你带来麻烦，而且不仅限于视频，还有图像。因此，在上线之前，请确保您使用所有浏览器都支持的元素，或者您有一个不受支持的元素的后备，以便您的用户不会遇到这种情况！

![unsupported video format](img/3f11a436d702c2b8f925cb12e41a3c08.png)

在这里详细了解[与不同浏览器](https://www.lambdatest.com/blog/incompatible-multimedia-formats-to-look-out-for-in-2018/?utm_source=Sitepoint&utm_medium=blog2&utm_campaign=SitepointAd1&utm_term=Sitepoint)的多媒体兼容性。

#### 你的 API 在所有浏览器中都连接了吗？

API 调用也依赖于浏览器。有些浏览器会确认 API 请求，而其他浏览器可能会避免这种情况，或者向您抛出一个错误。在上线之前，一定要确保你正在使用的 API 在每个浏览器中都是连接的。因为有一些像 Opera Mini 这样的浏览器不支持 Websocket 这样的 API。

还有一些 API 调用方法像`getUserMedia/Stream`会在 Opera Mini，iOS Safari 10.3，IE 11 上给你抛出错误。因此，在上线之前，你需要确保你的[API 与](https://www.lambdatest.com/blog/api-and-browser-compatibility/?utm_source=Sitepoint&utm_medium=blog2&utm_campaign=SitepointAd1&utm_term=Sitepoint)浏览器兼容，否则当你在公共服务器上时，你的用户可能会面临与世界脱节的情况。

#### 确保你验证了你的 CSS 和 HTML

开放的标签对开发人员来说可能是噩梦，如果用户在屏幕上看到代码，他们可能会感到恐惧。因此，必须确保代码是干净的，并且经过适当的验证。W3schools 也有同样的软肋。在使用 W3C 标记验证服务、自由格式化程序或 JS 格式化程序、W3C CSS 验证服务–w3jigsaw 或 CSS 验证服务等工具之前，您可以轻松验证您的 HTML、JS 和 CSS。

使用这些工具可以在更大程度上发现浏览器兼容性问题，您可以进一步解决这些问题。

阅读更多关于[在 HTML 和 CSS 中发现跨浏览器兼容性问题](https://www.lambdatest.com/blog/finding-cross-browser-compatibility-issues-in-html-and-css/?utm_source=Sitepoint&utm_medium=blog2&utm_campaign=SitepointAd1&utm_term=Sitepoint)的内容。

在检查完所有主要的兼容性问题后，您需要执行一轮跨浏览器测试，通常会考虑一些次要但重要的因素，如:

*   **元素对齐:**所有的元素都按照你想要的方式对齐了吗？
*   **弹出窗口:**检查弹出窗口是否正确显示，并在所有浏览器中打开。
*   **复选框对齐:**复选框在很多浏览器中都会导致问题。确保您的复选框对齐并处于正常工作状态。
*   按钮的对齐和功能:按钮在 CTA 或任何其他操作中起着重要作用，所以你需要确保它们在不同的浏览器中对齐并正常工作。
*   **按钮的 URL 重定向:**检查按钮重定向到的链接。
*   **下拉菜单:**验证下拉菜单在所有浏览器中都能正常工作。
*   **表单和表单 API:**确保表单接收数据，并且数据被完整地传输到收集 API 端点。
*   **网格/表格:**检查表格和网格在每个浏览器中的对齐和位置。
*   **会话和 cookies:** 如果您的网站使用 cookies，请验证提示是否存在，并且是否正常工作。
*   **日期:**测试日期格式是否符合每个浏览器的要求。
*   **放大和缩小功能:**检查放大和缩小功能是否正常工作，并且在运行时不会破坏用户界面。
*   **滚动条外观:**检查滚动条是否同时出现在水平和垂直条上，并且功能正常。
*   **Flash:** 确保 Flash 支持的视频、动画、RIA 和应用程序能够跨浏览器工作。
*   HTML 动画:验证你的动画是否能在所有浏览器上加载。
*   **鼠标悬停:**检查鼠标是否相应调整到按钮、文本框、链接、空白。
*   **图像对齐:**确保所有的图像在不同的浏览器中对齐并就位。
*   Alt 标签: Alt 标签也很重要，我们需要确保它们到位。

以及你能想到的所有可能的浏览器、操作系统和设备组合。

正如我们在这里看到的，有各种各样的东西需要在一个本地环境中通过数千种组合进行测试，以确保网站上线后不会妨碍用户的体验。因此，我们需要确保测试所有可能的组合，以避免进一步的意外。因为接下来的许多测试都是重复且耗时的，所以我们可以利用在线 Selnium 网格来自动化跨浏览器测试。有了这样的自动化，您可以避免重复和耗时的任务，并使用简单的脚本自动完成它们。

LambdaTest 还为您提供了一个[在线 selenium grid](https://www.lambdatest.com/selenium-automation?utm_source=Sitepoint&utm_medium=blog2&utm_campaign=SitepointAd1&utm_term=Sitepoint) ，您可以在这个网格上自动化您在公共和本地服务器上的测试，并在超过 2000 个设备、浏览器、浏览器版本和分辨率组合的 LambdaTest 云网格上进行测试。因此，你需要确保你找到了最好的跨浏览器测试工具，然后你可以在你的清单上执行测试，一些是手动的，而另一些是自动的。

一旦你完成了这个跨浏览器测试清单的测试，你就可以点击绿色按钮了。我希望你不要像我朋友一样面对这种情况，因为你足够聪明，不会重复同样的错误。

如果我遗漏了什么，请在下面的评论区告诉我们！

在那之前，祝测试愉快，并祝发布顺利！

## 分享这篇文章