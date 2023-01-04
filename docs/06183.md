# 基础 5

> 原文：<https://www.sitepoint.com/foundation-5/>

几天前发布了 Foundation 5。对于前端开发人员来说，这是一个完全响应的框架。基础框架由 Zurb 制作，于 2011 年首次发布。Foundation 5 声称是“世界上最先进的响应前端框架”，被数百万开发者和网站使用，包括国家地理、Mozilla、华盛顿邮报等。Foundation 在 2011 年发布时被认为是第一个完全响应的框架。

Foundation 4 是在大约九个月前发布的，现在 Zurb 发布了最新的版本 5，它有许多新的特性和改进。Foundation 5 是迄今为止最快也是最好的版本。它包含了帮助您比以往任何时候都更快地编码和学习的特性。基金会团队如是说。

Foundation 是一个完全响应的框架，它帮助开发人员和设计人员使用语义代码创建完全响应的漂亮网站。Foundation 使用移动优先的方法，所以你可以设计不仅在小设备上也能在大屏幕上工作的网站。

## Foundation 5 中的新功能

最新版本对用户来说更快，编码更快，学习更快。当屏幕大小达到某一点时，Foundation 的 interchange 插件允许您呈现不同的内容。例如，您可以在桌面上显示大型图像文件，在小型设备上显示小型优化图像。Foundation 5 为数百万用户提供了一些新功能。让我们来看看一些新功能。

## 新文档、论坛和基础业务

通过新的文档、详细的代码示例、新的启动指南和新的论坛，Foundation 的学习速度也更快。您可以快速了解如何开始使用 Foundation 5 及其工作原理。

**入门指南**

开始使用 Foundation 5 很容易。Foundation teams 创建了全新的部分，提供了新的更详细的代码示例。他们还编写了新的[“入门”](http://foundation.zurb.com/docs/)指南，帮助您了解有关 Foundation 5 的所有信息。你也可以在 [Codepen](http://codepen.io/anon/pen/FHwGB) 的浏览器里试试粉底。如果你来自 Foundation 4，你可以访问[基金会迁移指南](http://foundation.zurb.com/docs/upgrading.html)。

**新官方基金会论坛**

您现在还可以访问新的官方[基金会论坛](http://foundation.zurb.com/forum)，该论坛致力于与设计社区的问答和对话。

**基础业务**

如果您是企业用户，您现在可以依靠 [Foundation Business](http://foundation.zurb.com/business/services.html) ，在这里您将获得所有公司项目的专业支持、咨询、工具和培训。个人和企业计划可获得特优支持。

## 完全支持中等网格

根据大众的要求，Foundation 5 现在包含了中型网格。这是 Foundation 4.3 的一个实验性特性。所以，现在你有大中小网格可以用了。

## 新入门模板

使用全新的入门模板，您可以在几秒钟内快速启动下一个 Foundation 5 项目。基金会团队已经为您创建了 13 个布局，供您开始和建立。使用这些简单的 HTML 模板非常简单，只需[抓取代码](http://foundation.zurb.com/templates.html)并将其放在基础页面的`<body>`标签之间。博客、订阅源、横幅主页、联系人、营销和商店都有入门模板。你可以在[了解更多如何使用基础模板](http://foundation.zurb.com/templates.html)。

## 交换

Foundation 中的 Interchange 使用媒体查询来动态加载适合不同设备的响应内容。现在，您可以使用 Interchange 来获取 HTML 片段，这意味着您可以为小型、中型和大型设备加载完全不同的网页部分和内容(图像、CSS 和其他内容)。您可以使用此功能在小型设备上加载移动友好内容，在台式机上加载大型组件。

要使用 Interchange，您必须验证 jQuery 和 foundation.js 在您的页面上可用。你可以参考 JavaScript 文档来设置它。只需在 foundation.js 文件后添加 foundation.interchange.js 即可。

## 通过 jQuery 2、fast-click.js、GPU 加速等等，速度更快…

**再见 Zepto，你好 jQuery 2**

Foundation 5 使用了许多特性来提高速度。Foundation 转而使用 Zepto(带有 jQuery 选项),因为它的文件更小，加载速度更快，但是 Zepto 有一些性能问题。所以现在 Foundation 5 用的是 jQuery 2。

**移动用户快速点击**

随着 fastclick.js 在 Foundation 5 中的集成，移动用户将拥有更快的体验。FastClick 用于消除移动浏览器上物理点击和点击事件触发之间的 300 毫秒延迟。fast-click.js 的目标是让您的应用程序感觉不那么滞后，响应更快，同时避免对您当前的逻辑造成任何干扰。

**改进的 GPU 加速**

有了 GPU 加速，动画在 Foundation 5 中的表现会明显更好、更流畅。离画布特性提供了一种在屏幕中构建导航的强大方法。您可以将画布菜单放置在视口之外，并在激活时将其滑入。

**更强大的形态**

Foundation 5 中的表单也得到了改进。基金会的特点是一个易于使用，功能强大和多才多艺的形式布局系统。表单样式和基础网格的组合意味着您几乎可以做任何事情。

## 新的命令行工具和 Bower

Cli

基金会团队已经编写了他们自己的 CLI 包装器，因此您可以快速启动新项目。只需使用“基金会新项目名称”就可以了。

Foundation 已经开始使用流行的升级管理器 [Bower](https://github.com/bower/bower/blob/master/README.md) ，以使 Foundation 升级更有效、更容易进行。Bower 是一个流行的包管理器，解决了前端包管理的问题。

**ZURB 基金会 5 崇高文本片段**

SublimeText 是一个非常受欢迎的编辑器，也是开发人员的最爱，现在它提供了一些基础快捷方式，您可以使用它们来编写快速代码。你可以在 github 官方页面[https://github.com/zurb/foundation-5-sublime-snippets](https://github.com/zurb/foundation-5-sublime-snippets)阅读更多关于 Foundation 5 Sublime 的文本片段

## 下载基础 5

[下载 Foundation 5](http://foundation.zurb.com/develop/download.html) ，试一试，让我们知道你是如何找到它的。

## 分享这篇文章