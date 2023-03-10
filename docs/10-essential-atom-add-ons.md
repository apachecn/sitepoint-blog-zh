# Web 开发的 12 个基本 Atom 包

> 原文：<https://www.sitepoint.com/10-essential-atom-add-ons/>

在本文中，我们将挖掘 12 个最适合 web 开发人员的 Atom 包。Atom 有很多竞争对手——包括 Visual Studio 代码和 Sublime Text——但它仍然是一个受欢迎的、有竞争力的 web 开发工具。

## 为什么要使用 Atom 编辑器？

在过去几年中，VS 代码可能已经赢得了 web 开发人员的心，但 GitHub 的 Atom 编辑器仍然是市场上更好、更有能力的代码编辑器之一。非常喜欢它的原因包括:

*   安装程序适用于 Windows、Mac 和 Linux
*   在过去的十年里，它一直在不断更新
*   在对最初版本的一些批评之后，速度有所提高
*   它仍然可以免费下载和使用，没有任何限制或唠叨屏幕

微软在 2018 年收购了 GitHub，因此该公司现在有两个很好的基于电子的代码编辑器。Atom 的长期未来可能存在疑问，但开发仍在继续。如果你正在寻找一个新的代码编辑器，那么 Atom 应该是你的首选。

### Atom 包和主题

Atom 一直宣传自己是“21 世纪的可破解文本编辑器”。基本安装的特性相对较少，但是你可以用被称为*包*的附加组件来扩展它。

在撰写本文时，有超过 3000 个 Atom 主题和 9000 个 Atom 包可供使用。部分原因是 Atom 可以使用 web 技术进行扩展。如果您是 Node.js 或客户端 JavaScript 开发人员，您应该知道如何创建自己的 Atom 包，并以任何方式增强 Atom。

## 如何安装 Atom 包

添加 Atom 包非常简单，因为 Atom 带有内置的包管理器。(许多开发人员被 Atom 所吸引，部分原因是安装 Atom 包太容易了。)

打开 Atom 编辑器，点击顶部导航栏中的**编辑**菜单，然后选择**首选项**。一个新的**设置**选项卡将会打开。点击 **+ Install** 菜单项，右边会出现一个搜索栏。这将允许您按名称搜索新的 Atom 包。当你找到你想要的 Atom 包后，点击**安装**按钮。

![Install Atom packages](img/11e14bc5f453f72093f68382d26154e4.png)

点击**包**菜单项将显示当前安装了哪些 Atom 包。你自己安装的任何东西都会出现在**社区包**菜单项下。你会注意到还有一个**核心包**菜单项。这将列出默认安装的软件包。如果你愿意，你可以禁用它们，但是最好不要这样做，因为这会影响编辑器的基本功能。

![Installed Atom packages](img/dc8d32f4636a9399a2b0da90dae99223.png)

### 从命令行安装 Atom 包

Atom 还附带了一个名为`apm`(Atom Package Manager 的缩写)的命令行工具。您也可以使用这个工具直接从终端安装软件包。

语法如下:`apm install <package-name>`。

您可以通过使用`apm config`命令行选项或手动编辑`~/.atom/.apmrc`文件来配置`apm`。输入`apm help`会让你知道它还能做什么。

也就是说，这里有 12 个最好的 Atom 包— *加上一些额外的选项* —让 Atom 成为一个更好的代码编辑器…

## 1.文件图标

Atom 的默认文件和文件夹图标最好用“功能性”来形容。像`file-icons`这样的图标集改善了编辑器的外观，使得定位特定类型的文件更加容易。

![file-icons](img/c0c56a905e9056d64f263b8a0a99814f.png)

*   **在这里获取文件图标包** : [文件图标](https://atom.io/packages/file-icons)

在 **+ Install** 窗格中搜索“icon ”,找到几十个备选选项。

## 2.项目管理人

Atom 提供简单的基于文件夹的项目管理。如果你在几个项目之间切换，这已经足够好了，但是`project-manager`对于任何更复杂的项目都是理想的。它提供了命令面板选项和一个可编辑的 JSON 文件，您可以在其中定义项目，并使用它们自己的自定义设置，如颜色、选项卡首选项等。

![The Atom project manager package add-on](img/a5208ee29919822405f092d9397c3bb0.png)

*   **在此获取 Atom 项目经理包** : [项目经理](https://atom.io/packages/project-manager)

## 3.同步设置

如果您在多个设备上运行 Atom，那么跨安装同步设置、键绑定和代码片段是很有用的。您可以通过克隆配置文件夹中的文件来手动同步(**设置**，然后**打开配置文件夹**，但是`sync-settings`提供了一个更容易的自动化选项。设置被保存到一个 Gist，但是其他 Atom 包允许你选择一个[本地文件夹](https://atom.io/packages/sync-settings-folder-location)或者 [Git 库](https://atom.io/packages/sync-settings-git-location)。

*   **在这里获取 Atom 同步设置包** : [同步设置](https://atom.io/packages/sync-settings)

## 4.待办事项显示

你已经启动了 Atom，打开了一个文件夹，然后……*接下来呢？*`todo-show`Atom 包显示了分散在您的项目中的注释，其中包含关键词`TODO`、`FIXME`和`CHANGED`，但是您也可以添加自己的正则表达式。

![todo-show](img/daf3e081b3789c8d7b1e9970757f521a.png)

*   **在这里获取 Atom Todo Show 包** : [todo-show](https://atom.io/packages/todo-show)

## 5.Minimap(最小化)

`minimap`是最受欢迎的 Atom 软件包之一，下载量超过七百万次。它在代码编辑器窗口的右侧显示代码的精简视图，这对快速导航非常有帮助。这个特征进入你的潜意识；你不会觉得你在用它，但它不在的时候你会想念它。

![minimap](img/591a78465d94f28921467499f78201db.png)

**在这里获得原子小地图包** : [小地图](https://atom.io/packages/minimap)

## 6.突出显示所选内容

当您在 VS 代码、Sublime Text 或 Notepad++中选择一个关键字或变量时，它会突出显示所有其他实例。`highlight-selected`为 Atom 带来了这一特性，如果与`minimap-highlight-selected`结合使用，效果会更好:

![highlight-selected](img/80bcb5fe4b70409ec01270cb050c9445.png)

*   **在这里获取高亮选中的 Atom 包** : [高亮选中](https://atom.io/packages/highlight-selected)
*   **在这里获取小地图高亮选择包** : [小地图高亮选择](https://atom.io/packages/minimap-highlight-selected)

## 7.自动关闭 HTML

顾名思义，当您完成开始标记时，这个包将自动添加一个结束 HTML 标记。这可能是一个简单的包，但是如果没有自动关闭 HTML 标签，我将无法应付！使您的标记创建速度加倍。它开箱即用，但是这个包也允许你定义哪些标签应该内联完成(比如`<p></p>`或者`<li></li>`)，哪些应该创建换行符块(比如`<article> ... </article>`或者`<ol> ... <ol>`)。

*   **在这里获取自动关闭 HTML Atom 包** : [autoclose-html](https://atom.io/packages/autoclose-html)

## 8.天然色素

大多数编辑器都有 CSS 颜色预览器，但是很少有人配有 Atom 的`pigments`包。它解析颜色、CSS 自定义属性、预处理器变量，甚至执行`lighten()`和`darken()`等变色函数。它扫描您的源文件来构建调色板，以便您可以在任何地方引用它们。

![pigments](img/3a1d599ae2287bc068b2cf04885adde3.png)

*   **在这里获取颜料原子包** : [颜料](https://atom.io/packages/pigments)

此外，颜色选择器包是为那些宁愿选择颜色也不愿记住它们的名称或十六进制值的人准备的。

*   **在这里获取拾色器包** : [拾色器](https://atom.io/packages/color-picker)

## 9.棉绒

您可以从命令行运行 linters，但是它不如实时的编辑器内代码验证快速有效。Linter 是其中的佼佼者。它速度快，而且比一些竞争对手更少打扰。

注意，Linter 是核心的 Atom 包，它为几十种编程语言提供 API。有些，如 HTML 和 CSS，不需要更多的软件。其他如`eslint`，需要节点模块和配置设置(提供完整说明)。

林挺你的代码将极大地提高你的代码质量，所以我鼓励你试一试。

*   **在这里领取棉绒包** : [棉绒](https://atom.io/packages/linter)
*   **在这里获取 HTMLHint 包** : [linter-htmlhint](https://atom.io/packages/linter-htmlhint)
*   **在这里获取 Atom CSSLint 包** : [linter-csslint](https://atom.io/packages/linter-csslint)
*   **在这里获取诚信通套餐** : [linter-eslint](https://atom.io/packages/linter-eslint)

## 10.自动检测缩进

编码人员永远不会同意是使用制表符还是空格。即使他们这样做，他们可能更喜欢两个，四个或八个字符的口味。我通常选择让大多数人讨厌的东西(三个字符的硬标签？)但是`auto-detect-indentation`会计算出项目需要什么，所以你永远不用担心。

*   **在这里获取 Atom 自动检测压痕包** : [自动检测压痕](https://atom.io/packages/auto-detect-indentation)

或者，您可以使用 Atom 美化功能强制每个人的代码符合您喜欢的风格:

*   **在这里领取原子美容套装** : [原子美容](https://atom.io/packages/atom-beautify)

## 11.电传打字机

如果你曾经使用过 VS 代码的 [Live Share，你就会明白它是如何革新结对编程的。该扩展允许两个人同时在同一个工作区远程编辑代码。](https://marketplace.visualstudio.com/items?itemName=MS-vsliveshare.vsliveshare)

`teletype`是 Atom 的等价包。这是一个测试服务，但看起来不错，似乎是可靠的。

*   **在此获取原子电传包** : [电传](https://atom.io/packages/teletype)

## 12.更多 Atom 包

在我看来，我们已经讨论了一些最好的 Atom 包。最后，我们将特别提到一些没有排在第一位，但仍然非常有用，值得一看的东西。

*   [Emmet](https://www.sitepoint.com/how-to-code-like-a-zen-master/) (之前称为 Zen Code)可以将类 CSS 的表达式扩展成 HTML 标签: [emmet](https://atom.io/packages/emmet)
*   If you’re creating REST web services, Atom’s REST Client provides a quick HTTP testing tool. It’s no match for powerful alternatives such as [Postman](https://www.getpostman.com/), but is great for quick and dirty testing: [rest-client](https://atom.io/packages/rest-client)

    ![rest-client](img/35992e282b8ac34a29eba74930e6d3e8.png)

*   最后，不需要手动检查更新。`auto-update-packages`每六小时验证你的软件包并为你工作:[自动更新软件包](https://atom.io/packages/auto-update-packages)

## 下班后附加服务

如果你的键数( [keycount](https://atom.io/packages/keycount) )证明你已经完成了一天的工作，那就通过阅读 [xkcd 漫画](https://atom.io/packages/xkcd-comics)来放松一下，或者玩一个快速游戏[俄罗斯方块](https://atom.io/packages/tetromino)、[黑白棋](https://atom.io/packages/blueothello)、[乒乓](https://atom.io/packages/pong)、[蛇](https://atom.io/packages/snake)或[模拟城市](https://atom.io/packages/atom-city)！

![tetromino](img/0f17131119f240fa3327a7b4489fad76.png)

我错过了你最喜欢的 Atom 插件吗？

## 分享这篇文章