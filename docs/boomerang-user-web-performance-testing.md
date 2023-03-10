# 性能测试带着回旋镖回来了

> 原文：<https://www.sitepoint.com/boomerang-user-web-performance-testing/>

雅虎的回旋镖是我最近发现的最有趣和最具创新性的项目之一。开发人员经常使用诸如 [Firebug](http://getfirebug.com/) 和 [YSlow](http://developer.yahoo.com/yslow/) 这样的工具来测试页面和脚本的性能。这些工具通常可以测量加载时间和延迟，以确保您的页面保持响应。

不幸的是，你永远无法确定你的实验室测试代表了真实世界的条件。你可能在连接到 100 兆宽带管道的新 Mac Book 上使用 Chrome，但如果你的大多数用户坐在世界的另一端，在 Windows 98 上通过拨号连接使用 IE4 会怎么样？*(好吧，这不太可能，但你明白了！)*

进入 Boomerang——一个新的雅虎 JavaScript 项目，测量实际用户体验的响应时间。该工具可用于测量页面加载时间、带宽、延迟、Ajax 调用、小部件加载时间等等。收集的数据被传递回服务器上的单个信标 URL，在那里可以存储和分析这些数据。

你应该注意到，Boomerang 不像 YSlow 那样是一个安装即可使用的解决方案——你需要定义你自己的测试参数、存储和报告。然而，我怀疑易于使用的托管版本的应用程序将出现在不久的将来。

Boomerang 是一个测试版，但现在可以免费使用。更多信息，请阅读 Boomerang 文档，[下载文件](http://github.com/yahoo/boomerang/archives/master)，或者[在 github 浏览项目](http://github.com/yahoo/boomerang/)。

你觉得回旋镖有用吗？你会在你的系统上尝试吗？

## 分享这篇文章