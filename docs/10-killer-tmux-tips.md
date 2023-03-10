# 10 个黑仔 Tmux 提示

> 原文：<https://www.sitepoint.com/10-killer-tmux-tips/>

![tmux3](img/be1bfba7bfa1f6215ccb87751cab38ff.png)

在本文中，我为您带来了十个我最喜欢的 tmux 技巧。对于那些不熟悉 tmux 的人来说，SitePoint Ruby 编辑器 [Glenn Goodrich](https://www.sitepoint.com/tmux-a-simple-start/) 已经写了一篇很好的基础概述。

今天，我将向您展示我日常使用 tmux 的技巧和诀窍，以及 tmux 如何改进 Ruby 开发的工作流程。如果你是一个`vim`用户，你将会得到一份款待。

## 技巧#1:始终打开 tmux 会话

养成一个新习惯总是一场艰苦的战斗。就像试图通过禁用箭头键来强迫自己使用`vim`中的`hjkl`键一样，我需要找到一种方法来强迫自己一直使用 tmux。当您正在编写代码，却忘记了应该首先使用 tmux-ed 时，这非常有用。

[https://www.youtube.com/embed/FbKpN7c2aX8](https://www.youtube.com/embed/FbKpN7c2aX8)

上述示例适用于 Mac OSX 上的 iTerm2。搜索 Linux 的等价物应该不难。主要思想是当终端程序启动时总是运行`tmux attach -t base || tmux new -s base`。

上面的行试图连接到以前创建的名为`base`的会话，或者创建一个新的会话(也称为`base`),如果它不存在的话。你可以把`base`改名为任何你想要的名字，它不是一个特殊的关键字。有了这个咒语，无论何时你启动终端，你都会自动连接到你之前的 tmux 会话。

## 技巧#2:多窗格同步

[https://www.youtube.com/embed/-K9whwVkdRU](https://www.youtube.com/embed/-K9whwVkdRU)

上面的视频显示了四个装载了四种不同语言的回复的窗格。按顺时针方向，我们有 Haskell、Ruby、Elixir 和 Python。`setw synchronise-panes`给我们的是，它*同时*从一个窗格向其余窗格发送键盘输入。(在这种情况下，我们还可以看到，由于彩色输出，REPL 的长生不老药要优越得多。)

显然，这个演示仅仅是为了给开发人员留下深刻印象的一个聚会把戏，仅此而已。一个很好的用例是必须在多台机器上执行重复的操作。例如，想象有一个 tmux 会话连接到四台远程主机，并且必须在所有主机上执行完全相同的命令。

## 技巧 3:刷新 tmux

我倾向于用`Command + L`清终端。不幸的是，在 tmux 中，这只会搞乱整个会话。举例来说，上面显示了我典型的 tmux 体验:我正在愉快地编程，不知何故，我的胖手指碰到了`Command+k`,我的整个 tmux 会话变得一团糟，这让我很生气:

[https://www.youtube.com/embed/X3_k7_m0j7M](https://www.youtube.com/embed/X3_k7_m0j7M)

幸运的是，简单的解决方法是绑定`prefix-r`来重新加载 tmux 配置文件，一切又都好了。在`~/.tmux.conf`中，添加以下条目:

 `bind-key r source-file ~/.tmux.conf` 

这告诉 tmux 再次获取配置，实质上是重新加载 tmux。

## 技巧 tmux 插件管理器

这是我最近的发现。我没有意识到有人创造了一个 tmux [插件管理器](https://github.com/tmux-plugins/tpm)。我不会用安装的细节来烦你，因为它非常简单。

一旦你安装了插件，只需要选择你想要的插件，然后将条目粘贴到`tmux.conf`中。

如果用 Vundle 或者病原体做`vim`，过程是一样的。你可以通过提供 GitHub 用户名和 repo 来声明你想要的插件，保存`tmux.conf`，然后点击`prefix + I`来安装插件:

[https://www.youtube.com/embed/Cg-1sZs-rFs](https://www.youtube.com/embed/Cg-1sZs-rFs)

接下来的两个技巧涵盖了两个有趣的插件，它们可以用 tmux 插件管理器安装。

## 技巧 5:复活 tmux

[tmux-复活](https://github.com/tmux-plugins/tmux-resurrect)是一个跨系统重启保持 tmux 环境的工具。这为什么有用？当您重新启动 tmux 时，您的整个 tmux 会话都将消失。解决了这个问题。`tmux-resurrect`的另一个非常好的特性是能够恢复`vim`会话！

在接下来的视频中，我在左侧窗格中有一个`vim`会话，右侧窗格中有`rails console`和`rails server`。

为了让`tmux-resurrect`持续一个`vim`时段，安装蒂姆·波普的[vim-执念](https://github.com/tpope/vim-obsession)。`vim-obssesion`使录制`vim`会话变得更加容易。我没怎么玩过，但让我给你展示一下整个过程:

[https://www.youtube.com/embed/dgeyk-L8Esk](https://www.youtube.com/embed/dgeyk-L8Esk)

首先，我们要告诉`vim-obsession`跟踪会话。我还在另一个窗口中运行了`htop`。

要保存整个 tmux 会话，请键入`prefix + Control + s`。然后，如果有什么东西杀死了 tmux 服务器，这就是当你的电池耗尽或者有人被电线绊倒而断电时会发生的情况。有了`tmux-resurrect`，丢失 tmux 会话不是问题。为了恢复会话，您需要再次运行 tmux，这一次，点击`prefix + Control + r`。

然而，我已经可以在这里有人在想，“手动做`prefix + Control + s + r`是不是很麻烦！谁有时间记住所有这些？”输入提示 6。

## Tip #6: `tmux-continuum`

[tmux-continuum](https://github.com/tmux-plugins/tmux-continuum) 定期持续保存你的 tmux 环境，并在 tmux 启动时自动恢复。

## 技巧 7:缩放 tmux

缩放是在 tmux 1.8 中引入的。当您想要查看测试失败或检查日志时，tmux 缩放非常有用。要放大一个窗格，点击`prefix + z`，并使用相同的组合缩小。

## 技巧 8:在`vim`分割和 tmux 窗格之间无缝导航

有时很难确定你是在一个`vim`分割中还是在一个 tmux 面板中。在两者之间导航也很麻烦。直到 vim-tmux-navigator 的出现。

这里，我们有一个包含三个窗格的 tmux 会话。左半部分由两个`vim`分割组成，右手边包含两个 tmux 窗格。

[https://www.youtube.com/embed/vsBIjXPF71I](https://www.youtube.com/embed/vsBIjXPF71I)

有了`vim-tmux-navigator`，我可以使用`Control` + `hjkl`在`vim`分割和 tmux 窗格中导航:

## 技巧 9: `vim` + tmux + Ruby 规格

`vim`和 tmux 组成了一个*非常好的组合来运行 Ruby 测试。为此，您需要安装三个`vim`插件:*

*   [vim-rails](https://github.com/tpope/vim-rails)
*   [vimux](https://github.com/benmills/vimux)
*   [vim-turbux](https://github.com/jgdavey/vim-turbux)

一旦你安装了这些插件，让我们看看它们如何使我们的工作流程更好:

[https://www.youtube.com/embed/DteBSo_ZfsA](https://www.youtube.com/embed/DteBSo_ZfsA)

我们有一个`Micropost`模型的规格。我已经配置了 `to runs *all* the specs found in this file. However, when I hit` `, it runs only the specs under the cursor. Extremely handy!`

## Tip #10: `tmuxinator`

除了有一个令人敬畏的名字， [tmuxinator](https://github.com/tmuxinator/tmuxinator) 是一个优秀的 Ruby gem，它可以帮助您轻松地创建和管理 tmux 会话。当不同的项目需要不同的窗格/窗口布局时,`tmuxinator`特别有用。

## 感谢阅读！

我希望你喜欢这篇文章，并学到一些新东西。你还有其他小技巧可以分享吗？在下面的评论里传播一些 tmux 的爱吧！

## 资源

*   tmux:高效的无鼠标开发
*   [Vim 和 Tmux 带来的令人印象深刻的 Ruby 生产力 Chris Hunt 的古城 Ruby 2013](https://www.youtube.com/watch?v=9jzWDr24UHQ)

## 分享这篇文章