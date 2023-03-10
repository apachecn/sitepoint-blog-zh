# NetBeans 6.5 中的 PHP 支持

> 原文：<https://www.sitepoint.com/php-support-in-netbeans-65/>

**长久以来被斥为“玩具 Java IDE”，[NetBeans](http://www.netbeans.org/)——Sun 的开源集成开发环境(IDE)——近年来确实成长起来了。它也不再仅仅适用于 Java:对于 web 开发人员来说，NetBeans 6.5 现在开箱即可支持 Ruby 和 PHP。令人惊讶的是，这种支持是如此之好，以至于它现在比得上更成熟的竞争对手，如 [Eclipse](http://www.eclipse.org/pdt/) 、 [Komodo IDE](http://www.activestate.com/Products/komodo_ide/) 和 [Zend Studio](http://www.zend.com/en/products/studio/) 。**

我已经习惯了在一个简单的文本编辑器中编写我的 PHP 代码(如果你想知道的话， [jEdit](http://jedit.sourceforge.net/) )，所以我已经有一段时间没有享受到项目级代码完成、代码重构和完整调试器的乐趣了。NetBeans 6.5 中提供了所有这些功能以及更多功能。

NetBeans 不仅为内置函数提供了自动完成功能…

![Screenshot showing the NetBeans code editor recognizing and autocompleting the str_repeat function built into PHP, along with pop-up documentation](img/9a2d5e82b5a969d03ef58fe4bbea7a7d.png)

还包括项目中其他地方定义的函数和包含的库:

![Screenshot showing the NetBeans code editor recognizing and autocompleting the registerObserver method on a custom PHP class, along with pop-up documentation](img/908ea6efaf8bf7e367b855b96ca82da8.png)

在这两种情况下，NetBeans 都会在代码中选取 [phpdoc](http://www.phpdoc.org/) 注释，并使用它们来显示弹出文档及其自动完成建议。

为函数或方法选择代码完成后，NetBeans 会帮助您填写每个参数。填写完一个参数后，按 Enter 键自动移动到下一个参数。

![Screenshot showing the NetBeans code editor with a box around the first parameter of a str_replace function call, to indicate it is selected for editing](img/e359c95f772f0b2b5176d70f39ce8f69.png)

NetBeans 还为喜欢创造编码速度记录的开发人员提供了 PHP 代码模板。例如，键入`fnc`，然后按 Tab 键。NetBeans 会将其展开为完整的函数声明，并在将光标置于函数体之前提示您填写函数名:

![Screenshot showing the NetBeans code editor with a box around the function_name portion of a PHP function delcaration](img/4aa0ea11876c4ddbc9c42a23a64ec393.png)

因为拥有完整的 phpdoc 文档可以使 NetBeans 中的编码变得更加容易，NetBeans 甚至可以帮助您编写文档！将光标放在函数声明的正上方，键入`/**`并按回车键。NetBeans 将自动生成一个 phpdoc 注释，其中包含该函数的每个参数及其返回值的占位符:

![Screenshot showing the NetBeans code editor having just auto-generated a phpdoc template based on a function declaration](img/4548b3fccf6041df235ce5748810cd63.png)

NetBeans 的编辑器还有很多其他不错的功能，比如变量名重构。当您的文本光标位于变量名称上时，该变量的所有其他实例都以黄色突出显示。键入 Ctrl+R，您可以一次编辑该变量名的每个实例，以便在整个文件中立即重命名它:

![Screenshot showing the NetBeans code editor with boxes around all three occurrences of the $testvalue variable, with the cursor positioned to indicate the second of these occurrences is currently being edited](img/99318523dbf5c75847450871c963f446.png)

当然，您可以在任何变量、函数或类名上⌘-click(在 Windows 上按住 Ctrl 键单击),立即跳转到声明它的位置——即使它在项目的另一个文件中。

NetBeans 6.5 将所有这些 PHP 编辑功能与对服务器端调试的全面支持结合在一起，并带有 [XDebug](http://xdebug.org/) PHP 扩展。您可以在脚本中设置断点，并逐行检查代码，同时检查变量值。

当然，编写 PHP 应用不仅仅是 PHP 代码。您还必须编写 HTML、CSS 和 JavaScript 代码。当您这样做时，您会惊喜地发现 NetBeans 也内置了对这些语言的相同级别的支持！当一个文件将这些语言混合在一起时，NetBeans 可以无缝地处理这一切。

如果所有这些代码编辑功能还不够，NetBeans 6.5 的其他核心功能结合起来完善了体验。对版本控制系统的内置支持尤其受欢迎，包括 CVS、Subversion 和 Mercurial(还有其他免费插件，如 Git ),在代码旁边有彩色条来指示自上次提交以来已经更改的代码行。

NetBeans 不再是学习 Java 的玩具了。如今，它是一个强大的多语言开发环境，可以免费获取。如果您从事大型 PHP 项目，并且没有使用 NetBeans 这样的 IDE，您可能会惊讶于这样的工具可以节省您多少时间！

您可以了解 NetBeans 的 PHP 支持，并从 [NetBeans PHP 开发页面](http://www.netbeans.org/features/php/index.html)下载 IDE。如果你有兴趣关注正在为 NetBeans 的*下一个*版本开发的与 PHP 相关的新特性，一定要关注 NetBeans 的[PHP 博客](http://blogs.sun.com/netbeansphp/)。

## 分享这篇文章