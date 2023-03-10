# 社论:聚焦有趣的 JavaScript 项目

> 原文：<https://www.sitepoint.com/spotlight-on-interesting-projects/>

这是我最新的 JavaScript 时事通讯的社论，你可以在这里订阅。

偶尔在浏览网页时，我会遇到一些很酷的项目或库，它们做了一些我从未想过(或不知道可以)用 JavaScript 做的事情。我想这正好证明了阿特伍德定律:“任何可以用 JavaScript 编写的应用程序，最终都会用 JavaScript 编写。”。

最近，我遇到了一个名为 [Conkie](https://github.com/hash-bang/Conkie) 的项目。你们当中的 Linux 用户可能对启发它的软件很熟悉， [Conky](https://github.com/brndnmtthws/conky) ，这是一个小部件框架，用于将来自各种插件模块的信息显示到你的桌面上。这些模块通常显示系统统计数据，如 CPU 温度、硬盘使用情况和网络传输，但可以高度定制，以显示几乎任何类型的信息。

Conky 模块是用 Lua 脚本语言编写的，如果你不了解它，这可能是一个障碍。JavaScript 版本建立在[电子](http://electron.atom.io/)之上。如果您还不熟悉的话，Electron 是一个应用程序框架，它将 Node.js 与 Chromium 浏览器结合起来，允许您创建跨平台的桌面应用程序。

[![conkie-screenshot](img/4fca7c00289f2fbe14bb1a94bec0b80a.png)](https://uploads.sitepoint.com/wp-content/uploads/2016/09/1474298933conkie-screenshot-1024x576.png)

Conkie 的功能被划分为多个模块，用 JavaScript 编写，用于收集数据和主题，允许您使用 HTML 和 CSS 对输出进行样式化。这意味着我们 web 开发人员有能力使用我们最熟悉的技术来创建和定制小部件。默认主题使用 Angular (v1.x)和 Highcharts 来格式化输出，但是由于 Electron，您可以使用任何您想要的前端框架或可视化库。

Conkie 仍然是一个相对较新的项目(还不到一年)，仍然是一个正在进行中的工作。创作者，Matt Carter，似乎正在独自完成这项工作，并且正在寻找贡献者，所以如果你还没有尝试开源，这可能是一个很好的机会。除了帮助 Conkie 在 Mac 和 Windows 系统上工作，Matt 还寻求一些设计帮助，因此无论你的技能在哪里，都有机会参与进来。

作为开发人员，很容易陷入日复一日构建相似类型软件的窠臼，我认为看看这样的项目可以帮助激发您对 JavaScript 可以做什么的好奇心，甚至可能为您的下一个副业项目激发新的想法！我们希望突出更多有趣的 JavaScript 项目，所以如果你遇到一些不寻常或鼓舞人心的事情，请用#SpotlightOnJS 标签发 tweet us @SitePointJS。

## 分享这篇文章