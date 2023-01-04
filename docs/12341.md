# 开发 CodeBurner——探索性编程中的一个练习

> 原文：<https://www.sitepoint.com/developing-codeburner-an-exercise-in-exploratory-programming/>

CodeBurner 的新版本已经发布。这个版本不依赖于 Firebug——它是一个独立的扩展，并且在有限的时间内与一本免费的书捆绑在一起！[立即拿起你的。](https://getfirebug.com/)

今天[我们发布了](https://www.sitepoint.com/introducing-codeburner-the-sitepoint-reference-tool-for-firebug/) ~~火镜~~ [CodeBurner](https://getfirebug.com/) ，这是 [Firebug](https://getfirebug.com/) 的一个新附件，它扩展了 <abbr title="HyperText Markup Language">HTML</abbr> 和 <abbr title="Cascading Style Sheets">CSS</abbr> 的参考资料。

事实证明，开发 Firebug 与开发任何其他 Firefox 扩展并没有太大的不同。我非常感谢[简·奥德瓦科](https://www.softwareishard.com/blog/)发表的关于[扩展 Firebug](https://www.softwareishard.com/blog/firebug-tutorial/extending-firebug-hello-world-part-i/) 的系列文章，从中我获得了让它工作所需的基础知识——如何挂钩 Firebug 的脚本命名空间，如何向其用户界面添加控件，以及如何利用其可用的脚本对象。

但是除了像这样的一些基本文章之外，Firebug 的 <abbr title="Application Programming Interface">API</abbr> 基本上是没有文档记录的，所以我不得不做一些你可能称之为“探索性编程”的事情——在源代码中摸索，寻找看起来可能做我需要的代码。例如， ~~FireScope~~ CodeBurner 需要能够提取应用于给定元素的所有 CSS 属性，以便它可以在参考中搜索关于每个元素的信息。我们已经知道 Firebug 可以做到这一点，因为它就在样式面板中，但是我们如何挂钩到正确的进程，以便在我们需要的时间和地点获得信息呢？幸运的是，由于 Firebug 是开源的，所以它能做的任何事情最终都会被发现，即使它确实需要一些搜索！

我用来寻找这种功能的过程是一种逆向工程，你从最终结果开始，然后逆向工作。例如，我开始寻找处理用户界面中显示 <abbr title="Cascading Style Sheets">CSS</abbr> 信息的部分的代码，我可以在 Firebug 的 <abbr title="eXtensible User-interface Language">XUL</abbr> 文件中找到它的位置( <abbr title="eXtensible User-interface Language">XUL</abbr> 是 Firefox 用来定义其 <abbr title="User Interface">UI</abbr> 组件的可扩展用户界面语言)。一旦我找到了那个代码，并且发现它包含在一个方法中，我就可以进一步回溯，寻找那个方法被引用的地方。如此等等，通过方法调用和引用，直到最终我找到从 <abbr title="Cascading Style Sheets Document Object Model">CSS DOM</abbr> 中提取信息的代码。

虽然复杂，有时令人困惑，但这种反向发现的过程允许我处理最复杂和抽象的代码，这使得构建这样的工具成为可能，**非常依赖于来自未记录的代码库**的功能。

这并不是对每个人都有吸引力的事情，因为这需要大量的时间和一定的耐心，但我发现当一切顺利时，这是令人难以置信的满足感——这是让我享受这个过程的原因。

你呢？你喜欢这种编程任务吗，还是这个想法让你觉得很冷？

## 分享这篇文章