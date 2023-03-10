# JavaScript: 2016 年回顾

> 原文：<https://www.sitepoint.com/javascript-2016-in-review/>

![JavaScript in review, 2016](img/0fbc13d624291271e8062b227a81d9e6.png)

*这篇文章由[琼·尹](https://github.com/newjs)、[斯科特·莫利纳里](https://www.sitepoint.com/author/smolinari/)和[朱利安·莫茨](https://www.sitepoint.com/author/jmotz/)进行了同行评议。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

2016 年是具有纪念意义的、奇异的、有点欢乐/恐怖的一年，这取决于你的观点。与其他事件相比，专注于 JavaScript 似乎无关紧要，但这是每个 web 开发人员工作生涯中的一个重要部分。

[JavaScript 人气持续爆发](http://www.modulecounts.com/)。不是每个人都喜欢这门语言，但你很少听到十年前的嘲笑。就我个人而言，我一直喜欢 JavaScript——即使是在最初令人沮丧的几年。那些从 C++、Java 或 PHP 方向接近它的人一开始可能会感到困惑:JavaScript 看起来很熟悉，但并不熟悉。克服你的假设，你会欣赏它的简单优雅，实用性和灵活性。(也就是说，*日期处理仍然是一场噩梦！*)

JavaScript 在五月庆祝了它的 21 岁生日，所以让我们回顾一下它成熟的第一年…

## ECMAScript 演变

ES6/2015 是该语言诞生以来最重要的更新。该规范花了七年时间才完成，但是浏览器和运行时终于开始支持[箭头功能](https://www.sitepoint.com/javascript-arrow-functions/)、`let`、`const`、代理和更多的乐趣。[ES6 兼容表变成了绿色](http://kangax.github.io/compat-table/es6/)。

如果你支持老版本的浏览器，也许完全切换到 ES6 还为时过早。我说的旧版本是指一年前发布的任何东西。你可以使用 ES6-to-ES5 编译器，比如 [Babel](https://babeljs.io/) ，但是如果不引入额外的构建步骤，开发就足够复杂了。

ES7/2016 与其说是革命，不如说是进化。一个令人兴奋的新特性是 [async](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function) ，它允许以同步方式编写异步代码，而没有回调或承诺的语法复杂性(这一直困扰着我)。

## 渐进式网络应用

2016 年我最喜欢的基于 JavaScript 的技术被授予渐进式网络应用。pwa 在[谷歌 2015 年 Chrome Dev 峰会](https://developers.google.com/web/shows/cds/2015/progressive-web-apps-chrome-dev-summit-2015)上公布，但稳定的技术和工具最终在 7 月份的 Chrome 52 上发布。pwa 允许离线优先功能，并取代了脆弱的 AppCache 方法。Web 应用终于可以与本地应用竞争，并提供以下优势:

*   主屏幕图标
*   快速启动和自定义闪屏
*   敏捷的执行
*   没有互联网连接的脱机功能
*   URL、链接和书签
*   全屏或主题界面
*   沙盒执行
*   具有同步功能的本地和/或基于云的存储
*   更少的设备空间和处理资源
*   更好的安全(HTTPS 是先决条件)
*   从任何搜索引擎轻松发现
*   安装前请先试用
*   更简单的部署:*它只是一个 web 应用*
*   没有 AppStore 废话:你的应用程序可以包含任何你想要的裸露和脏话，而没有人要求你 30%的利润！

最重要的是:任何网站或应用程序都可以在几个小时内转换成 PWA。步骤:

1.  **在您的服务器上启用 HTTPS** 。
2.  **创建一个应用清单**——在你的应用根目录下创建一个 JSON 文件，定义名称、颜色、图标和显示选项。
3.  **创建一个服务工作者**——在你的根目录下创建一个 JavaScript 文件，它拦截网络调用，并在必要时返回缓存或实时数据。

现在还为时尚早，例子很少，但是 PWAs 提供了一个惊人的机会来“移动化”你的网络应用。诚然，不能保证苹果会实现这项技术，但这没关系:你的应用程序仍然可以在 Safari 中运行，只是不会从离线执行中受益。我有一种感觉，一旦 Android 上的网络体验变得明显更好，苹果将会被鼓励支持 PWAs。

有关更多信息，请参见 [Dev。Opera 的渐进式网络应用:权威的资源集合](https://dev.opera.com/articles/pwa-resources/)和[谷歌的 PWA 指南](https://developers.google.com/web/progressive-web-apps/)。

## 框架固定

很难做出不带偏见的判断，但今年 [React](https://facebook.github.io/react/) 似乎受到了最多的关注。你可能不同意；这取决于你在使用什么，你在哪里浏览，你和谁交谈过！

[Vue.js](https://vuejs.org/) 人气大增，9 月份发布了 2.0 版本。

AngularJS 可能失去了一些它在 2015 年获得的势头，但随着 Angular 2 在 9 月份的发布，这种情况可能会发生变化。新版本完全是重写的；它不能向后兼容 1.0 版。

新的框架和库令人兴奋，但是十年前的 jQuery 仍然很强大。6 月 9 日发布了 3.0 版本，7 月 7 日又发布了 3.1 版本。该库现在以严格模式运行，支持承诺，并应用了各种修复。(查看[升级指南](http://jquery.com/upgrade-guide/3.0/)以了解变更的完整列表。)

96.4%使用 JavaScript 的网站采用 jQuery。相比之下，Angular——最常用的*现代*框架——只有 0.5%的份额。jQuery 1.x 是最受欢迎的版本，占[安装](https://w3techs.com/technologies/details/js-jquery/all/all)的 93.5%。2.x 版本紧随其后，为 6.0%，3.x 版本为 0.5%。

我一直对开发者在每个项目中默认使用 jQuery 持批评态度。当更合适的选项或一点普通的 JavaScript 就足够了时，它可能会被过度使用。然而，它提供了一个更浅的学习曲线，比大多数更灵活。要让另一个框架或库取代 jQuery 还需要很多年。

## API 滥用

啊，电池状态 API。当我在 2013 年写下它的时候，它看起来非常有用；当你的应用程序检测到用户的手机即将没电时，还有什么比负责任地最小化网络请求和处理更好的呢？

不幸的是，Mozilla 估计约有 6%的网站使用该 API，但大多数是广告商检测(相当)独特的电池状态，并跟踪用户在不同领域的导航。当某些服务知道用户更加绝望时，它们也有可能提高价格…

虽然这本身不是 JavaScript 或 API 的问题，但出于隐私原因，Mozilla 采取了前所未有的措施[从 Firefox 53](https://www.fxsitecompat.com/en-CA/docs/2016/battery-status-api-has-been-removed/) 中删除了电池状态 API。不太可能出现在 iOS 设备中；其他因类似原因而面临风险的 API 包括[接近传感器](https://www.w3.org/TR/proximity/)和[蓝牙](https://developer.mozilla.org/en-US/docs/Web/API/Web_Bluetooth_API)。这是一个遗憾:这些 API 有实际的好处，我希望隐私问题可以在未来的版本中得到解决。

## 新节点

一年两次的 Node.js 时间表在四月给了我们 6.0 版本，在十月给了我们 7.0 版本。

尽管 W3Techs 报告称 Node.js 服务器的使用率仅为 0.2%(T1)，而 PHP 的使用率为 82.3%(T3)，但该平台仍呈上升趋势。这些数字可能有点误导，因为 Node.js 即使安装了也不一定标识自己。

没有服务器端运行时可能赶上 PHP:它已经领先很长时间，并且仍然是主机提供的最实用的选择。然而，Node.js 正在开辟自己的道路，并被各种语言信仰的开发人员广泛使用。

## 纱线中的纱线

[我喜欢 npm](https://www.sitepoint.com/10-npm-tips-and-tricks/) ，认为它是 Node.js 工具爆炸的首要原因之一。我从来没有经历过太多的问题，但是我没有参与像脸书这样大规模的项目。

脸书的工程师们在 10 月份发布了 Yarn。这是一个新的 Node.js 包管理器，旨在比 npm 更快更稳定。它依赖于 npm 注册表，因此应该保持完全兼容。

Tim Severien 的 [Yarn vs npm:你需要知道的一切](https://www.sitepoint.com/yarn-vs-npm/)描述了 Yarn 提供的好处。我同意他的结论:

> 尽管 Yarn 不是一个分叉，但它改善了 npm 的几个缺陷。如果 npm 从中吸取教训，让脸书、谷歌和其他 Yarn 贡献者帮助改进 npm，这不是很酷吗？

## 厌倦了疲劳

2016 年的《我再也受不了了》一文是 Jose Aguinaga 的[2016 年学习 JavaScript 的感受](https://hackernoon.com/how-it-feels-to-learn-javascript-in-2016-d3a717dd577f#.e4rkosi1w)。亚军:【dayssincelastjavascriptframework.com】T2。

这些可能是对 JavaScript 当前状态的幽默描述，但是要跟上最新的趋势、框架和建议越来越难了。开发人员在面对过多的评估选项时会很纠结。

我的建议是:*不要试图跟上*。不可能的。无论你今天赌什么系统，明天都会被更好的系统取代。为你的项目选择一个选项，坚持下去，除非工作变得难以为继。

只有一件事是绝对确定的:JavaScript 本身。首先学习语言，然后不断积累你的知识。您的经验将帮助您了解每个框架是如何运作的，以便您可以做出明智的选择。这个选择可能是完全放弃框架。

*新年快乐！*

## 分享这篇文章