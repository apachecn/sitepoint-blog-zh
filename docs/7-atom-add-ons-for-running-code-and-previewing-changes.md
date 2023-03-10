# 7 个 Atom 附加组件，用于运行代码和预览更改

> 原文：<https://www.sitepoint.com/7-atom-add-ons-for-running-code-and-previewing-changes/>

![Seven Atom add-ons](img/440c7ea37df4e4cc28206150bd68175f.png)

在本文中，我将介绍代码编辑器 [Atom](https://atom.io/) 的一些包，这些包有助于在您键入时预览更改(特别是对于 web 开发)，以及编译和执行代码，而无需从编辑器切换到控制台或 <abbr title="integrated development environment">IDE</abbr> 。我还将评论为什么使用它们是一个好主意。

*注意:`Ctrl|Cmd`表示显示快捷方式时，PC 的“control”键，或 Mac 的“command”键。*

## 可视化实时变化

从一个程序(编写代码的程序)转换到另一个程序(可视化事物的程序)不仅需要时间成本，还需要认知成本。也就是说，这会降低你的工作效率，因为当你从一项任务转换到另一项任务时，你会失去注意力和脑力。(参见“[多任务处理的高成本:任务切换导致 40%的生产力损失](https://www.wrike.com/blog/high-cost-of-multitasking-for-productivity/)”和“[多任务处理的真实成本](https://www.psychologytoday.com/blog/brain-wise/201209/the-true-cost-multi-tasking)”，来阐明这个话题。)

另一方面，能够对您的编程获得即时反馈，并在您键入时看到反映的更改(如果可能的话)将使您节省大量工作时间和精力，因为您不需要切换任务和应用程序，并且在很大程度上，编程、可视化和调试的行为变成了一项任务。

我将向您展示一些社区包，这些包只需轻轻一击就可以启用/禁用可视化更改，这样我们就可以在 Atom 中保留所有内容—编辑和查看。

### webdav

与主要为 web 设计的代码编辑器[不同，Atom 没有内置的 HTML 和 CSS 代码预览。这实际上有点奇怪，考虑到 Atom 是建立在](http://brackets.io/)[电子](http://electron.atom.io/)之上的，这是一个已经使用 [Chromium](https://www.chromium.org/) (谷歌 Chrome 浏览器背后的开源项目)作为其引擎一部分的框架。

尽管如此，由于 Atom 的“可攻击性”，这里有一些包可以为编辑器带来这种功能。

#### Atom HTML 预览

**[Atom HTML 预览](https://atom.io/packages/atom-html-preview)** 显示 HTML 文档的实时、随输入预览，支持 CSS 和 JavaScript。这对于今天的 web 开发来说有些基础，但是对于我们大多数人来说，它会做得很好。这是一个简单、轻量级的包，完全符合预期。

![Atom HTML Preview](img/824f13119b0c56388eaf2c9090104d28.png)

要安装:`apm install atom-html-preview`

#### 浏览器升级版

**[Browser Plus](https://atom.io/packages/browser-plus)** 是一个可以让你在 Atom 标签中打开浏览器的包，它甚至附带了开发者工具，可以方便地进行调试！

对于实时更改预览，您需要单击 thunderbolt 图标，但是请注意，您仍然需要保存您正在编辑的文件(`Ctrl|Cmd+S`)，以便可视化更改。为了使“超级”实时预览能够在您键入时显示更改，同时支持预览 JSP/PHP/Express/Django 以及其他语言和框架中的更改，您需要安装配套的软件包 **[Preview Plus](https://atom.io/packages/pp)** (PP)。

![Browser Plus](img/1c45a004a21433e980cdc3ee4e1a7f49.png)

`apm install browser-plus`安装:
安装【hyper】预览版等框架支持:`apm install pp`

#### Atom 的 Bootstrap 3 支持

**[Bootstrap 3 对 Atom](https://atom.io/packages/atom-bootstrap3)** 的支持，现在我们变得更加具体，带来了对 Twitter 的 web 开发框架的支持， [Bootstrap](http://getbootstrap.com/) 。它可以与前面提到的 Atom HTML 预览版一起顺利工作，并且具有以下特性:

*   支持 JavaScript、CSS 和 HTML 中所有助手类的自动补全。
*   对所有组件的代码片段支持。
*   字形图标、字体和八字形图标的自动完成

![Bootstrap 3 Support for Atom](img/fb98457a55d674e1d17fd9fae1bbbb0d.png)

要安装:`apm install atom-bootstrap3`

#### Atom 预览

如前所述，如今的 web 开发不仅仅是编辑 HTML、CSS 和 JavaScript 文件。如今，使用预处理程序如 [Less](http://lesscss.org/) 和 [Sass](http://sass-lang.com/) 来生成样式表，或者用像 [TypeScript](https://www.typescriptlang.org/) 或 [CoffeeScript](http://coffeescript.org/) 及其衍生语言来编写代码，这些代码随后会编译成 JavaScript 代码是很常见的。使用所有这些工具的缺点是，它使得可视化和调试实际的最终代码变得更加棘手。如果您正在大量使用框架或库，如脸书的 [React](https://facebook.github.io/react/) ，那么即使突出显示普通的 JavaScript 代码也没有什么帮助。

Atom 的 **[预览是一个非常广泛的包，它通过允许您预览编译形式的源代码(即实际的最终代码)来处理这些场景。](https://atom.io/packages/preview)**

它支持以下语言、框架和库:

*   咖啡脚本
*   文学咖啡脚本
*   较少的
*   翡翠
*   狗的描述
*   以打字打的文件
*   唱针
*   DSON
*   反应(JSX)
*   EmberScript
*   ng-分类
*   直播脚本

它还计划在不久的将来支持 Sass、Markdown、Haml、ClojureScript 和 Dart。

其特点包括:

*   在单独的选项卡中预览编译形式的源代码
*   预览的实时更新
*   显示加载和错误消息
*   标签更改时的更新
*   使用主动原子主题突出显示
*   用户可以手动更改默认预览渲染器
*   SpacePen 视图可以实时预览

![Preview for Atom](img/b2c3a44b5c1c5c407d8297b646bb3982.png)

要安装:`apm install preview`

### 降价

![Markdown](img/2359be73ae3814f388ffffad91a41a20.png)

[来源](https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/208px-Markdown-mark.svg.png)

[Markdown](http://daringfireball.net/projects/markdown/) 是一种轻量级标记语言，仅使用纯文本创建富文本，由于它非常[易读](https://en.wikipedia.org/wiki/Human-readable_medium)它经常被用于格式化自述文件，以及在线论坛中的消息，稍后将被呈现为 HTML。

#### 降价预览

Atom 自带编辑和可视化这些文件的内置支持，由 **[Markdown Preview](https://github.com/atom/markdown-preview)** 包提供，无需安装。扩展名为`.md`的文件将自动使用 Markdown 语法高亮显示，要打开预览选项卡，您可以使用默认快捷键`Ctrl|Cmd+Shift+M`。

![Markdown preview](img/c18684558bf1ea0eee0ae0392cd325ec.png)

安装:(捆绑，无操作)

#### 减价预览增强版

**[Markdown Preview Plus](https://atom.io/packages/markdown-preview-plus)**(MPP)是对之前软件包的社区扩展，增加了一些附加功能:

*   打开预览。`Ctrl|Cmd+Shift+M`快捷方式的替代物。
*   LaTeX 方程渲染。用等式块扩展 GitHub 风格的 markdown 的语法。
*   [Pandoc](http://pandoc.org/) 支持。更丰富的功能，包括支持定义自定义 pandoc 参数，并支持引用替换。
*   按需同步。同步 Markdown 源代码编辑器和按需预览的位置。

![](img/dcf1e10ca6bee52da793982f1f9cb61a.png)

要安装:`apm install markdown-preview-plus`

## 执行解释和编译的代码

**[脚本](https://atom.io/packages/script)** 是一个名字简单的扩展，它带来了你在专门的 ide 上更常见的功能，这将为你节省测试和调试的时间。基本上，它允许您执行解释型语言(如 PHP 或 Python)的代码，甚至是编译型语言(如 C 或 Java ),并在 Atom 的另一个窗格中查看结果，就在您正在编辑的代码旁边。此外，对于大多数语言，它允许您完整地执行(或编译，然后执行)正在编辑的文件，或者只执行其中的一部分(当您选择一段文本时)。

它提供了许多快捷方式来运行(使用和不使用编译概要文件，以及使用选项)和终止进程，以及关闭输出视图——一切只需几个按键。可以选择对输出进行计时，以查看脚本/程序执行了多长时间。

您还可以方便地传递将在编译或执行时被替换的标签，如`{FILE_ACTIVE}`或`{PROJECT_PATH}`。

令人惊讶的是，安装和大多数 Atom 包一样简单。您只需要确保您需要的解释器或编译器都在您的`PATH`环境变量中。

支持的语言和编译器列表非常全面:1C (BSL)、Ansible、AutoHotKey、AppleScript、Babel ES6 JS、Bash、Batch、Behat Feature、BuckleScript、C、C#、C# Script、C++、Clojure、CoffeeScript(普通和识字)、Crystal、Cucumber(小黄瓜)、D、Dart、DOT (Graphviz)、Elixir、Erlang、F#、F*、Fish、Forth(通过 GForth)、Fortran(通过 gfortran)、Gnuplot、Go、Groovy、Haskell(普通 Lua(普通和 WoW)、Makefile、MATLAB、MIPS、MongoDB、MoonScript、NCL、newLISP、Nim(和 NimScript)、NSIS、Objective-C、OCaml、Octave、Oz、Pandoc Markdown、Perl、Perl 6、PHP、PostgreSQL、PowerShell、Processing、Prolog、Python、R、rack、RANT、Reason、Ren'Py、RSpec、Ruby、Ruby、Ruby on、Rails、Rust、Sage、Sass/SCSS、Scala、Scheme、Shell 脚本、Standard ML、Stata、Swift、TCS

这就是用脚本执行代码的样子:

![Script](img/993f837e6949cbffa66f7af574f6b06c.png)

要安装:`apm install script`

## 下一步做什么

精通你的代码编辑器——不管它是什么——是成为一个熟练程序员的一个关键方面，因为它将极大地提高你的工作流程。所以，如果你还没有熟悉这些快捷方式，去谷歌一下你最常做的动作，看看是否有简化的方法。或者，如果你在使用 Atom、[搜索包](https://atom.io/packages)这样的黑客编辑器，或者考虑自己写一个，如果从长远来看能节省你和其他人的时间。

其他 SitePoint 推荐阅读:

*   [12 个最受欢迎的 Atom 技巧和快捷方式，改善您的工作流程](https://www.sitepoint.com/12-favorite-atom-tips-and-shortcuts-to-improve-your-workflow/)。
*   [如何为 GitHub 的 Atom 代码编辑器开发一个包](https://www.sitepoint.com/how-to-write-a-syntax-highlighting-package-for-atom/)。
*   [10 个必不可少的原子附加物](https://www.sitepoint.com/10-essential-atom-add-ons/)。

官方 Atom 资源:

*   命令调板(快捷键):`Ctrl|Cmd+Shift+P`
*   [原子飞行手册](http://flight-manual.atom.io/)
*   [Atom 包](https://atom.io/packages)
*   [Atom 博客](http://blog.atom.io/)

这是我发现非常有用的 7 个附件。请在下面的评论中告诉我们你最喜欢的。

## 分享这篇文章