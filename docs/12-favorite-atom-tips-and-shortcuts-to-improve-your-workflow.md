# 改善工作流程的 12 个最受欢迎的 Atom 技巧和快捷方式

> 原文：<https://www.sitepoint.com/12-favorite-atom-tips-and-shortcuts-to-improve-your-workflow/>

![Atom logo](img/c69e9b66d224996424c1cafb1fafc9cf.png)

Atom 是 GitHub 团队发布和维护的代码编辑器。2014 年发布的“Sublime Text killer”拥有超过[110 万的月用户](http://blog.atom.io/2016/05/06/two-years-open-source.html)，这并不奇怪:IDE 易于扩展、定制和黑客攻击，已经成为许多开发人员的最爱。

尽管它被广泛使用，但我经常看到有能力的开发人员绕远路去做事情，或者不知道它的真正潜力。这篇文章探索了一些改进 Atom 工作流的技巧。我希望，当你完成的时候，你已经学会了至少一个你离不开的新技巧。

注意:虽然这篇文章是为 Atom 用户写的，但是很多技巧和快捷方式也适用于 Sublime 文本。

## 技巧

第一站是一些通用的 Atom 技巧。有您可以启用的选项、IDE 具有的功能以及您从来不知道存在的菜单设置。浏览所有的菜单选项是值得的——因为你可能会发现一些你从来不知道的东西！

### 多个光标

Atom 最令人印象深刻的特性之一是它的多光标支持。这使得你可以一次在文档的任意位置、多行输入许多内容。只需按住 **cmd** (Mac)或 **ctrl** (Windows/Linux)，在每一个你想打字的地方点击。还有其他获得多个光标的方法——但是我们稍后会用[键盘](https://www.sitepoint.com/best-mechanical-keyboards/)快捷键来介绍它们。

![multiple cursors](img/9769c50be520888de28be46cc321b9e1.png)

### 自动缩进

我的一个烦恼是当我从某个地方复制代码时，到处都是缩进。幸运的是，Atom 为您提供了保护。选择代码并前往**编辑>行>自动缩进**。这应该会复制文件上的当前缩进来纠正您的代码。

![auto indent](img/cffc9c39bc24b299f134c9b136017a43.png)

为了加快速度，我创建了一个自定义的键盘快捷键，使我可以在任何时候使用 **Ctrl + Cmd + ]** 。(在 Windows 上，我会选择**Ctrl+}**——也就是**Ctrl+Shift+]**——因为 **Ctrl + Alt + ]** 已经被占用了。)

要自己做同样的事情，请转到 **Atom >键盘映射** (Mac)或**文件>设置>键盘绑定>键盘映射** (Windows/Linux)，并为 Mac 粘贴以下内容:

```
'atom-text-editor':
    'ctrl-cmd-]': 'editor:auto-indent' 
```

或者这对于 Windows/Linux:

```
'atom-text-editor':
    'ctrl-}': 'editor:auto-indent' 
```

### 显示不可见内容

为了确保文档和所有行都使用正确的缩进，我在编辑器中启用了**不可见元素**。这显示了空格缩进的`···`，制表符的`»`和新行字符的`¬`。这有助于您准确地看到混合标签和充满空格的空新闻的位置。

虽然一开始它会让你的屏幕感觉“很忙”，但你很快就会习惯，现在我发现它非常有价值。

为此，请前往 **Atom** (Mac)或**文件** (Windows/Linux) **>首选项>(向下滚动)显示不可见元素**。

![preference settings](img/7515153fb0a56274f3798487be37a7be.png)

### 软包装

我讨厌必须左右滚动*和上下滚动*，所以为了确保只有一个方向，我在 Atom 中启用了**软包裹**。这可以确保没有东西离开屏幕边缘，而是环绕在周围。如果它已经换行，它会将其缩进到与前一行相同的级别，并用一个`·`替换装订线中的行号。

要启用该选项，需要从 **Atom** (Mac)或**File**(Windows/Linux)**>Preferences>(向下滚动)Soft Wrap** 中的 **Show invisibles** 选项向下几个复选框。

### 字符大小写转换

有时你会看到大小写错误的文本。您可能需要全部大写，或者您可能需要将一个 SHOUTY 句子转换为全部小写。位于**编辑>文本**菜单中的是一些巧妙的文本操作工具，包括**大写**和**小写**功能。

![character case conversion options](img/474426ed534481ca9879a19a953553fa.png)

## 包装

软件包是选择 Atom 的一个令人信服的理由。安装和更改任何东西的能力是这个代码编辑器如此伟大的原因。我不会一口气说出你*必须*安装的最好的插件——有很多帖子[已经这么做了](https://www.sitepoint.com/10-essential-atom-add-ons/)。

相反，我会告诉你安装你遇到的每一个，然后卸载你不喜欢的(或者给你的启动时间增加太多宝贵的时间)。如果你进入**设置>包**并点击一个已安装的扩展，它会告诉你它增加了多少毫秒的启动时间。

这是我每天依赖的几个软件包，我没有在许多其他博客帖子中看到它们:

*   [项目经理](https://atom.io/packages/project-manager)
*   [Git 加](https://atom.io/packages/git-plus)
*   小地图
*   [颜料](https://atom.io/packages/pigments)。

## 快捷键

我喜欢键盘快捷键。对于我使用的每一个程序，我都忍不住去尝试和学习它们。(有些事情我是通过 Photoshop 中的快捷方式来做的，而我根本不知道如何用其他方式来做！)

很自然地，你会忘记那些你不用的，而那些你用的会一直铭刻在你的脑海里。我下面列出的那些我倾向于每小时至少使用一次。他们是救生员。

### 双线电路

```
Cmd + Shift + D (Mac)
Ctrl + Shift + D (Windows/Linux)
```

如果捷径获得奖牌，这将是赢得金牌的一个。我经常用这个。它允许您将光标放在该行的任何位置并复制它。

![duplicating a line](img/f3558f1fbdefbc41e05d698031b5ad51.png)

这对复制 CSS 选择器、渐变或表格单元格非常有帮助。当然，您也可以一次复制多行，方法是高亮显示它们或使用多个光标:

![duplicate multiple lines](img/79fbeeb710aa8e2a8ac84719cb24502c.png)

### 向上或向下移动当前行

```
Cmd + Ctrl + Up (or Down) Arrow (Mac)
Ctrl + Up (or Down) Arrow (Windows/Linux) 
```

这个快捷键与上面的复制行结合起来非常好。无论光标在哪里，该快捷键都会将当前行移动到其周围行的上方或下方。

![moving lines up and down](img/ef258d58f17bae5e09c9f305bfedc5c0.png)

如果您选择了多行，当您移入和移出标签和括号时，它将移动整个块(并自动缩进)。

### 选择下一个匹配字符

```
Cmd + D (Mac)
Ctrl + D (Windows/Linux) 
```

此命令允许您选择下一个与突出显示的单词或字符匹配的单词或字符。然后，您可以(使用自动生成的多个光标)删除、编辑或更新突出显示的值。

![matching characters](img/f834b1c9ec0d957b947c5cfd1e19c269.png)

如果您只想更新几个值或属性，而不求助于查找和替换，这将非常有用。

### 取消选择下一个匹配字符

```
Cmd + U (Mac)
Ctrl + U (Windows/Linux) 
```

如果您正在选择下一个匹配字符的*，有时您会走得太远。该快捷键将*以相反的顺序取消选择*最近选择的字符。*

![unselect next matching character](img/7ee196cd52440f3b33494d07c8e45ad7.png)

### 选择所有匹配的字符

```
Cmd + Ctrl + G (Mac)
Alt + F3 (Windows/Linux) 
```

有时候你想批量编辑*文档中所有*匹配的字符，而不是对每个字符都按下 **Cmd/Ctrl + D** 。此快捷方式选择与您所选内容相匹配的所有内容。(警告:大量选择会严重降低 Atom 的速度！)

![select all matching characters](img/1c37d888b566dda36813872ed675c75e.png)

### 切换注释(开和关)

```
Cmd + / (Mac)
Ctrl + / (Windows/Linux) 
```

有些情况下，您可能希望注释掉一行或一组行。无论您使用哪种语言，此快捷方式都会用正确的注释语法适当地注释掉当前行(或选择的行)。你再也不需要记住你的`<!--`或`/*`了。

![toggle comments](img/6f116daf8480bdb76cfc5573e8ed884e.png)

## 结论

Atom 很神奇，我每天都在发现关于它的新东西。然而，这些是我一贯使用的快捷方式和插件。你有什么我没提到的最爱吗？让我们知道！

## 分享这篇文章