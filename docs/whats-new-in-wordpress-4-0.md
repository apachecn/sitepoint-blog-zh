# WordPress 4.0 的新功能

> 原文：<https://www.sitepoint.com/whats-new-in-wordpress-4-0/>

*这篇文章已经过时了，[点击](https://www.sitepoint.com/whats-new-in-wordpress-4-6/)查看 WordPress 4.6 的新功能。*

今天， **WordPress 4.0** (以爵士音乐家本尼·古德曼命名为“本尼”)发布了。以下是由发布负责人 Helen Hou-Sandí领导的 WordPress 团队的最新更新综述。

![WordPress 4.0 Release](img/f19ffdef5dea913af99d50d0fbfe78b2.png)

## WordPress 4.0 的主要特性

每个 WordPress 版本都有大量的工作要做，4.0 版也不例外。在本文中，我们将回顾 4.0 版本带来的主要改进。我们将介绍的主要功能有:

*   安装时选择您自己的语言
*   媒体库的新网格视图
*   内容编辑器的改进
*   对 oEmbed 的更多支持
*   TinyMCE 视图的改进
*   新的插件安装体验
*   新部件定制器面板
*   新的辅助功能
*   更新的外部库

我们还将介绍如何将您的网站更新到最新版本。

## 安装时选择您自己的语言

WordPress 的一个主要好处是它是一个开源平台，世界各地的任何人都可以使用。事实上，据估计 29%的 WordPress.com 博客是用英语以外的语言写的。考虑到这一点，WordPress 团队满足这一需求是很有意义的。

在新网站上安装 WordPress 时，你会看到一个新的“语言选择”面板。

![WordPress 4.0 Installation Language Selection](img/853626b0a4eb76f306353170ee2d05c8.png)

如果你已经安装了使用另一种语言的 WordPress，你可以通过导航到设置>常规来改变它。在这个页面的底部，你应该可以看到新的“网站语言”选项。

![WordPress 4.0 Language Selection](img/36734bcac557f1fc35948f2a4c82740f.png)

## 媒体库的新网格视图

可以说是 WordPress 最大的(也可能是最期待的)改版，新的媒体库布局现在提供了网格布局视图(除了当前的列表视图)。这项功能最初是一个独立的项目，它提供了一个新的、改进的视图，使管理媒体文件比以往任何时候都更容易、更快。

当你点击一个媒体项时，它会直接把你带到一个弹出的模态窗口(而不是一个完全独立的页面)。在新的模式窗口中，您可以看到更大的预览，轻松编辑图像数据，甚至直接在媒体文件之间导航。新的网格视图还允许您同时选择多个文件，方法是选中右上角的一个框。

![WordPress 4.0 Media Grid View](img/2161fef09f5df3ccf5c03167d4e7a3cd.png)

## 内容编辑器的改进

WordPress 4.0 允许你在编辑模式下更好地利用你的屏幕。

编辑器现在可以调整大小，并且在需要时可以锁定顶部和底部的栏。因此，当你上下滚动时，文章编辑器会保持不动，所以没有必要再滚动到页面顶部。这个特性很难用截图来说明，但是如果你在内容编辑器上花了很多时间，你会发现它极大地改善了编辑体验。

## 对 oEmbed 的更多支持

新的 WordPress 4.0 版本提供了直接在可视化编辑器中预览任何嵌入的 oEmbed URL 的能力。视频本身实际上呈现了。这样就不再需要“预览”你的文章或页面，只是为了看看它在你的网站上看起来怎么样。这项新功能非常适合预览直接取自 YouTube、Vimeo、Flickr、Twitter 和 SlideShare 等服务的 URL。虽然 oEmbed 不是 WordPress 的新功能，但在可视化编辑器中查看实时预览的功能绝对是。

WordPress 现在也支持来自更多提供商的 oEmbed，包括:

*   TED 演讲
*   大学幽默
*   YouTube 播放列表
*   是吗
*   Mixcloud
*   Polldaddy 短 URL

为了让你了解这个全新的功能是如何工作的，这里有一个在嵌入式 TED Talk 视频的内容编辑器中的实时预览:

![WordPress 4.0 oEmbed Visual](img/80250cf8581ce1f854e3f9e69db28d49.png)

这是 HTML 视图:

![WordPress 4.0 oEmbed HTML View](img/fd663a4aea5b3ce880de8cccc6c36078.png)

## TinyMCE 视图的改进

键盘和光标与 TinyMCE 视图(如 gallery preview)的交互方式也有一些改进。这些改进与上面提到的编辑器调整大小和滚动改进非常相似。

## 新的插件安装体验

这个新版本的 WordPress 在插件安装部分提供了一个全新的视觉体验。改进后的插件安装页面允许你在网格视图中浏览(很像新的媒体库网格视图),并在插件搜索时返回大量信息。

如果你开发了一个 WordPress 插件，确保你添加了一个缩略图来帮助你利用新的插件信息显示。

![WordPress 4.0 Plugin Install View](img/a8664802e38c490f8bf4e71bb36eeca2.png)

你现在也可以过滤测试版插件。

![WordPress 4.0 Beta Plugins](img/c238bed18cc1888e563932fad5f3bce9.png)

## 新的小部件定制器面板和 API

在 WordPress 的新版本中，小部件在定制器部分有自己的面板(注意“主题”这个词也从这个部分中去掉了)。在 3.9 版本中，小部件只是主题定制器的一部分。

开发人员还应该[在定制器](http://make.wordpress.org/core/2014/07/08/customizer-improvements-in-4-0/#customizer-panels)中检查面板的 API，定制器允许你将设置分组到面板中。

## 新的辅助功能

此版本对辅助功能进行了一些改进，包括“添加媒体”面板中的键盘辅助功能，屏幕阅读器对“定制器”和小部件的支持，以及帮助选项卡中的链接现在可以通过键盘进行访问。

## 更新的外部库

WordPress 4.0 现在提供了对以下库的更新:

*   TinyMCE 4.1.3
*   jQuery 1.11.1
*   mediaelemont 2.15

## 我如何更新？

到今天为止，您应该会看到以下消息，提示您只需点击几下鼠标即可更新到 4.0 版。与任何更新一样，您应该确保在继续之前有最新的备份。

![WordPress 4.0 Upgrade Message](img/755cd272b97a635fcff1366672c4c0ee.png)

要更新，只需按照消息中显示的链接。然后，您将看到以下屏幕，您可以选择自动更新或下载最新的文件。点击“立即更新”让你自动完成更新，你应该会看到“欢迎使用 WordPress 4.0”的信息。

![WordPress 4.0 Update Now](img/22db0878116e45766ee48ddd90c82d93.png)

你可能还记得 WordPress 3.7 版本默认引入了自动后台更新，但是这并不适用于主要版本的更新(例如 4.0、5.0 等版本)。).更新是可配置的，你可以在[配置自动后台更新](http://codex.wordpress.org/Configuring_Automatic_Background_Updates)的 WordPress Codex 页面上了解更多信息。

即使你的网站已经更新，检查你的主题和插件是否与最新更新兼容也是很重要的。如果您有任何问题或疑问，您可以在[WordPress.org 论坛](http://wordpress.org/support/topic/thoughts-on-wordpress-version-40)上找到关于该版本的一般讨论。

和往常一样，对你的站点进行**完整的文件和数据库备份**是很重要的，这样可以确保你在遇到任何问题时可以回滚。

## 超越 WordPress 4.0 和一个关于 WordPress 发布周期的注释

现在我们是 covered version 4.0 中的新特性，不要忘记 4.1(以及更高版本)即将推出。虽然一些评论者认为这次更新不像 3.0 版本那样具有突破性，但它的许多功能承诺并提供了更好的用户体验。

为了深入了解 WordPress 的发展，Andrew Nacin 在今年早些时候康涅狄格州 WordPress camp 的主题演讲中谈到了 WordPress 的发布周期。你可以[在 WordPress.tv](http://wordpress.tv/2014/06/07/andrew-nacin-wordcamp-connecticut-keynote/) 上观看他的演示。Andrew 的演讲也很好地概述了自动更新功能背后的想法以及 WordPress 的发展方向。

## 结论

我认为 WordPress 团队已经做了很好的工作，进一步改善了 WordPress 的用户体验，使它成为一个更容易被全球用户使用的平台。

对于特别感兴趣的人来说，这里是 WordPress 4.0 的官方发布说明和 WordPress Codex 上的更新日志:

*   [http://codex.wordpress.org/Version_4.0](http://codex.wordpress.org/Version_4.0)
*   [http://codex.wordpress.org/Changelog/4.0](http://codex.wordpress.org/Changelog/4.0)

开发者会对 4.0 的最新更新感兴趣。

如果你经常使用 WordPress，我相信你会喜欢这些变化。作为一个花大量时间在内容编辑器上的人，我已经爱上了 TinyMCE 编辑器、锁定和媒体网格的改进。我很想听听你的想法和反馈。

## 分享这篇文章