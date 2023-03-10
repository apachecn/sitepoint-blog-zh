# 强大的开源 Web 编辑器(IDE)

> 原文：<https://www.sitepoint.com/powerful-open-source-web-editor-ide/>

复杂的 WYSIWYG 编辑器的出现和现在几乎占主导地位已经成为网页设计者和开发者快乐和痛苦的源泉。Macromedia Dreamweaver、Adobe GoLive 甚至微软的 Front Page 等工具利用模板和其他自动化功能节省了无数时间。

这些编辑器成功的一个核心部分是集成开发环境(IDE ),它包括远程发布、文件和修订管理、与图像编辑器等其他应用程序的联系以及对底层源代码的访问。

每一种都有优点和缺点，我必须承认我自己对 Dreamweaver 的偏见，自从 1999 年以来，我一直在 Mac 和 Windows 平台上使用它。我的一个主要抱怨与编辑器无关，只是我不能在我的主要 Linux 开发工作站上运行它们。多亏了 NVU，这个问题似乎得到了某种程度的解决。

NVU 建立在 Mozilla Composer 的源代码基础上，是一个新的开放源代码解决方案，跨越多个操作系统(Windows、Macintosh 和 Linux)，然而，它的主要目标是为 Linux 提供一个全面的 IDE。

该项目由 Linspire 资助，由前 AOL/Netscape 开发者丹尼尔·格拉次曼领导，IDE 看起来是一个很好的起点。它具有许多与那些商业解决方案相同的功能和能力，包括复杂的 CSS 和 JavaScript 编辑、选项卡式工作区、用于在编辑环境中发布到多个位置的站点管理器等等。

NVU 网站声明该程序支持模板功能——然而——在加载和使用该软件时，我还没有找到一个明确的方法。虽然 Macromedia 不一定要模仿 Dreamweaver 的模板方法，但它确实能很好地处理模板。虽然我们可以使用 Apache 和其他 web 服务器的本地 include 函数或者一种脚本语言来创建自己的模板，但是在 NVU 内部创建一个类似模板的小部件会很棒。

如果 NVU 希望吸引越来越多的开发人员使用该工具，那么从上述工具中移植模板的方法可能也是一个有价值的附加功能。

作为一个开源项目，我相信 NVU 也会欢迎那些对 IDE 做出贡献的人。除了模板，可能还有集成的 ODBC 连接管理器、预输入库和对 sftp 的本机支持(尽管可以使用 [MindTerm](https://www.sitepoint.com/blog/) 来解决这个问题)。

## 分享这篇文章