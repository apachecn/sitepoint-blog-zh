# 2022 年你需要的 8 个跨浏览器测试工具

> 原文：<https://www.sitepoint.com/cross-browser-testing-tools/>

*本文是与 [LambdaTest](https://www.lambdatest.com/?utm_source=Sitepoint&utm_medium=blog&utm_campaign=SitepointAd1&utm_term=Sitepoint) 合作创作的。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

*本文于 2019 年 4 月第三次更新，以反映来来去去的跨浏览器测试工具，同时也反映当今浏览器的使用统计。*

首先，什么是跨浏览器测试？

跨浏览器测试是在用户今天使用的所有常见 web 浏览器中测试 web 应用程序和网站的形式，这确保了我们在任何地方都提供一致的用户体验，而不仅仅是我们喜欢的 web 浏览器。以下是一些需要注意的事项:

*   代码验证:有些浏览器会报告代码错误吗？
*   性能:网站运行缓慢，甚至导致崩溃？
*   响应式设计:设计是否始终具有响应性？
*   UI 不一致:还有其他设计缺陷吗？
*   其他奇怪的行为:还有其他简单的不工作吗？

### 如果我不测试会怎么样？

不一致其实很正常。事实是，所有网络浏览器的行为和呈现网站的方式都略有不同，有些浏览器甚至可能不支持我们最初打算利用的功能；当这些不一致出现时，会对我们的收入产生直接影响。

让我们以电子商务为例。 [69.89%](https://baymard.com/lists/cart-abandonment-rate/) 的退房被放弃，[其中 17%](https://baymard.com/lists/cart-abandonment-rate/#why-users-abandon) 归因于网站错误和崩溃。假设一家企业每年将增加 50 万份销售额，那么由于跨浏览器测试可能导致的错误和崩溃，就损失了 59，407 份销售额。

### 我应该在哪些浏览器上测试？

由于微软宣布他们将放弃自己的 EdgeHTML 和 Chakra 引擎，转而支持广泛采用的 Blink 和 V8 引擎，这意味着今天许多主流浏览器都提供类似级别的代码兼容性。虽然从健康竞争的角度来看，这是一种倒退，但这确实意味着如果一个网站可以在谷歌 Chrome 上运行，它很可能会在 Brave、Opera 和即将推出的微软 Edge 上运行。再加上微软已经指示我们停止使用 Internet Explorer，跨浏览器测试比以往任何时候都更容易，只有 Safari 和 Firefox 使用自己的引擎。

从技术上来说，我们今天应该支持的网络浏览器是我们的用户和客户正在使用的浏览器，使用谷歌分析或其他类型的网络分析跟踪软件很容易找到这些信息。但是如果你没有那种可用的数据，这里有[的全球统计数据](https://en.wikipedia.org/wiki/Usage_share_of_web_browsers) *:

*   铬合金:61.75%
*   狩猎旅行:15.12%
*   火狐浏览器:4.92%
*   加州大学:4.22%
*   歌剧:3.15%
*   互联网浏览器:2.8%
*   三星互联网:2.74%
*   微软 Edge: 2.15%

**截至 2018 年 11 月。*

此外，请记住，跨多个操作系统的每个 web 浏览器都有多个版本。听起来吓人吗？不尽然，但是在所有网站上测试太无聊了！

幸运的是，现在有很多优秀的跨浏览器测试工具，所以今天我们来看看其中 7 个最好的。

## [浏览器堆栈](https://www.browserstack.com/)

众所周知的 [BrowserStack](https://www.browserstack.com/) 可以立即访问运行在真正的 Android 和 iOS 设备上的 2，000 多种网络浏览器，让开发人员和其他利益相关者参与跨浏览器测试，无论是捕捉几张截图，实时调试错误，还是与浏览器进行本机交互，并查看窗口调整大小时的布局如何。不需要在模拟器和仿真器上妥协，BrowserStack 为您提供完全的控制，因为您将在远程机器上与真正的浏览器进行交互。

这不是*像*一样真实的东西，它*是*真实的东西。

BrowserStack 还支持 [Selenium](https://www.browserstack.com/selenium) ，这是一个开源工具，可以帮助您自动执行各种测试，因此您不必手动执行。

尽管上述功能对于跨浏览器测试工具来说是相当标准的，但真正让 BrowserStack 与众不同的是，你可以从 12.50 美元/月开始，这是一项针对自由职业者的计划——提供更轻量级的功能。

## [大头针](https://www.headspin.io/?ref=sitepoint)

[HeadSpin](https://www.headspin.io/?ref=sitepoint) 是一个 AI 驱动的测试和 DevOps 协作平台。它能够深入洞察应用的性能，为开发、QA、运营和产品团队提供支持，以确保用户获得最佳体验。

借助 HeadSpin，您可以提供迄今为止最好的用户体验！该平台提供对关键性能 KPI 的洞察，并从几十个角度了解您的用户体验——用户看到的内容、边缘利用率、系统性能、代码执行等。自动执行发布前和发布后的功能和性能测试，轻松发现堆栈所有层的表面问题和根本原因，从量化音频/视频 QoE 到代码和数据包可见性。

HeadSpin 的智能在问题变成问题之前就将其暴露出来，客户已经看到了令人印象深刻的结果:花费在新版本上的 QA 时间减少了 60%，开发周期加快了 68%，应用内加载时间加快了 30%，生产问题减少了 90%。

像 Tinder、迪士尼、Telstra 和 Pinterest 这样的顶级公司都使用 HeadSpin。

![](img/7b69863a9743ef22d6a8ab5e3d40a307.png)

## [交叉浏览器测试，由 SmartBear](https://crossbrowsertesting.com/)

[smart bear 的 CrossBrowserTesting】通过 Selenium 提供手动和自动测试，1500 多个移动和桌面远程浏览器，并具有与 BrowserStack 类似的订阅设置(不包括他们的“自由职业者计划”)。凭借对浏览器扩展和开发工具(如 Chrome Dev Tools 和 FireBug)的完全访问，使用滑动动作等进行交互的能力，以及本机调试前端错误的手段，CrossBrowserTesting 与 BrowserStack 并无太大区别。](https://crossbrowsertesting.com/)

CrossBrowserTesting *和* BrowserStack 还支持用户比较版本(直播或截图)，一次运行多个测试(或截图)，甚至共享结果。

All-in-all，BrowserStack 的一个合适的替代品，但是如果支持的浏览器数量对你很重要，我会选择 browser stack(browser stack 支持额外的 500 个)。

![](img/1ef88c774c9c4c47abb194d87e7b95fc.png)

## [LambdaTest](https://www.lambdatest.com/?utm_source=Sitepoint&utm_medium=blog&utm_campaign=SitepointAd1&utm_term=Sitepoint)

有了无限制的实时浏览器测试、无限制的自动化测试、无限制的响应测试、无限制的截图测试和 24×7 支持，您在使用 [Lambdatest](https://www.lambdatest.com/?utm_source=Sitepoint&utm_medium=blog&utm_campaign=SitepointAd1&utm_term=Sitepoint) 时将不会有丝毫的限制方面的担忧。最低的计划起价仅为 15 美元/月，Lambdatest 提供了最佳性价比。事实上，甚至有一个高功能的“终身免费”选项，使它成为当今市场上最容易使用的跨浏览器测试工具之一，非常适合那些第一次担心跨浏览器测试的人。

作为一个额外的奖励，所有选项包括免费的自动化分钟；也就是说，如果你不选择他们强大的自动化选项！

所有选项还包括:

*   2000 多种本地测试浏览器
*   使用测试日志跟踪问题
*   本地测试功能
*   自动化屏幕截图测试
*   响应和视觉比较测试
*   访问 WordPress 和 Chrome 扩展
*   与特雷罗、阿萨纳、吉拉和斯莱克的整合

简而言之，LambdaTest 自动化平台是一个[在线 selenium 网格](https://www.lambdatest.com/selenium-automation?utm_source=Sitepoint&utm_medium=blog&utm_campaign=SitepointAd1&utm_term=Sitepoint)，它让您能够在 2000 多个浏览器环境中运行您的测试脚本。

![](img/e7cc9034a84be2457494f072959da94e.png)

## [浏览](https://www.browserling.com/)

尽管它是最便宜的跨浏览器测试工具之一，每月 19 美元(价值仅次于 Lambdatest)，但不要让 [Browserling](https://www.browserling.com/) 的朴实网站欺骗了你。如果自动化测试对您的团队来说不是很重要，Browserling 是一个相当划算的选择。

此外，他们还有 Chrome、Firefox、Opera 和 Safari 的扩展功能。

![](img/aaf403fe4969ee14d206a7d687923c06.png)

## [实验测试](https://experitest.com/)

Experitest 提供了相当标准的功能，但降低了成本，起价为 9 美元/月，甚至还有一个像 Lambdatest 这样有吸引力的免费增值选项。也就是说，Experitest 在 Lambdatest 上感觉不那么耀眼，所以只有在你预算有限的情况下，Experitest 可能是更好的选择。

![](img/38534eed3feccc0d34a8f570fec2bba0.png)

## [功能化](https://www.functionize.com/cross-browser-testing/)

将的跨浏览器测试工具与竞争对手区分开来的是它对人工智能的大量使用，以及如果你为 *one* 浏览器设置测试，就没有必要为其他人重新编码测试，这可以节省相当多的时间。它们都是关于自主测试的(使用它们的 Adaptive Event Analytics 技术)，因此您可以花更多的时间来分析结果。

没有具体说明 Functionize 支持多少浏览器，但是，它们提供了与其他跨浏览器测试工具相同的功能标准，如代码调试和可视化测试。

仅供参考:他们的跨浏览器测试工具是作为一个更大平台的一部分提供的，其价格并未公开。

![](img/a2e50507d4414a9b02e9c12b2c77e0d9.png)

## [酱实验室](https://saucelabs.com/)

尽管提供了与其他跨浏览器测试工具类似的功能， [Sauce Labs](https://saucelabs.com/) 起价 89 美元/月(用于在真实设备上测试)。虽然他们已经存在了一段时间，并声称拥有“世界上最大的持续测试云”，所以如果你有预算，值得一试。

![](img/fbfdbba55007875aec237fb80c9028b5.png)

## 结论

尽管目前市场上的大多数跨浏览器测试工具在所提供的功能方面保持了非常高的标准，允许开发团队增加他们的测试覆盖范围，并在所有设备和屏幕尺寸上提供更加一致的用户体验，但其中许多工具至少有一个小小的特点，使他们从竞争对手中脱颖而出。

无论是 Functionize 和他们的人工智能驱动的前端测试方法，Experitest 的竞争性定价，还是 BrowserStack 针对自由职业者的低成本选项，这些工具中的一个肯定能满足您团队的需求。如果你正在寻找一个令人敬畏的全能选择，Lambdatest 似乎提供了最好的性价比。

## 分享这篇文章