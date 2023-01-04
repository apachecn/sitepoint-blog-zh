# 加速开发:NetBeans 代码模板、自动热键和同上

> 原文：<https://www.sitepoint.com/code-templates-autohotkey-and-ditto/>

用 PHP 或任何其他语言编写应用程序是一项既有回报又有时具有挑战性的工作。这也是一个耗时的过程，取决于你正在做的项目的复杂程度。一些 ide 内置了旨在加速编程过程的工具或插件。还有其他有用的软件，如库、代码片段和第三方应用程序。在本文中，我将向您展示如何使用 NetBeans 内置的一个特性以及另外两个应用程序来提高我在 Windows 上编程的效率。这些技巧和技术提供了一种不同的思考方式，对任何开发人员都是有帮助的，不管他的技术水平如何。

出于本文的目的，我正在使用 NetBeans IDE。如果您还没有安装它，您需要确保首先安装 Java JDK，因为这是 NetBeans 的一个要求。你可以从甲骨文下载最新的 JDK。一旦安装了 JDK，你就可以从 netbeans.org/downloads 的 [target="_blank" >下载 NetBeans 了。有几个版本的 NetBeans 可用；我下载了 All 包，因为除了 PHP 之外，我还做了一些 C 和 Java 开发。如果您只需要 PHP 支持，那么可以安装 PHP 包。](http://netbeans.org/downloads/ "NetBeans IDE Download")

## NetBeans 的代码模板

继续启动 NetBeans 并创建一个新项目；一个虚拟项目将足以满足我们现在的目的。从主菜单栏中选择`File` > `New Project`，在新建项目对话框中选择 *PHP 应用*，点击下一步。因为您只是在创建一个虚拟项目，所以您可以在下一个屏幕上单击 Finish。NetBeans 创建新项目并打开新的`index.php`文件供您编辑。

代码模板是 NetBeans 的代码片段功能。使用模板允许您为一段常见的代码编写一个缩写，然后按一个触发键用一个代码片段替换这个缩写。让我们尝试一个已经定义的模板…

在创建新项目后打开的`index.php`中，将光标放在 PHP 块中的注释后，按回车键插入新的一行。然后键入单词 if，不要按空格键，而是按 Tab 键。NetBeans 会将 if 语句的开头添加到脚本中。

现在让我们创建一个新的代码片段。从主菜单中选择`Tools` > `Options`，点击选项窗口顶部的编辑器图标。选择代码模板选项卡，然后从语言下拉列表中选择 *HTML* 。点击新建按钮，在缩写字段中输入 *pre* ，然后点击确定。在展开的文本字段中，键入以下内容:

```
<pre>
<?php print_r(${cursor}); ?>
</pre>
```

我选择它作为一个简单的例子，因为它是在 PHP 中查看数组和对象内容的常用方法。NetBeans 是代码感知的，所以因为您在 HTML 模板中创建了代码片段，所以如果您在 PHP 块中，它将不起作用。代码片段中的`${cursor}`变量告诉 NetBeans 在按下触发键后将光标放在哪里。键入代码后，单击 OK 并进行测试。

## AutoHotKey

现在，您已经知道如何在 NetBeans 中定义代码模板，让我们继续学习另一个有用的软件——一个名为 AutoHotKey 的程序。AutoHotKey 是一种脚本语言，用于在 Windows 上自动执行任务。你可以从 www.autohotkey.com/download 下载。

一旦安装了 AutoHotKey，在桌面上点击右键，选择`New` > `AutoHotKey`脚本，给文件起个你喜欢的名字。在文本编辑器(如记事本)中打开 AHK 文件，并键入以下内容:

```
#^p::
Send pre{tab}
Return
```

井号告诉 AutoHotKey 监听 Windows 键，脱字符表示 CTRL 键，P 就是 P 键。这两个冒号随后告诉 AutoHotKey 这是一批命令。下一行发送击键 *p* 、 *r* 、 *e* ，以及一个标签到当前活动的窗口。这些击键也与您的代码模板缩写和 NetBeans 中的触发键相匹配。Return 告诉 AutoHotKey 停止处理行并开始监听其他键。

保存文件，然后右击 AHK 文件并选择`Run script`。返回 NetBeans，删除使用代码模板插入的前一个代码块。现在在你的键盘上按 CTRL + WIN + P，pre 块应该被插入并且光标被放置在正确的位置。

AutoHotKey 有一个简单但有效的脚本窗口语法。你可以在[www.autohotkey.com/docs/Hotkeys.htm](http://www.autohotkey.com/docs/Hotkeys.htm "Hotkeys (Mouse, Joystick and Keyboard Shortcuts)")阅读更多关于它的热键和更多样本脚本。

## 同上

我想让你注意的最后一个软件是同上。Ditto 是一个剪贴板管理器，是我试过的最好的一个，因为它不显眼，占用内存少。你可以从 ditto-cp.sourceforge.net 的[下载。下载完 Ditto 后，将存档文件的内容解压到您选择的任何位置。打开文件夹，双击`Ditto.exe`文件。这运行同上，并把它放在系统托盘中。右键单击复制图标，然后单击`Options`。](http://ditto-cp.sourceforge.net/ "Ditto clipboard manager")

我设置了几个选项，但在我使用它的时间里，大多数默认选项都被证明是足够的。例如，Ditto 默认存储大小为 500 个项目，在我看来这已经足够了，但是您可以根据自己的需要更改这个数字。不过，我更改的选项是在系统启动时启动 Ditto，快捷键是 CTRL + SHIFT + V。这允许我通过按一个额外的粘贴键来使用 Ditto。

让我们假设您已经为数组复制并粘贴了几个变量名，现在您想查看其中一个变量名的内容。在 NetBeans 中，按 CTRL + WIN + P 插入 pre 块，然后按 CTRL + SHIFT + V，直到显示要使用的变量名。你已经减少了很多击键次数，并且设法让你的手在键盘上保持相似的位置！

Ditto 允许你在多台计算机上同步你的剪贴板，如果你能用你的路由器转发端口，调整你的防火墙设置等等，这是很棒的。不幸的是，我在办公室没有访问路由器的端口转发设置。为了避开这个限制，我从`Ditto`文件夹中复制了`ditto.db`，并把它放在我的 Dropbox 账户的`Ditto`目录中。然后我在 Ditto 的选项中更改数据库路径，指向 Dropbox 文件夹。我只需要记得在离开我的办公室电脑之前关闭 Ditto，数据库就会在我的其他电脑之间保持同步。我还利用 Dropbox 来存储我的 AutoHotKey 脚本。

## 摘要

在本文中，我向您展示了如何使用 NetBeans 的代码模板和 AutoHotKey 以及 Ditto 来提高我的编程效率。如果你以前不熟悉这些程序，我希望它们对你有用。如果你有任何想要分享的提高效率的小技巧，请在下面的评论区随意发表。

图片 via [贡纳尔·皮佩尔](http://www.shutterstock.com/gallery-242458p1.html "Stock Photos | Shutterstock: Royalty-Free Subscription Stock Photography & Vector Art")/[Shutterstock](http://www.shutterstock.com "Stock Photos and Royalty-Free Images by Subscription")

## 分享这篇文章