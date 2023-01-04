# 括号编辑器综述

> 原文：<https://www.sitepoint.com/review-brackets-editor/>

本文将介绍最初由 Adobe 开发的开源编辑器[括号](http://brackets.io/)。它可能没有 GitHub 的新轻量级编辑器 Atom 那样的宣传和炒作水平，但对于前端开发人员来说，这是一个有前途的选择。

## 目标受众

值得指出的是，括号主要是针对前端开发人员和网页设计师的。尽管它本质上是一个文本编辑器，因此适合编写几乎任何东西，但它是针对 HTML、CSS 和 JS(以及 SASS、Less、CoffeeScript 等衍生工具)优化的。Ruby、Python、PHP 开发人员等*可能*在其他地方会更好，无论是成熟的 IDE 还是更轻量级的东西，比如 Sublime 或者 Atom。

## 是什么让括号与众不同？

括号强调了这样一个事实，即它是使用 HTML、CSS 和 JavaScript 的组合编写的。虽然这对普通用户来说意义不大，但那些希望扩展甚至破解它的人有机会使用他们可能已经熟悉的技术来这样做。事实上，它的开源也使它脱颖而出。比如 Sublime 是商业产品，而 Atom 是闭源的，不会永远免费。

## 获取括号

支架可以从网站下载[，适用于 Mac OSX、Windows 和 Linux (32 或 64 位)。为了这篇评论的目的，我正在 Mac OSX 上跑第 37 次“冲刺”。](http://brackets.io/)

## 第一步

当您第一次打开括号时，您会直接进入一个带有 CSS 的基于 HTML 的教程文件，这是一个开始探索的好方法。

![The Brackets editor](img/94b3ae4f4325ca457ee120236300defa.png)

左栏列出了工作文件，下面是一个文件夹视图。它就像 Sublime 一样，你可以一次点击查看一个文件，而不用真正打开它。双击打开文件进行编辑，将其添加到工作文件列表中。

右边一栏包含了打开实时预览(我们很快就会看到)和扩展管理器的图标。许多第三方扩展创建了自己的图标，并将它们放在这个栏中，因此它可以很快成为一个无价的发射台。

## 快速特色旅游

让我们来看看括号的一些关键特性。

### 实时预览

实时预览功能会启动一个新的 Chrome 窗口，显示当前页面，不仅在您更改文件时不需要手动刷新，而且在您键入时也不需要。它本质上是 Chrome 的 Web Inspector，但拥有自包含编辑器的所有好处。这是一个真正伟大的功能，特别是如果你有一个 splt-screen 设置。它也不仅仅适用于 HTML 文件。如果你对一个链接的 CSS 文件做了一个影响当前页面的改变，这也会立即在浏览器中反映出来。如果您正在编辑当前可见页面使用的 CSS 文件，突出显示规则会导致预览突出显示受其影响的元素。

### 快速编辑

快速编辑功能是特定于上下文的。让我们看看它的一些用途。

#### 半铸钢ˌ钢性铸铁(Cast Semi-Steel)

当您编辑 HTML 时，如果您单击链接文件中带有相应 CSS 声明的标签并点击`Ctrl / Command + E`，会出现一个行内编辑器，允许您快速编辑该规则。下面的截图很好地说明了这一点。

![Inline CSS editing](img/79538118fb07de56e58f17c5e5c90943.png)

#### 旗帜

将鼠标悬停在 CSS 声明中的颜色上，会显示该颜色的一小块样本。同样，同样的键盘快捷键(`Ctrl / Command + E`)给了你一个非常复杂的颜色选择器/转换器，如下图所示。

![The Color selector](img/ef64e454b4fe10f7e20f67cb19e7e83c.png)

#### 曲线

虽然不太可能像 CSS 规则或颜色编辑器那样经常使用，贝塞尔曲线编辑器是一个非常令人印象深刻的小功能。同样，最好用截图来说明:

![Editing beziers in Brackets](img/66c5626cb1589dde98e124c5b4fda248.png)

#### 更多

看起来第三方扩展将为快速编辑提供额外的上下文，就像本文后面描述的正则表达式编辑器一样。看看生产了什么会很有趣；很明显有很多可能性。

### JSLint

默认情况下，内置的 JSLint 支持将在保存时检查您的 Javascript 代码，其结果显示在主编辑窗口下方的面板中。

![JSLint in action](img/bec8089eb163fddc90393cb20590cbf0.png)

因为括号在保存时运行 JSLint 进程，而不是在保存过程中运行，所以它会感觉有点反应迟钝；您可以更正某些内容，但报告会保留在屏幕上，因为您没有保存它。然而，以任何其他方式进行操作的性能影响可能会产生自己的问题。

### 其他功能

正如您对代码编辑器的期望，括号实现了代码完成。而且，很快。非常快。我发现许多编辑在过去感觉迟钝，这对体验有负面影响，但在这里不是这样。这并不是说它是完美的。例如，在编写 SASS 时，如果一旦您开始键入嵌套选择器(即，带有句点或散列)，代码补全将停止提示您输入规则，这将是一件好事。

![Autocomplete isn't perfect](img/12706fe89f8ecf9cae6a73044a95ef30.png)

## 扩展支架

我觉得括号的真正力量在于它的延伸潜力。已经有大量的扩展可用。我要去看看几个让我印象深刻的。安装扩展的过程再简单不过了。只需点击右上角的“砖块”图标，您就可以在应用程序中搜索并安装它们。

![Installing extensions from within Brackets](img/8d538d822ebc70c71b56147f35aae765.png)

如果你正在寻找其他代码编辑器中的特性，有对[代码折叠](https://github.com/thehogfather/brackets-code-folding)、[片段](https://github.com/jrowny/brackets-snippets)和[智能突出显示](https://github.com/enturn/brackets-quick-search)等的扩展。Theseus 是一个用于括号的 JavaScript 调试器，可用于 Chrome 和 Node.js。它可能有自己的文章，所以我不会在这里详细介绍它。

如果您使用 Markdown，您可能会发现 [Markdown 预览](https://github.com/gruehle/MarkdownPreview)扩展非常有用。当你在括号中打开或创建一个 Markdown 文件时，它会将屏幕水平分成两个面板，一个用于编辑，一个用于实时预览。虽然这并不能提供像 Mou 这样的专门的 Markdown 编辑器所不能提供的任何东西，但是它的一个好处是你可以在不离开编辑器的情况下编辑项目的 Markdown 文件(例如 GitHub READMEs)。不利的一面是，它不能做你所期望的一切，比如让按键显示粗体或斜体。下面显示了这个扩展的一个屏幕截图。

![The Markdown extension in action](img/7ec458d9bf20a4c10ffb4c44789761ee.png)

对于 Node.js 开发人员来说，[这个扩展](https://github.com/briancraig/brackets-nodejs)允许您停止和启动应用程序，并在编辑器中查看它们的输出。

[Autoprefixer](https://github.com/mikaeljorhult/brackets-autoprefixer) 模块解析 CSS 声明，并使用来自[的最新浏览器信息自动将供应商前缀添加到适当的规则中。](http://caniuse.com/)

如果你处理的文件扩展名没有给出语言，比如`.inc`文件，伪装成`.module`文件的 PHP 文件，或者实际上没有扩展名的文件，那么[语言切换器](https://github.com/p4bl1t0/brackets-languageswitcher)模块可能会有所帮助。该模块如下所示。这也意味着您可以利用特定于语言的特性，比如在保存新文件之前对其进行语法高亮显示。

![The file language switcher provides a new drop-down menu to specify a file's language](img/70e063d7a3bd474ca481f63a7796efd6.png)

如果像我一样，正则表达式会让你头疼，那么甚至有一个[内联正则表达式编辑器](https://github.com/peterflynn/brackets-regex-editor)。这与其他快速编辑上下文(如颜色和贝塞尔曲线)的工作方式相同，表明这是一个贡献特别活跃的领域。

您甚至可以从编辑器内部运行终端。

## 结论

括号感觉像是 Chrome 的开发者工具的延伸。实时预览功能非常有用，结合了浏览器编码和专用编辑器的优点。对我来说，它的另一个杀手级功能是快速编辑，尽管感觉离它的全部潜力还有一段距离。然而，通过利用用户的网络技术知识，任何缺口似乎都可能很快被贡献所填补。

从个人的角度来看，我不认为我会使用 PHP 之类的语言来做服务器端的工作。对于更多的前端开发，我可以看到它成为我的工具箱中有用的一部分。如果你是一名前端开发人员，那么我建议你至少尝试一下——毕竟，它是免费的。

## 分享这篇文章