# PHP storm——顶级生产力技巧和快捷方式

> 原文：<https://www.sitepoint.com/phpstorm-top-productivity-hacks-shortcuts/>

编码时我们都有自己喜欢的 IDEs 编辑器。我的是 [PhpStorm](http://www.jetbrains.com/phpstorm/) ，花了一段时间才掌握了它所有的快捷方式，建立了快速的工作流程。在本文中，我将与您分享一些键盘快捷键和提示，如果您也在这个 IDE 中工作，您应该将它们作为日常工作的一部分。这篇文章的部分灵感来自于[这个 Reddit 帖子](http://www.reddit.com/r/PHP/comments/1ty06s/phpstorm_the_best_productivity_hack_extended/)，也将提到和展示一些在那里找到的快捷方式。

请注意，当我在 Windows 上的一个流浪盒子中进行 Linux 开发时，我的快捷方式可能与你的不同。JetBrains 参考卡将他们完美匹配，并为您提供 OS X 备选方案:[参考卡](http://www.jetbrains.com/phpstorm/documentation/PhpStorm_ReferenceCard.pdf)。

## 键盘快捷键和窍门

PhpStorm 的高度可配置性(它几乎是 PHP IDE 世界的“Android ”)让你可以对它进行无限的微调——你可以把它变成一个非常个性化的工具，对于任何打开你的设置的人来说，它可能看起来像一个不同的 IDE。让我们看看一些我们可以利用的捷径和窍门:

*   CTRL + ALT + L，我最常用的键盘组合之一，会立即根据首选的样式准则格式化你的代码:
    ![](img/79adbd19e6ce76fe35529951dd41e3c3.png)

    此外，正如您在上面动画中看到的弹出窗口，您可以选择重新格式化整个项目。如果您过度使用它，并且不经常在其他人的项目中工作，那么您就更上一层楼了—[将一个宏](http://www.jetbrains.com/phpstorm/webhelp/binding-macros-with-keyboard-shortcuts.html)绑定到 CTRL+S 快捷键，并让 PhpStorm 在每次手动保存时自动重新格式化您的代码。但是，请注意，如果您与其他人一起从事一个项目，这可能会导致在各种文件中过于频繁地重构，并且用根本不是变更的变更污染您的版本控制系统。如果你在 Linux 上，你的 CTRL+ALT+L 快捷键可能已经绑定了“锁屏”，你需要从系统中解除它的绑定，或者在 PhpStorm 的设置中改变它。

*   按 CTRL+SHIFT+Backspace，或者鼠标上的“前进”和“后退”按钮(如果有的话),将引导您浏览之前的编辑位置。这在处理大型文件时非常有用，因为必须引用另一个部分才能返回到原始部分继续工作:
    ![](img/77a2eed446c49c8b428df73daec6b166.png)

*   当鼠标悬停在变量或方法上时按住 CTRL 键会给出它们的全名或声明位置，而 CTRL+单击变量或方法，或者在方法调用或变量上按 CTRL+B 键会直接带你到定义它的地方:
    ![](img/55c4af7523eeeb9e55a6d3207524c17e.png)

*   CTRL+Space 会召唤一个代码提示对话框，如果你不小心关闭了或者因为某种原因没有打开:
    ![](img/6e886ac7f4d97afdb6940845c98e3003.png)

*   当 PhpStorm 检测到一些问题时，它会在有问题的区域加下划线，当你将鼠标或键盘光标悬停在该区域时，它会在上面召唤一个亮灯。点击这个灯泡或按 ALT+Enter 将会给出解决方案的建议——用任何语言。这可以从关闭被禁止通知的错误报告(我建议[反对](https://www.sitepoint.com/why-suppressing-notices-is-wrong/))到合并 CSS 规则:
    ![](img/f94fb953f3e81e17bae64bd95383c3e1.png)

*   CTRL+N 和 CTRL+SHIFT+N 将让您立即移动到整个项目中任何类或文件的任何部分:
    ![](img/b8c105b6ed2eb2b8a8bf834dcbe48ea7.png)
    搜索是模糊的，也支持行号——您可以使用缩写后跟:XX 其中 XX 是行号:
    ![](img/e5b5d05ae2dd7eb2c20ecbdaefa82e1b.png)

*   PhpStorm 可以记住多个剪贴板内容——你可以按 CTRL+SHIFT+V 来弹出一个窗口，让你粘贴比最新内容更早的剪贴板内容。

*   如果你喜欢 Sublime 搜索 IDE 命令的能力，你也可以这样做。CTRL+SHIFT+A 将生成一个命令搜索弹出窗口。忘了代码重新格式化快捷键是怎么回事了吗？只需 CTRL+SHIFT+A 并键入“refor ..”:
    ![](img/ccd472e45abb9fe0ab5e6e09f8395073.png)

*   使用内置在 PhpStorm 中的 [Emmet](http://emmet.io/) 生成完整的 HTML，而不用写出来。输入`div#myDiv>span.mySpanClass>ul>li*5`并点击`tab`将变成:
    ![](img/202b36e2efad3614dca60557048b8963.png)
    通过在`Settings -> Editor -> Live Templates`对话框中添加你自己的可扩展模板，进一步扩展你选择的任何语言。您可以选择光标的位置，添加变量，等等。完整的 Emmet CSS 和 HTML 备忘单可以在[这里](http://docs.emmet.io/cheat-sheet/)找到。

*   也许有点违反直觉，CTRL+W(最常用于关闭制表符，所以您可能希望重新映射它)将在光标下选择一个代码“单元”(一个单词、一个变量等)。重复该组合然后扩展选择以包括父节点——一直到整个文件(例如函数调用->实例和函数调用->行->整行->方法->包含类…):
    ![](img/cab9bbbc6a3947f3e3484c628977d9c3.png)

*   上下文感知编辑将允许您在专用编辑器中编辑用另一种语言编写的字符串，包括代码提示和自动完成。例如，如果您有一个用 PHP 字符串编写的 HTML 片段，您可以在字符串的内容中按 ALT+ENTER 并选择 Edit HTML content。出现这种情况:
    ![](img/d29167ea30ee64d7354bf996a8407ee1.png)

*   按住鼠标中键并在一段代码上拖动可以调用列选择模式。我发现当我需要粘贴代码中缩进很深的部分，但又不想复制前面的空白时，这特别有用:
    ![](img/53fdbe07370760c69997261ed3deb07c.png)

*   按 ALT+Home 将允许您使用光标在顶部的面包屑导航中移动。ESC 退出此模式并返回到编辑器，除非您在面包屑中选择了一个文件，否则该文件会立即成为焦点。
    ![](img/9d853d2bc58fe102c87e40c9cb7f3942.png)

*   重构(重命名)变量并将更改应用到所有使用它的位置非常简单，只需选择右键单击->重构，按 SHIFT+F6 或者甚至按 SHIFT+ALT+CTRL+T 来调用整个重构菜单，如下图所示:
    ![](img/15b65fafb1845f743b12f85a7a460ea9.png)

*   CTRL+F12 会弹出一个窗口，让你在当前文件中选择一个你想跳转到的方法。即使没有搜索栏，如果你只是开始输入，你可以缩小选择范围，准确地找到你要找的东西。
    ![](img/ef0d8a68f9b88053a815110508acf61e.png)

*   正如这篇推文中的[所描述的，你现在可以通过双击 Shift“搜索任何地方”。这就像是所有以前的搜索快捷方式的强大混合，允许您在项目或 IDE 的任何部分找到您需要的任何东西——从 IDE 命令到类和函数声明等等。](https://twitter.com/phpstorm/status/420597140386099200/photo/1)

## 插件

PhpStorm 有丰富的各种语言插件。例如，我经常使用 PhpStorm 的 Dart 插件。不过，这不是我想谈论的插件类型——也有太多的生产力插件。插件增加了你从未想过 PHP IDE 会有的特性:

*   将 [DynamicReturnType 插件](https://github.com/pbyrne84/DynamicReturnTypePlugin/)与一个好的[依赖注入器](https://github.com/rdlowrey/Auryn)结合起来，正如 Danack 所描述的[，将让你做适当的 IoC，并且仍然保持你所有的自动完成和代码检查，就像直接处理类一样。关于使用这种特殊组合的更详细的文章将很快发表。](http://www.reddit.com/r/PHP/comments/1ty06s/phpstorm_the_best_productivity_hack_extended/cee7vnw)

*   当你执行鼠标操作时，[按键提示器](http://plugins.jetbrains.com/plugin/1003?pr=idea)插件向你显示鼠标操作的快捷键。如果你经常使用某些动作，而它们没有快捷键，它会问你是否想添加它。

*   CSS-X-Fire 允许你在 Chrome 的 Firebug lite 或 Firefox 的 Firebug 中编辑你的 CSS，在切换回 PhpStorm 时，你会被询问是否要将你在浏览器中所做的更改应用到你项目代码中的 CSS。

*   您可能知道 PhpStorm 是一个基于项目的 IDE，这意味着它不能像普通的文本编辑器一样打开和编辑任意的非项目文件。当你需要集思广益或只是为自己写一些笔记，但不想污染你的项目文件夹时，这可能会很尴尬。Scratch 允许你在你的项目中使用临时文件来达到这个目的。

## 可读性强的深色主题

许多人低估了一个好的主题，认为它是天赋或者纯粹的虚荣心。这是错误的——一个好的主题就是一个好的用户体验——它增加了可读性，让你无需思考就能直观地注意到错误、关键词和所有的语法趣味。使用深色主题有几个好处:

*   当看明亮的显示屏时，你的眼睛会很快疲劳，尤其是在晚上(大多数程序员都在晚上工作)。在黑暗的显示器上，你的眼睛不是聚焦在整个明亮的屏幕上，试图找出其中的漏洞(黑暗——字母)，而是立即被明亮的部分——文本——吸引，完全忽略背景。
*   我主观地认为，在较暗的背景下，颜色更容易被注意到。自然，这意味着颜色应该有一个体面的，但不是太强烈的背景对比
*   众所周知，自发光显示器发出的蓝光会抑制褪黑激素，从而扰乱你的睡眠模式。如果你发现自己在凌晨 4 点的编码会议后难以入睡，尽管感觉完全被疲劳抹杀，尝试更暗的主题，并在晚上工作时让自己暴露在更少的光线下。

主题中要寻找的东西是中档对比。避免绝对化——背景不应该是纯黑色，文本不应该是纯白色/红色/绿色/橙色。这些鲜明的对比会使眼睛感到困惑和疲劳，就像全白的背景和全黑的文字一样。相反，寻找柔和的，烟熏色。同样，如果主题的默认字体很小，增加字体通常是个好主意。当阅读太小的字母时，眼睛会疲劳，如果间距也不够好，你将很难不头痛地阅读任何东西。我对黑暗主题的两个建议是 Darcula，它内置在 PhpStorm 中，以及[日光化](http://ethanschoonover.com/solarized)。

[![](img/f8362a5d34602b8b059a006c6b820df5.png)](https://uploads.sitepoint.com/wp-content/uploads/2014/02/phpstorm1.png)

[![](img/696c7ef8c45771d3151e61072f09f4d6.png)](https://uploads.sitepoint.com/wp-content/uploads/2014/02/phpstorm2.png)

除了 Darcula 中更令人愉快的配色方案(如上所示)之外，我是那种在项目中大量使用注释的开发人员，所以让注释更清晰但又不分散注意力是一个有价值的额外好处。在我看来，第二张截图中的项目结构更具可读性——浅蓝色和灰色的对比在我看来是最佳的。

诚然，大多数主题偏好讨论是主观的，但我鼓励你走出眼睛的舒适区一两天，尝试不同的选择。如果你找到一个特别适合你的，你会惊讶于你适应新风格的速度。

## 结论

在本文中，我们介绍并演示了 PhpStorm 的一些流行的和/或非常有用的键盘快捷键和生产力技巧——这是一个比乍看之下想象的更加可定制的 IDE。你有什么技巧和生产力诀窍？你看到有丢失的吗？请在下面的评论中告诉我们，我们会添加它们！

## 分享这篇文章