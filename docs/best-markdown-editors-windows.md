# Windows 的最佳降价编辑器

> 原文：<https://www.sitepoint.com/best-markdown-editors-windows/>

![A box for a typesetter's letters](img/a7dba00c09af68b65a721e190d4b603b.png)

*这篇受欢迎的文章于 2017 年更新，涵盖了最新的 Windows Markdown 编辑器，并回顾了一些旧编辑器的发展情况。*

Markdown 已经成为网络上的标准文本标记语言。它通常用于现代 CMSs、论坛和创作工具中。它是跨平台的，易于理解，易于协作。

Markdown 有许多“风格”(变体或扩展)，名称各不相同，因为创建者不希望任何人在他们的项目中使用“Markdown”名称。Forks 包括 PHP-Markdown、PageDown、Parsedown 和 Pandoc 等等。

大型网站倾向于通过额外的定制来调整它，所以你也会听到像“Vim 风格的降价”和“GitHub 风格的降价”这样的名字。栈交换网络在服务器端使用称为 Pagedown 的 Markdown 和 MarkdownSharp，并加入了一些 PHP-Markdown。

在本文中，我将使用“Markdown”或“vanilla Markdown”来指代[原始 Markdown 规范](http://daringfireball.net/projects/markdown/syntax)，在描述扩展特性时，我将引用特定风格的名称。

* * *

想了解更多关于减价的信息吗？看看这些伟大的链接:

*   [Mac 的最佳降价编辑器](https://www.sitepoint.com/the-best-markdown-editors-for-mac/)
*   【Linux 的最佳降价编辑器
*   [获取我们的免费可打印减价备忘单](https://www.sitepoint.com/grab-our-free-printable-markdown-cheat-sheet/)
*   充分利用 WordPress 中的 Markdown 功能
*   [用减价给你的电子邮件增添趣味](https://www.sitepoint.com/spicing-up-your-emails-with-markdown/)
*   [7 个 Atom 插件，用于运行代码和预览变更](https://www.sitepoint.com/7-atom-add-ons-for-running-code-and-previewing-changes/)
*   [使用 Pandoc 和 LaTeX 从 Markdown 创建 PDFs】](https://www.sitepoint.com/creating-pdfs-from-markdown-with-pandoc-and-latex/)

查看 [SitePoint Premium](https://www.sitepoint.com/premium) 获取更多书籍、课程和免费视频。

* * *

### 语法风格

没有一个程序能涵盖 Markdown 的每一种风格和语法，但是你总能从基本的 Markdown 语法开始。SitePoint 本身要求作者使用经典的文章提交和链接到主要项目的网站[这里](http://daringfireball.net/projects/markdown/syntax)来学习它。

一个制定官方规范的倡议来自于 [CommonMark](http://commonmark.org) 。下面的几个编辑支持它，我希望有一天我们能得到一个通用的标准来避免有这么多的口味。

关键是，确保您选择的编辑器支持您最想使用的特定语法！

### 多种编辑风格

这些应用程序在外观和感觉上都大相径庭。在我测试这七个工具的过程中，很明显没有两个工具以同样的方式做事情，这让我只能给出我最喜欢的一般意见。

在这篇文章中，我不会评论在线编辑器，例如[迪林杰](http://dillinger.io)、[马克布尔](http://markable.in)或 StackEdit。我在这里的重点只是你可以在你的 Windows 机器上运行的应用程序。

我发现这些是编辑器中的主要特性差异:

*   支持的语法和扩展
*   语法突出显示和/或所见即所得功能
*   实时预览窗口(分屏)
*   导出选项
*   语法助手，工具栏，快捷键等。
*   免费和/或付费选项

排名不分先后，以下是我使用每个编辑器一段时间后的看法。

## 文本

[Texts](http://www.texts.io) 是一个 Windows 和 Mac 的编辑器，看起来有点像 Windows 编辑器记事本。它有一个干净的单窗格界面，没有实时预览。有一个简单的工具栏可以打开或关闭。

你在文本中看不到原始代码，因为它以传统的 WYSIWYG 风格格式化代码。这是这个系列中唯一对您隐藏语法代码的编辑器。

短信不是免费的。它从试用开始，然后团队中的每个用户或单个许可证的费用为 19 美元。这比我上次审查时的 30 美元有所下降。

Texts 使用 [Pandoc](https://pandoc.org/) ，这意味着必须安装 Pandoc 才能使用某些功能。

Texts 更适合希望将文档导出为 PDF、Word、HTML5、ePUB 等格式的 WYSIWYG 用户。它包括用于数学公式的 Tex，以及对特定超链接、脚注和表格的一些特殊处理。其他高级功能包括支持 Unicode、OpenType 字体和演示模式。

导出为 PDF 还需要安装 XeLaTeX。

就我个人而言，文本不适合我喜欢的写作风格，在我看来，它处理风格的方式很古怪，并且在我键入时隐藏了原始标记。

![A screenshot of the Texts editor](img/bac9f19860bb63104f21de66aa3a909c.png)

如果你想要一个普通的 Windows 或 Mac 应用程序，一个所见即所得的写作风格，以及良好的导出选项，文本可能就是你想要的。它也有一些有趣的功能，如移动段落，“粘贴为”选项，以及“插入参考书目”功能。

## 写猴子

WriteMonkey 专注于“干净”和不受干扰的用户界面。界面上几乎什么都没有，它被设计成全屏使用。右击将弹出该程序所有的扩展选项，包括文件和文件夹视图、目录、书签等等。这是一个不隐藏 Markdown 源代码的单窗格编辑器。如果需要，可以启用最小语法高亮显示；否则，您会看到纯文本。

支持的语法包括 Markdown Extra、Textile 和 WikiCreole。

这个编辑器最适合那些不想或不需要所见即所得风格来完成工作，但也喜欢修补完美编辑体验的 Markdown 专家。从精确的边距和缩放到打字机声音和滚动效果，任何东西都可以改变。它可以计算文档中的所有内容，甚至是你最常用的单词。

你可以设置你想要编辑的时间，或者字符或字数限制，这样你就不会写太多！

其他很酷的功能包括文本替换、自动备份和单词查找。

WriteMonkey 是一个免费的、仅适用于 Windows 的独立应用程序，需要微软。NET 4.0。当然，你可以从 USB 驱动器上运行它，这是唯一一个独立的编辑器。

它支持扩展以及许多不同翻译的语言包。扩展只适用于向项目捐款的人，包括辞典和番茄定时器之类的东西。

WriteMonkey 是一个修补程序的编辑器。插件引擎是 JavaScript，你甚至可以定制用户界面本身，在信息栏上放置你喜欢的主题。

可以在窗口内调整左/右边距。如下图所示，我将书写区域向左移动:

![The writing area in WriteMonkey](img/e4e7163d0dae4ef5928d4bf5f2f94d20.png)

下图是主右键菜单中的一些可用功能:

![WriteMonkey options](img/4759b3a5ddfa6d5a742d5ed18f8d135c.png)

甚至看着我的进步，也有很多选项可以玩:

![And more WriteMonkey options](img/8fea7e32d101b57e2e868fb17d4cd94d.png)

WriteMonkey 可能是我的顶级编辑，如果不是因为我喜欢 Markdown 本身的视觉风格，并且我不太喜欢无休止的修补和高级功能。如果你使用的是 Windows，想要一款免费的独立应用，这款软件正适合你。

## 哈罗帕德

Haroopad 运行在 Windows、Mac 和 Linux 上，专注于在每个平台上提供相同的体验。它功能齐全，使用分屏，支持多种语言和颜色高亮显示，Vim 键绑定，fenced 代码块(带高亮显示)，GitHub 风格的语法，表格，多标记(下划线，上标，下标)，音频/视频嵌入，数学，等等。

我不得不缩短我对 Haroopad 的评论，因为这个项目似乎被搁置了。GitHub 已经有 4 年没有太多活动了，有超过 180 个开放的问题，还有一个作者的推文，他们不确定它会有任何新的发布。

然而，仍然有粉丝希望这个功能齐全、深受喜爱的节目能够起死回生。由于历史原因，我在本文中留下了一个提及，但如果你想尝试一下，该应用程序仍然有效。

## 标记板

MarkPad 是一个分屏编辑器，它没有这里其他编辑器的许多选项。它与普通的 Windows 应用程序有着完全不同的外观和感觉。

没有右键菜单、工具栏或助手。它的选项和配置很少。但是，它有一个功能可以直接发布到某些博客和 GitHub 上。

样式对我来说不是很好，浏览器似乎有问题，当窗口调整大小时字体没有正确显示。

与博客的默认连接使这成为一个独特的选项，你可以点击打开一个新的 Jekyll 页面。可以发布到 GitHub，或者使用 MetaWeblog API，如果你的博客支持的话。

截至我的最后一次审查，似乎没有任何更新，提交时间超过 3 年了。我担心这也是一个垂死的项目，就像 Haroopad 一样。

这是一个开放源代码的，只适用于 Windows。NET 4 项目使用微软 WPF 技术。它使用 vanilla Markdown 和一个选项来实现一些额外的增强。

![A screen shot of MarkPad in action](img/c067bf06dad202bcfdfc5cc628334352.png)

## MarkdownPad 2

[MarkdownPad 2](http://markdownpad.com/) 是最早为 Windows 问世的桌面应用之一，成熟度可见一斑。这个项目看起来非常扎实，考虑得很周到。有免费版和专业版。您需要专业版来导出 pdf，并使用增强的降价语法选项。

这是带有选项卡式界面的两个编辑器之一。分屏是可调的，你可以改变样式，也可以使用自己的 CSS 样式表。它有最全功能的工具栏，用户界面是一个更标准的窗口感觉，类似于文本，而不像这里的其他。

对于 SitePoint 的文章来说，vanilla Markdown 非常适合我，工具栏上有我需要的所有东西。编辑器没有隐藏代码，但它添加了一些突出显示，这在视觉上是很好的，因为知道图像和链接输入正确。

专业版售价 14.95 美元，允许商业使用、自动保存等等。

MarkdownPad 2 是使用。NET 4 框架和 Windows Presentation Foundation 框架(就像 MarkPad 一样)。但是这个没有同样的 UI 问题。

![A screen shot of the MarkdownPad interface](img/23a76180c3f9becc57ff43b97643a3ab.png)

对于我连续第三次审查，我也为 MarkdownPad 的未来感到担忧。这是我两年前选择的最佳编辑，我用了很长时间。可悲的是，它也停滞不前了！他们最后一次更新推文是在 2016 年 2 月，显示了新进展的前景，但随后就沉寂了。下一条推文是在 2014 年 12 月！

这是一个非常可靠的编辑器，我敢肯定它仍然工作良好，但他们会希望在试用后付款，谁会想买一些没有工作的东西呢？

## 你这个白痴

我的列表中的一个新条目是 [Typora](https://typora.io/) ，这是一个现代而活跃的项目，它正在发展桌面减价编辑本身(在我看来)。

Typora 是一个具有可视化样式的单窗格编辑器，仅在光标离开文本后隐藏 Markdown。例如，一个链接看起来像一个链接，但如果你把光标放在它上面，降价代码就会出现供编辑。这实现得很好，我开始喜欢这种方法。

视觉样式类似于文本的工作方式，除了我更喜欢 Typora 的方式。并不是想纯粹做一个所见即所得。它也有一些好看的默认样式，比如 GitHub 外观、night 样式和 news-ey 外观。

该项目仍处于测试阶段(在撰写本文时版本为 0.9.29)。changelog 不显示日期，并在其中使用奇怪的版本，例如“0.9.9.9.4.2”。

不管怎样，当我停止使用 MarkdownPad 2 时，Typora 成了我的新编辑。它在测试阶段是完全免费的，甚至连一个捐赠按钮都没有。

Typora 不像 WriteMonkey 那样有很多功能，但是您可以做一些有趣的事情，比如简单的表格编辑和漂亮的代码栏语法突出显示。有一个跳转到标题的文档大纲，你可以随时跳转到一个纯粹的“源代码模式”，只查看原始代码，但仍然保留一些语法亮点。

[https://www.youtube.com/embed/TO8uaVTyzXs](https://www.youtube.com/embed/TO8uaVTyzXs)

Typora 可以在 Windows、Mac 和 Linux 上运行。他们有一个 [GitHub](https://github.com/typora/typora-issues/issues) 账户用于问题跟踪，截至本文撰写时，该账户有大约 75 个未解决的 bug。我当然希望作者继续写这本书！

![A screen shot of the Typora interface](img/13fbf714e2119e0b3a76b752addde45c.png)

## 脱字号

另一个名为 [Caret](https://caret.io/) 的新编辑器也加入了这场争论，它是由 Parsedown 的作者开发的。它基于 Electron，可以在 Mac、Windows 和 Linux 上运行。

有一个免费的试用版，但除此之外，一个许可证将花费你 25 美元。

特性列表比不上我们的黄金标准 WriteMonkey，但是它的特性非常有用。比如语法帮助、自动完成和查找。多光标编辑，前台事务支持，分页符插入，文件浏览器，Git 支持，和自动图像张贴到 Imgur。甚至表情符号也支持。

事实上，我正在用 Caret 写这篇文章，并且在测试的时候已经很满意它几个星期了。单窗格风格是我最喜欢的，尽管如果你喜欢它也支持双窗格。用户界面的风格干净整洁。语法高亮很好，但是它没有像 Typora 那样隐藏 Markdown 代码。

当我写这篇文章的时候，它最近的一次更新是在 9 天前，所以开发者正在修复问题并推出新功能，这很好。

他们甚至有一个“发布”功能，可以立即将你的文档上传到他们的云中，并提供一个可以共享的公共链接。

问题在他们的 [GitHub](https://github.com/careteditor/caret/issues) 账户中被追踪，有 46 个公开的 bug 和 150 个问题。

![Dangling a caret screen shot](img/c3811efa62ee8432481628d29e8cf690.png)

众所周知，其他 SitePoint 作者和编辑也使用 Caret 进行写作。

## 结论

我现在的结论与两年前大不相同。随着 Haroopad、MarkdownPad 2 和 MarkPad 似乎成为死项目，我只剩下其他四个编辑器可供选择。

对于那些寻求所见即所得体验的人，我会将文本与 Typora 进行比较，我同意 Typora，因为它没有完全对我隐藏降价。

我会将 WriteMonkey 与 Caret 进行比较，以获得更原始的降价体验。WriteMonkey 有非常少的语法高亮帮助，而 Caret 更有风格。

所有这四个都允许单窗格体验(我更喜欢的体验)，但是 Caret 允许预览窗格。WriteMonkey 有很多有趣的特性和修补能力。所有的编辑都做得很好，给了我一个新鲜和干净的编辑体验，干扰最小。

如果你想要一个跨平台的桌面应用程序，我的投票是 Caret 与它的电子使用。但是如果你需要一个免费程序，WriteMonkey 是一个不错的选择，如果你可以没有一些付费的好处，只需要 Windows。

我没有评论的另外几个编辑器包括 [Markdown Plus](https://tylingsoft.com/markdown-plus/) 和一个开源的免费选项[代笔](https://wereturtle.github.io/ghostwriter/)。

当然有更多的桌面应用程序，或者只支持 Mac 的工具，或者支持 Markdown 的 Linux 工具、文本编辑器插件或 ide。也有在线编辑器和协作工具。无论它的平台是什么，都可以用你最喜欢的 Markdown 编辑器随意评论！

## 分享这篇文章