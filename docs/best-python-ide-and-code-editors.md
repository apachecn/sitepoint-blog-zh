# Python 的最佳 ide 和代码编辑器

> 原文：<https://www.sitepoint.com/best-python-ide-and-code-editors/>

在这篇文章中，我们将列出 9 个最好的 Python ide 和代码编辑器，并且我们还将探索使每一个都独一无二的一些特性。

Python 是一种通用的编程语言，从 web 开发到科学计算，它有许多用途。当谈到为 Python 开发选择 IDE 或代码编辑器时，有许多选项可供选择，选择正确的选项会对您的工作流程产生很大的影响。

**内容:**

1.  [Python 是什么？](#whatispython)
    *   [Python 真的这么容易吗？](#ispythonreallysoeasy)
2.  [什么是 ide 和代码编辑器？](#whatareidesandcodeeditors)
3.  9 个最好的 Python ide 和代码编辑器
    *   [Atom](#atom)
    *   [Visual Studio 代码](#visualstudiocode)
    *   [Vim](#vim)
    *   [PyCharm](#pycharm)
    *   [Spyder](#spyder)
    *   [崇高的文字](#sublimetext)
    *   [PyDev](#pydev)
    *   [千年一遇](#thonny)
    *   [翼蟒 IDE](#wingpythonide)
4.  [IDE 和代码编辑器特性概述](#asummaryofideandcodeeditorfeatures)
5.  [最终想法](#finalthoughts)

## Python 是什么？

我们在[什么是 Python 以及它有什么用途中详细讨论了这个问题？](https://www.sitepoint.com/what-is-python/)，但简而言之，Python 是一种高级编程语言，由[吉多·范·罗苏姆](https://gvanrossum.github.io/)于 1991 年创建，在开源许可下发布，任何想使用或修改软件的人都可以免费使用。

Python 以其易用性而闻名，其语法允许程序员用比 C++或 Java 等语言更少的代码行来表达概念。它的一些突出特点包括:

*   很有趣
*   它是一种通用语言(这意味着它允许我们做任何事情，从构建网页到探索数据科学和人工智能)，也是一种多范式语言(过程化、面向对象和函数式的)的 T2
*   你可以在所有主要操作系统(Linux、Windows 和 macOS)上使用它，甚至可以在[微控制器](https://ffrafat.medium.com/best-microcontrollers-for-python-305dd3135019)上使用它
*   它有一个庞大的开发者社区每天都在使用它
*   它有一个大而高质量的标准库(这就是为什么它经常被称为“包含电池”的语言)
*   它有一个伟大的生态系统，由不同的库、工具和框架组成，帮助你在更短的时间内编写更好的代码——被谷歌、脸书和网飞等公司广泛用于构建大规模的应用程序

### Python 真的有那么容易吗？

Python 代码经常被说成几乎像[伪代码](https://en.wikipedia.org/wiki/Pseudocode)，因为它允许你用非常少的代码行表达非常复杂的算法，同时可读性非常好。

例如，下面是 Python 中经典的[快速排序](https://en.wikipedia.org/wiki/Quicksort)算法的实现:

```
def quicksort(arr):
    # base case for recursion
    if len(arr) <= 1: return
    pivot = arr[len(arr) // 2]            # choose a pivot element from the array
    left = [x for x in arr if x < pivot]  # create subarray with elements less than pivot
    right = [x for x in arr if x > pivot] # create subarray with elements greater than pivot

    # concatenate the arrays and sort recursively
    return quicksort(left) + [pivot] + quicksort(right) 
```

## 什么是 ide 和代码编辑器？

ide，即[集成开发环境](https://en.wikipedia.org/wiki/Integrated_development_environment)和[代码编辑器](https://en.wikipedia.org/wiki/Source-code_editor)是开发人员最常用的两种工具。

IDE 是为软件开发提供工具的软件应用程序。一个**代码编辑器**是一个文本编辑器，具有专门为编程设计的附加功能。它们都提供了语法高亮和代码补全，并且有插件来增加功能。一些现代的代码编辑器甚至具有与 ide 非常相似的功能，比如调试和重构。

一些开发人员更喜欢 ide，因为它们提供了更完整的开发体验。其他人更喜欢代码编辑器，因为它们的轻量级特性。下表比较了它们的各种功能。

| 5 月 | 代码编辑器 |
| --- | --- |
| 通过内置的项目管理、调试和版本控制工具，提供更完整的开发体验。 | 不像 ide 提供那么多功能，但是经常有可以增加功能的插件。 |
| 可能会很慢并且需要大量资源。 | 重量轻，速度快。 |
| 往往有一个陡峭的学习曲线。 | 比大多数 ide 的学习曲线要浅。 |

如你所见，两者各有利弊，所以最终归结为个人喜好。

要了解更多关于 IDE 和代码编辑器的知识，请查看什么是 IDE？它如何实现更快的开发？

## Python 的 9 个最佳 ide 和代码编辑器

为什么有这么多 IDE 和代码编辑器选项？这是个合理的问题。答案是 Python 是一种通用的脚本语言，这意味着它不仅仅可以用于构建 web 应用程序。它也非常适合创建系统脚本和自动化任务，因此您会发现一些 ide 和代码编辑器是专门针对某些用例而设计的。

以下是一些需要考虑的因素:

*   你从哪个平台切换到另一个平台？如果是这样，您将需要多操作系统支持。
*   你喜欢简单的工具吗？你可能更喜欢极简主义的选择。
*   你更愿意使用万能工具吗？以防万一，你可能需要额外的功能。
*   你非常关注代码测试和质量吗？
*   你专攻数据科学吗？
*   你是只编写 Python 代码，还是也使用其他语言？
*   你很在意前沿功能吗？如果没有，你可能只是想要一个工具来完成这件事。

带着这些问题，让我们看看目前最好的 Python IDEs 和代码编辑器，并找到一些答案。

### 原子

![The Atom logo](img/449ed26a4a0e7bf7e4a9d498273394e3.png)

[Atom](https://atom.io/) ，一个“21 世纪的可黑客攻击的文本编辑器”，已经被证明是一个相当创新的编辑器，与 GitHub 有着杰出的集成(嗯，Atom *是由 GitHub 开发的*)，我们以前写过关于 [Atom 插件的文章，用于运行代码和预览更改](https://www.sitepoint.com/7-atom-add-ons-for-running-code-and-previewing-changes/)。

甚至是 Atom 构建的框架——electronic ()也通过允许使用 web 技术构建桌面应用程序带来了大量的创新。(稍后会有更多相关内容。)

然而，2022 年年中， [GitHub 宣布](https://github.blog/2022-06-08-sunsetting-atom/):

> 我们正在为 2022 年 12 月 15 日的正式日落归档 Atom 和 Atom 组织下的所有项目。

因为[微软收购了 GitHub](https://news.microsoft.com/announcement/microsoft-acquires-github/) ，所以决定专注于 Atom 的对手 Visual Studio 代码(VS Code)的进一步开发。这可能是一个好的决定，因为 VS 代码已经成为一个更好的选择。

### Visual Studio 代码

![The VS Code logo](img/c16974227a087fbda42763d7a8d2293e.png)

[VS 代码](https://code.visualstudio.com/)“免费的、基于开源的、可以在任何地方运行的”编辑器[在很大程度上](https://insights.stackoverflow.com/survey/2021#section-most-popular-technologies-integrated-development-environment)是 2023 年代码编辑的标准。还记得 GitHub *为* Atom 开发的电子框架吗？具有讽刺意味的是，VS 代码也是在它之上开发的！

![A screenshot of the VS Code interface](img/bb3baedb73660c8d81cc3e22c9cd391b.png)

VS 代码界面截图。[图片来源](https://code.visualstudio.com/assets/home/home-screenshot-win.png)

VS 代码界面截图

VS Code 在开发社区掀起了一场风暴，原因很简单:它太棒了！它的一些功能包括:

*   是[开源](https://github.com/microsoft/vscode)
*   它有一个全功能的[网页版](https://vscode.dev/)(见 [7 远程结对编程的协同编码工具](https://www.sitepoint.com/collaborative-coding-tools-for-remote-pair-programming/)
*   它[滚动每月更新](https://code.visualstudio.com/updates)(这也是丰富的功能)
*   它有一个活跃的 YouTube 频道和 T2 博客
*   它提供了[内置 Git 支持](https://code.visualstudio.com/#built-in-git)
*   它有一个充满活力的扩展市场
*   它展示了一个[记录良好的 API](https://code.visualstudio.com/api)
*   它有丰富的学习资源

对于微软曾经在支持开发者方面做错的一切(不要让我开始)，该公司这次已经成功地用 VS 代码扭转了局面。而且它的 *Python 支持是顶尖的！*

![IntelliSense and autocomplete for Python code](img/3f8026753c7272b8475d7dd562301fe6.png)

Python 代码的智能感知和自动完成。[图片来源](https://code.visualstudio.com/assets/docs/languages/python/python-editing.gif)

**提示**:查看 VS 代码的 [IntelliCode 扩展(预览)](https://go.microsoft.com/fwlink/?linkid=2006060)。IntelliCode 为 Python 中的 IntelliSense 提供了一组人工智能辅助功能，例如基于当前代码上下文推断最相关的自动完成:

*   自动完成和[智能感知](https://code.visualstudio.com/docs/editor/intellisense)
*   [林挺](https://code.visualstudio.com/docs/python/linting)
*   [调试](https://code.visualstudio.com/docs/python/debugging)
*   [环境支持](https://code.visualstudio.com/docs/languages/python#_environments)
*   [嵌入式 Jupyter 笔记本](https://code.visualstudio.com/docs/datascience/jupyter-notebooks)
*   [测试](https://code.visualstudio.com/docs/python/testing)

上面所有的特性要么是内置的，要么是官方扩展支持的。

![Jupyter notebook running in VS code in the Notebook Editor](img/15fdc6b8ec895ab339edd69bb5803bc3.png)

Jupyter 笔记本运行在 VS 代码的笔记本编辑器中。[图片来源](https://code.visualstudio.com/assets/docs/languages/python/native-jupyter.png)

所以，是的，VS 代码将满足你所有的基本 Python 编码需求(甚至更多)。

[https://www.youtube.com/embed/-udPvjv8jyI?rel=0](https://www.youtube.com/embed/-udPvjv8jyI?rel=0)

<br>

#### extra:Visual Studio(IDE)

![The Visual Studio logo](img/30e5b221921c157e97d723e0597a1085.png)

Visual Studio 是微软的成熟集成开发环境，既可以在[购买](https://visualstudio.microsoft.com/subscriptions/)，也可以在[免费购买](https://visualstudio.microsoft.com/free-developer-offers/)。

Visual Studio 是什么？NET 和 C++开发人员通常用来编写游戏程序(当然还有其他东西)，但是它不会给你的 Python 工作流增加多少价值，所以不要担心。

### 精力

![The Vim logo](img/0a313b32884e59db07232794f18f1363.png)

Vim “无处不在的文本编辑器”，是一个高度可配置的文本编辑器，旨在使创建和更改任何类型的文本都非常高效。它作为“vi”包含在大多数 UNIX 系统和 macOS 中。Vim 非常稳定，并且正在不断发展，变得更好。

![The Vim console](img/2e7f4bda380416e68f7ccb404ed1e995.png)

Vim 控制台。[图片来源](https://upload.wikimedia.org/wikipedia/commons/8/8c/Vim-%28logiciel%29-console.png)

其特点包括:

*   它是持久的
*   它有一个多层次的撤销树
*   它有一个广泛的插件系统
*   它支持数百种编程语言和文件格式
*   它具有强大的搜索和替换功能(使用正则表达式)
*   它提供了集成的区分/合并工具

Vim 不仅仅是一个 IDE。更像是一种生活方式！但是，即使你不采用“Vim 生活方式”，当你需要通过控制台远程快速编辑某些东西时，它仍然可以节省你的时间。此外，你可以从命令行在 Vim 中做任何事情，或者你可以在你的浏览器中使用 [Firefox](https://addons.mozilla.org/en-GB/firefox/addon/vimium-ff/) 或 [Chrome](https://chrome.google.com/extensions/detail/dbepggeogbaibhgnhhndojpepiihcmeb) 来使用它。

但是如果您正在寻找 Python 特有的功能，有一些很棒的插件可以提供您所需的所有功能:

*   you complete me:Vim 的代码完成引擎，除了其他功能之外，它还提供了对 Python 的扩展支持
*   ulti snips:Vim 的一个片段解决方案，与 YouCompleteMe 配合得非常好。

[https://www.youtube.com/embed/Co4S_uJYb1o?rel=0](https://www.youtube.com/embed/Co4S_uJYb1o?rel=0)

<br>

### 皮查姆

![The PyCharm logo](img/21ebd8f0f6cd53f88df84a76ca585e9e.png)

通过 [PyCharm](https://www.jetbrains.com/pycharm/) “面向专业开发人员的 Python IDE”，我们谈论的是一个合适的图形 IDE，我们*最终*谈论 Python，附带的好处包括:

*   智能代码完成
*   即时错误检查和快速修复
*   轻松的项目导航

![The PyCharm interface](img/e0df7e536cbcf38b3707d099b0af544f.png)

py charm 接口。[图片来源](https://www.jetbrains.com/pycharm/img/screenshots/complexLook@2x.jpg)

PyCharm 是一个跨平台的 IDE，可以在 Windows、Linux 或 macOS 上运行。它还有专业版(付费)和社区版(免费)两种版本。专业版为 Python 开发提供了集成的工具集，包括对 Django 的支持。

更详细地说(引自 [JetBrains 网站](https://www.jetbrains.com/pycharm/)):

*   **智能 Python 协助** : PyCharm 提供智能代码完成、代码检查、即时错误突出显示和快速修复，以及自动化代码重构和丰富的导航功能。

*   web 开发框架:PyCharm 为现代 Web 开发框架提供了强大的特定于框架的支持，例如 Django、Flask、Google App Engine、Pyramid 和 web2py。

*   **科学工具** : PyCharm 集成了 IPython Notebook，拥有交互式 Python 控制台，支持 Anaconda 以及包括 matplotlib、NumPy 在内的多个科学包。

*   **跨技术开发**:除了 Python，PyCharm 还支持 JavaScript、CoffeeScript、TypeScript、Cython、SQL、HTML/CSS、模板语言、AngularJS、Node.js 等等。

*   **远程开发能力**:在远程主机或虚拟机上运行、调试、测试和部署应用程序，使用远程解释器、集成的 ssh 终端以及 Docker 和 vagger 集成。

*   **内置开发者工具**:开箱即用的庞大工具集合:集成调试器和测试运行器；Python profiler 内置终端；以及与主要 VCS 和内置数据库工具的集成。

所以没错，这是正经级别的 Python 原生支持。然而，你应该注意到社区版并没有包括这些额外的好处——比如 VCS 支持、科学工具、web 开发、Python web 框架，甚至没有 Python profiler、远程开发功能或者数据库和 SQL 支持。

### Spyder

![The Spyder logo](img/e37268102a22b2a572c9aec958be83e9.png)

[Spyder](https://www.spyder-ide.org/) “科学 Python 开发环境”，是一个跨平台的 IDE，可以在 Windows、Linux 或 macOS 上工作。它也是开源的，在麻省理工学院的许可下可以免费使用。

![A screenshot of the Spyder interface](img/7fb38623959e327658ec590fcadb43a4.png)

Spyder 界面截图。[图片来源](https://upload.wikimedia.org/wikipedia/commons/1/1b/Spyder-windows-screenshot.png)

Spyder 在很多方面与 PyCharm 颇为相似。它们都是支持插件的 Python 原生 ide。但是 Spyder 是专为数据科学而设计的:

*   **[IPython 控制台](https://docs.spyder-ide.org/current/panes/ipythonconsole.html)** ，在一个 GUI 中控制尽可能多的 IPython 控制台
*   **[变量浏览器](https://docs.spyder-ide.org/current/panes/variableexplorer.html)** ，可以动态地与变量进行交互和修改
*   **[绘图](https://docs.spyder-ide.org/current/panes/plots.html)** ，浏览、缩放、复制和保存您创建的图形和图像
*   **[调试器](https://docs.spyder-ide.org/current/panes/debugging.html)** ，交互跟踪代码执行的每一步
*   **[帮助](https://docs.spyder-ide.org/current/panes/help.html)** ，即时查看任何对象的文档，并呈现自己的

由于 Spyder 在引擎盖下使用 IPython 控制台，你可以随时打开一个`spyder`控制台，用它代替 [Jupyter 笔记本](https://jupyter.org/)。

与 PyCharm 不同的是，无论您是否向该项目捐款，您都可以访问所有这些功能。但你也可以为 Spyder 提供资金支持其发展。😁

[https://www.youtube.com/embed/E2Dap5SfXkI?rel=0](https://www.youtube.com/embed/E2Dap5SfXkI?rel=0)

<br>

### 崇高的文本

![The Sublime Text logo](img/3ef8484af350c7e788b5d16de53d0eb8.png)

Sublime Text 是一个复杂的代码、标记和散文文本编辑器。它也是跨平台的(Windows、macOS 或 Linux ),并作为付费产品提供(99 美元)。

然而，Sublime 文本可以免费下载和评估。虽然“必须购买许可证才能继续使用”，但在实践中，目前对评估没有强制的时间限制(除了弹出窗口和定期提醒)。

![The Sublime Text interface](img/deda6917d2510c0c2a1b57c2b57a447e.png)

崇高的文字界面。[图片来源](https://www.sublimetext.com/screencasts/osx_image_4_light_packed.png)

我必须说，直到几年前，Sublime Text 提供了一种优质的体验，因为它的界面设计简洁，资源消耗低(很明显，它背后有高质量的产品管理和软件工程)。但是男孩在过去的五年里对 VS 代码失去了动力。

![Sublime Text vs Visual Studio Code chart](img/74a9a13bf3c3109b3157e12828d86a72.png)

崇高文本 vs Visual Studio 代码

无论如何，不管怎样，Sublime Text 确实为最新的 Python 版本提供了[更新支持:](https://www.sublimetext.com/blog/articles/sublime-text-4)

> Sublime Text API 已经更新到 Python 3.8，同时保持了与为 Sublime Text 3 构建的包的向后兼容性。API 得到了显著的扩展，增加了允许 LSP 这样的插件比以前更好地工作的特性。

#### 号外:崇高合并

说到制作精良的 UI 和 IX，你听说过 [Sublime Merge](https://www.sublimemerge.com) 吗？

它提供了卓越的 Git 体验，远胜于 VS 代码。引用 Siblime 合并主页，它提供了以下功能:

*   **更快的工作方式**:Sublime Merge 拥有灵活的跨平台 GUI 工具包、无与伦比的语法高亮引擎和定制的高性能 Git 阅读库，为性能设立了标杆。

*   **精确灵活**:通过逐行和大块分段提交您想要的内容。选择一行或多行以将大块分割成多个变更。

*   Sublime 突出显示:借助 Sublime 文本语法突出显示的强大功能，您可以准确地了解提交中发生了什么变化。有了 40 多种现成的受支持语言，以及已安装的第三方语法的自动加载，我们已经覆盖了您。

*   真正的 Git:当你使用 Sublime Merge 时，你就是在使用 Git。查看您正在使用的确切 Git 命令，并在命令行和 Sublime Merge 之间无缝转换。

*   **强大的搜索**:寻找提交？使用“随键入查找”搜索来查找您要查找的确切提交。

*   您的 Git 客户端:使用适应性强的布局和强大的主题化系统让它成为您的。

干得好，[崇高 HQ](https://www.sublimehq.com/) 。👏

### PyDev

![The PyDev logo](img/6b211692f5c7d8653e73add2865e1a05.png)

[PyDev](https://www.pydev.org/) 是“用于 Eclipse 的 Python IDE，可用于 Python、 [Jython](https://www.jython.org/) 和 [IronPython](https://ironpython.net/) 的开发”。因为它运行在 Eclipse IDE 之上，所以它也需要 Java。

![The PyDev interface](img/431e1ed2c4215c263e3802bf5917eeee.png)

PyDev 接口。[图片来源](https://upload.wikimedia.org/wikipedia/commons/c/c9/Screenshot_Vrapper.png)

PyDev 增加了许多通用特性，以及许多 Python 特有的特性:

*   [Django 整合](https://www.pydev.org/manual_adv_django.html)
*   [代码完成](https://www.pydev.org/manual_adv_complctx.html)
*   [自动导入代码完成](https://www.pydev.org/manual_adv_complnoctx.html)
*   [类型提示](https://www.pydev.org/manual_adv_type_hints.html)
*   [代码分析](https://www.pydev.org/manual_adv_code_analysis.html)
*   [转到定义](https://www.pydev.org/manual_adv_gotodef.html)
*   [重构](https://www.pydev.org/manual_adv_refactoring.html)
*   [调试器](https://www.pydev.org/manual_adv_debugger.html)
*   [远程调试器](https://www.pydev.org/manual_adv_remote_debugger.html)
*   [在调试器中查找引用者](https://www.pydev.org/manual_adv_debugger_find_referrers.html)
*   [代币浏览器](https://www.pydev.org/manual_adv_open_decl_quick.html)
*   [互动控制台](https://www.pydev.org/manual_adv_interactive_console.html)
*   [`unittest`整合](https://www.pydev.org/manual_adv_pyunit.html)
*   [代码覆盖率](https://www.pydev.org/manual_adv_coverage.html)
*   [皮林积分](https://www.pydev.org/manual_adv_pylint.html)
*   [MyPy 集成](https://www.pydev.org/manual_adv_mypy.html)

正如你所看到的，质量保证是重点，所以如果你是一个 QA 专家，你可能会喜欢 PyDev。

#### Visual Studio 代码上的 PyDev

尽管 PyDev 通常与 Eclipse 一起使用，但是现在也可以在 VS 代码上利用 PyDev 的特性了！

> 虽然还需要添加一些重要的特性(比如调试器)，但是当前版本已经可以利用许多特性，使得 PyDev 在 Visual Studio 代码中独一无二！

只需将这些扩展添加到 VS 代码中，如下图所示。

![VS Code extensions for PyDev](img/dbd008510fdea55fd9394c99677d6189.png)

VS PyDev 的代码扩展。[图片来源](https://www.pydev.oimg/vscode/install.png)

### 托尼

![The Thonny logo](img/2369a0055af5099e58c6890450925705.png)

“面向初学者的 Python IDE”Thonny，是一个非常简单、非常轻量级(~20MB)，但功能相当不错的 Python 代码编辑器/IDE——它甚至已经内置了 Python 3！

尽管它很简单，但它提供了变量跟踪器、调试器、单步执行表达式计算、代码完成——几乎是您在 IDE 中所期望的一切。同样，仅用 20MB。哇🤯

![A screenshot of the Thonny interface](img/3be6f9992acac00d5b416f2638897999.png)

Thonny 界面截图。[图片来源](https://thonny.org/img/screenshot.png)

该项目是开源的，托管在 [GitHub](https://github.com/thonny/thonny) 上。

查看演示！

[https://www.youtube.com/embed/nwIgxrXP-X4?rel=0](https://www.youtube.com/embed/nwIgxrXP-X4?rel=0)

<br>

### Wing Python IDE

![The Wing IDE logo](img/08fc76e207e2bb962c3fe15d28e45494.png)

[Wing Python IDE](https://wingware.com/) ，“Python 的智能开发环境”，我们列表中最新的 Python IDE，有三个版本(参见[完整对比](https://wingware.com/downloads)):

*   **[Wing 101](https://wingware.com/downloads/wing-101)** :一个非常简单的免费 Python IDE，专为初学者设计
*   **[Wing Personal](https://wingware.com/downloads/wing-personal)** :为学生和爱好者设计的免费 Python IDE
*   **[Wing Pro](https://wingware.com/downloads/wing-pro)** :专为专业开发者设计的全功能 Python IDE

![A screenshot of the Wing IDE interface](img/b995c09586631adca64a58427c678e3d.png)

Wing IDE 界面截图。[图片来源](https://wingware.cimg/screenshots/wing7-screenshot-linen-small.jpg)

它还展示了您在 IDE 中所期望的一切，但目标是 Python 开发:

*   智能编辑器，具有上下文相关的自动完成和文档、内联错误检测和代码质量分析、PEP 8 实施、调用协助、自动编辑、重构、代码折叠、多选、可定制的代码片段等等
*   **[强大的调试器](https://wingware.com/wingide/debugger)** ，以交互方式修复 bug 和编写新的 Python 代码。
*   **[轻松码导航](https://wingware.com/wingide/navigation)**
*   **[项目管理](https://wingware.com/wingide/project)** ，与由`virtualenv`、`pipenv`、`conda`和 Docker 管理的 Python 环境一起工作
*   **[集成单元测试](https://wingware.com/wingide/testing)** ，带有`unittest`、`doctest`、`nose`、`pytest`和 Django 测试框架
*   **[远程开发](https://wingware.com/wingide/remote)** ，无缝且安全地使用 Wing 的所有功能，同时处理运行在远程主机、虚拟机、容器或集群上的 Python 代码
*   可定制可扩展，通过编写调用 Wing 脚本 API 的 Python 代码，可以添加新的 IDE 特性
*   **[还有更多](https://wingware.com/wingide/other)** :用黑色、YAPF、autopep8 重新格式化代码；缩进样式转换；远程开发到 Raspberry Pi 和其他 ARM Linux 设备

而且有意思的是，[很多专门的 Python 书籍](https://wingware.com/support/books)一直在用 Wing！

## IDE 和代码编辑器功能概述

这里是我们要查看的所有 IDE 和源代码编辑器:

|  | 类型 | 开放源码 | 免费使用 | 原生 Python 支持 |
| --- | --- | --- | --- | --- |
| 原子 | 编者ˌ编辑 | 是 | 是 | 不 |
| Visual Studio 代码 | 编者ˌ编辑 | 是 | 是 | 部分(通过扩展) |
| 可视化工作室 | 集成驱动电子设备 | 不 | 是 | 部分(通过扩展) |
| 精力 | 编者ˌ编辑 | 是 | 是 | 部分(通过插件) |
| 皮查姆 | 集成驱动电子设备 | 不 | 社区(有限) | 是 |
| Spyder | 集成驱动电子设备 | 是 | 是 | 是 |
| 崇高的文本 | 编者ˌ编辑 | 不 | 评估(弹出窗口) | 不 |
| PyDev | 集成驱动电子设备 | 是 | 是 | 是 |
| 托尼 | 集成驱动电子设备 | 是 | 是 | 是 |
| 翼 | 集成驱动电子设备 | 不 | 个人(有限) | 是 |

## 最后的想法

[https://www.youtube.com/embed/G5mtQhWNezQ?rel=0](https://www.youtube.com/embed/G5mtQhWNezQ?rel=0)

<br>

Python 是一种多功能的语言，你可以用它来构建各种各样的应用程序，通过合适的 ide 或代码编辑器，你可以提高你的工作效率，让 Python 开发变得更加容易和有趣。

如果你刚刚开始 Python 开发(参见[如何更快地学习编程语言](https://www.sitepoint.com/how-to-learn-programming-languages-faster/))，我建议你尝试一下这些 ide，看看哪一个最适合你。如果我很紧张，我会说 PyCharm 和 Visual Studio 代码分别是 Python 开发的最佳 IDE 和代码编辑器。亚军是 Wing Python IDE 和 Sublime Text，它们是很棒的 IDE 和代码编辑器。如果你碰巧在一个控制台上远程编辑某个东西，Vim 将获得荣誉奖。

无论如何，如果你已经在使用你喜欢的 IDE 或者代码编辑器，不管是哪一种，那就彻底地学习它吧！像了解和使用工具提供的所有快捷方式这样简单的事情可以让你的工作流程一天比一天不同。因此，请确保您了解您的工具！

## 分享这篇文章