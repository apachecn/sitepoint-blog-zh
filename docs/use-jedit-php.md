# 使用 jEdit 编辑你的 PHP

> 原文：<https://www.sitepoint.com/use-jedit-php/>

如果你正在寻找一个免费的 PHP 脚本开源文本编辑器，我推荐你试试 [jEdit](http://www.jedit.org/) 。这是一个成熟的、基于 Java 的程序员的文本编辑器，具有一组优秀的核心特性，以及更多可以通过“插件”添加的特性

尽管 jEdit 在支持 Java 方面最强，但它在 PHP 方面做得很好，它的核心优势适用于任何语言。对于 PHP 来说，它并不具备集成开发环境(T1)或集成开发环境(T3)的资格，尽管它与后者有一些共同的特性，包括语法高亮和 FTP。它缺少的一些 PHP IDE 特性是调试和代码完成。

在本文中，我们将研究 jEdit 的一些通用特性，以及它的 PHP 特有特性。

##### 下载和安装

请注意，完整的设置需要一系列的下载，这个过程可能需要时间。为了使用 jEdit，您需要 Java Runtime Environment(JRE)1.3 版或更高版本。如果你还没有安装，你可以从 Sun 的 Java 网站下载。

然后，[下载 jEdit](http://www.jedit.org/index.php?page=download) 。在撰写本文时，最新版本是 4.2。下载了基于 Java 的安装程序后，双击它。jar 文件并遵循安装步骤。jEdit 的默认安装没有 jEdit 图标，但你可以从[jEdit 网站的图像商店](http://jedit.org/index.php?page=images)或[其社区的文件商店](http://community.jedit.org/?q=filestore/browse/23)得到一个。

程序安装通常很简单，但是如果你有关于安装的问题，它们可能会在 jEdit [安装常见问题解答](http://www.jedit.org/FAQ/installation.html#id2878664)中得到解答。

当您第一次启动 jEdit 时，jEdit 帮助会打开，为您提供用户指南和一个空的编辑窗口。除了阅读用户指南，也许你想做的下一件事就是下载一些插件。jEdit 有许多扩展其特性集的插件。虽然插件可以从 jEdit 的[插件中心](http://plugins.jedit.org/)获得，但一般来说，获得和安装它们最简单的方法是使用 jEdit 内置的插件管理器。

要使用管理器，一旦你的计算机在线，在空的编辑窗口中进入插件|插件管理器，然后点击安装标签。可以通过单击列标题对插件列表进行排序。点击一个插件将会显示该插件的描述。首先，我建议您选择以下插件进行安装:

*   BufferTabs
*   编辑方案
*   恐怖分子
*   文件传送协议
*   JTidyPlugin
*   航海家
*   PHPParser
*   助手
*   TextTools
*   空白
*   可扩展标记语言

安装这些插件后重启 jEdit。

那些想要拼写检查功能的人有两个选择:拼写检查和 Jazzy。拼写检查利用了支持多种语言的 Aspell 拼写检查器。要使用拼写检查，首先安装 Aspell(一个 [Windows 版本](http://aspell.net/win32/)可用)。你还需要安装一本字典。

这两个项目安装完成后，下载 jEdit 拼写检查插件，将其解压缩，然后将。jar 文件与其他插件在同一个目录中。jar 文件。您可能需要搜索它们——它们在保存您的 jEdit 设置的“jars”目录中——或者安装程序可能会自动为您搜索。例如，在 Windows XP 上。jar 被正确地放在 C:Documents and settings user . jeditjars 中。

您可以使用插件|插件选项|拼写检查将拼写检查指向 Aspell 可执行文件，并为 PHP 编辑模式选择自动标记。遗憾的是，目前拼写检查似乎不包括向用户目录添加单词的功能。

另一个拼写检查插件， [Jazzy](http://jazzy.sourceforge.net/) ，确实包含了向用户词典添加单词的功能。你可以使用插件管理器来安装 Jazzy。你还需要在你选择的位置安装一个[字典](http://sourceforge.net/project/showfiles.php?group_id=34472&package_id=62241)(只有英语可用:选择 English _ DIC . zip)——可能在你的。jedit 文件夹。使用 Plugins | Plugin Options | Jazzy 指向字典文件 eng_com.dic 的位置，并放弃其他文件。

现在，您已经获得了以强大的方式使用 jEdit 所需的所有文件！

##### 定制的

接下来要做的是调整编辑器的外观。

要更改设置以满足您的需求，请转到实用程序|全局选项。我建议你现在不要改变状态栏的设置，因为通过点击状态栏的特定位置(在文本区域下面)，你可以快速切换特定的便利设置，比如文本的换行。(我发现，除非使用[等宽字体](http://www.kuro5hin.org/story/2004/12/6/11739/5249)，例如[比特流 Vera Sans Mono](http://www.gnome.org/fonts/) ，否则软换行似乎无法正常工作。)

你也可以点击插件|插件选项|缓冲标签，并默认启用缓冲标签。另一个插件，编辑器模式选择器，可以让你改变 jEdit 文本区域的颜色。

右键单击文本区域会弹出一个可定制的菜单。默认的右键菜单有常用的选择。

如果你花些时间研究 jEdit 的用户指南，以及不同的菜单，你会学到很多。你也可以学习 jEdit 的启动提示(实用程序|全局选项| jEdit |外观，启动时显示提示)。

##### 搜索和替换

jEdit 具有广泛的搜索和替换功能。它可以搜索突出显示的选择、当前缓冲区(即活动文件)、所有打开的缓冲区(文件)和/或目录和子目录，在搜索词中使用或不使用正则表达式。

##### PHP 语法高亮显示

jEdit 的标准安装包括超过 130 种文件类型的语法高亮显示，包括 PHP。我发现这个特性在编写 PHP 代码时非常方便。颜色的视觉反馈有助于减少代码中可能出现的语法错误。

jEdit 中的语法突出显示由称为“编辑模式”的 XML 文件控制。这些文件(每种文件类型一个)保存在 jEdit 安装的“modes”目录中。PHP 的编辑模式称为 php.xml。

现在，当我查看这个文件并将其与当前的 PHP 在线文档进行比较时，我发现这个编辑模式文件并不是最新的。我创建了一个修订版并上传到 jEdit 社区网站，从那里你可以获得 php.xml 更新版本的副本。注意，这个版本只是实验性的；请务必保留 jEdit 安装时附带的原始 PHP 编辑模式文件的备份。

要使用新的编辑模式，只需用新的模式替换原来的模式，然后重新启动 jEdit，或者选择实用程序|故障排除|重新加载编辑模式。编辑模式由文件扩展名自动选择(例如 php)，但是也可以通过工具|缓冲区选项|编辑模式为当前缓冲区中的任何文件扩展名手动选择它们。

##### PHP 4 解析器

上面推荐的插件之一是 PHPParser。这是一个 PHP 4 解析器，它在加载或保存文件时检查 PHP 文件中的语法错误(假设您使用插件|插件选项| PHP 解析器选择了这些选项)。任何错误都会在文本区域加下划线，如果你已经安装了 ErrorList 插件并设置它在出错时自动显示(使用插件|插件选项|错误列表)，一个弹出消息框也会描述 PHP 语法错误。像语法高亮一样，这是另一个在早期诊断 bug 的便利特性。

PHPParser 的作者目前太忙，无法添加 PHP 5 支持，但他正致力于为该插件添加一些其他功能。

##### 文件传送协议

FTP 插件可以与常规和安全的 FTP 服务器一起工作，使用主动或被动 FTP。它提供了远程浏览目录和编辑文件的能力。要访问它的功能，点击插件| FTP。

##### 可折叠的

jEdit 有一个简洁的特性，叫做“折叠”它允许基于缩进或通过使用“折叠标记”(不要与常规标记混淆，常规标记允许您快速跳转到文件中的其他位置)来折叠或展开代码。折叠标记为`{{{`和`}}}`，放置在要折叠和展开的行的前后。

要使用这些折叠标记，必须打开“显式”折叠。在工具|缓冲区选项|折叠模式下为当前缓冲区(即文件)设置折叠选项，或者在工具|全局选项|编辑|折叠模式下为所有文件设置折叠选项。要使用折叠标记，请在 PHP(或 HTML)代码中添加额外的注释行，并在注释中放置打开和关闭折叠标记。

##### 宏指令

jEdit 的标准安装附带了一组有用的宏，它们位于 macros 菜单的下半部分。在同一菜单的顶部还有一个宏记录器。jEdit 用户指南的第三部分包括“编写宏”，可以通过 Help | jEdit Help 访问。jEdit 使用 [BeanShell](http://www.beanshell.org/) 作为它的宏脚本语言。

##### HTML/XHTML 和 XML

[XML 插件](http://plugins.jedit.org/plugins/?XML)为 HTML/XHTML 和 XML 文件提供标签和实体完成、匹配标签高亮显示和标签属性的图形编辑(取决于文件扩展名，例如。html)。它不像其他编辑器中的一些 HTML 标签创建工具那样功能齐全，但仍然很方便。XML 插件还根据 XML 文件的 DTD 来验证 XML 文件。

[Xilize 插件](http://plugins.jedit.org/plugins/?Xilize)基于 Xilize 标记生成 XHTML 代码，这是[纺织品](http://textism.com/tools/textile/)的超集。 [JTidyPlugin](http://plugins.jedit.org/plugins/?JTidyPlugin) 提供了一个到 [HTML Tidy](http://tidy.sourceforge.net/) 实用程序的接口，该实用程序与 JTidyPlugin 一起安装。

[额外的 HTML 和 XML 插件](http://plugins.jedit.org/list.php?category=4)可用。

##### 支持

如需用户指南之外的支持，请访问 [jEdit 社区](http://community.jedit.org/)网站。

*致谢*

jEdit 的核心开发者是 Slava Pestov。感谢 Justin Hagstrom 向我提供了关于 PHP 语法高亮的信息，感谢 Matthieu Casanova 编写了 PHPParser 插件并向我提供了进一步的信息。

## 分享这篇文章