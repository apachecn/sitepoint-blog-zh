# 15 个鲜为人知的 Unix 命令

> 原文：<https://www.sitepoint.com/15-little-known-unix-commands/>

每个开发者都需要对终端有一定的掌握。您不可能总是亲自出现在您要使用的计算机附近，在这种情况下，您需要远程登录计算机。虽然 GUI 应用程序确实可以实现这一点，但它们通常没有终端访问快(毕竟，这只是一些文本的交换！).

不管你是终端的初学者还是有经验的用户，我相信你喜欢学习新的技巧和窍门。在这篇文章中，我将介绍 15 个你以前可能没有听说过的 Unix 命令。

*注意:在这篇文章中，我将使用方括号来表示任何变量。当您实际运行该命令时，您应该用实际值替换它，去掉方括号。例如，我们的第一个例子，`man [command]`可以用作`man cd`或`man grep`。*

## 1.`man`

先说一个简单的。在文档中，`man`命令代表“手动”。如果您想了解任何 Unix 命令，可以运行以下命令:

```
man [command]
```

`man`最简单的用例是查看`man`命令本身的手册:

满满的

`man`本身并不一定是一个鲜为人知的命令，您可能会在任何 Unix 教程中找到它。然而，有一些特殊的用法我想强调一下，它们可能不会出现在普通的教程中。

如果您需要了解更多关于 ASCII 字符的信息，可以试试这个。

man ascii 码
[/shell]

![ASCII manual page](img/a4234e7a82736515ea34e5da0b046a4a.png)

曾经困惑过[微微-](http://en.wikipedia.org/wiki/Pico-) 和[毫微微-](http://en.wikipedia.org/wiki/Femto-) 哪个更小？尝试以下方法获取单位前缀信息:

[shell]
man 单位
[/shell]

![Man Units page](img/04af856d629ba17a5688e334507212bb.png)

还有很多这样的手册页，其中一些是[真的很搞笑](http://linux.about.com/od/funnymanpages/a/a_man_funindex.htm)也！(提示:试试`man xkill`。)我将让你去摆弄它。同时，让我们继续学习更多的命令。

## 2.`cd -`

如果你在一个目录下工作，不小心换到了另一个目录，有一个简单的方法可以回到原来的目录。只需运行以下命令即可返回上一个工作目录:

[shell]
CD-
[/shell]

![use of cd -](img/f7e517f825c173fd23fd70b082aa29dc.png)

## 3.`sudo !!`

![Use of sudo on XKCD](img/5ae717c795b799a800a282aff7f2f1ad.png)

[XKCD](http://xkcd.com/149/)的漫画强调了`sudo`命令在 Unix 系统中的重要性。`sudo`以管理员权限运行命令，前提是您的用户已添加到 sudo-ers 组。

假设您运行了一个没有前缀`sudo`的命令。如果您不愿意再次输入相同的命令，您可以运行下面的命令，以`sudo`的身份运行最后一个命令。

【贝壳】
须藤！！
[/shell]

![Running last command as sudo](img/90ab9e81de030ff012d0e5f5bf39e241.png)

## 4.`mtr`

`mtr`是一款功能强大的网络诊断工具。它结合了`traceroute`和`ping`命令的功能。

[shell]
mtr[主机名]
[/shell]

![use of mtr](img/6109b0056838d19b171937bc078d8437.png)

`mtr`检查主机(运行`mtr`的主机)和远程`[hostname]`之间的网络连接。这里是[在`mtr`上的详细帖子](https://library.linode.com/linux-tools/mtr)，展示了该命令的全部范围。

## 5.[空格]命令

经常使用终端的用户可能知道他们运行的每个命令都记录在一个文件`~/.bash_history`中。要在运行命令时跳过此日志记录步骤，只需在前面加一个空格，该命令将不会被记录:

[shell]
【空格】【命令】
[/shell]

## 6.`jot`

`jot`，顾名思义，生成一些文本——从数字到字符再到胡言乱语。如果要生成某个范围内的数字，请运行以下命令:

[shell]
jot[number _ of _ numbers][starting _ number]
[/shell]

如果您提供一个参数，它将生成从 1 到那个数字的数。

`-r`选项产生随机数。语法如下:

[shell]
jot-r[number of numbers][lower _ limit][upper _ limit]
[/shell]

![Generating random numbers](img/ca2eb091820b94f2e087405670377e11.png)

`-b`选项重复给定的单词。要查看选项列表，你可以运行`man jot`或查看[本教程](http://docstore.mik.ua/orelly/unix/upt/ch45_11.htm)。

## 7.`df`

在我们的列表中，`df`是一个相对简单的命令，代表“disk free”并显示磁盘中的可用空间量。

![use of df](img/243983e7bbeb6ec9edb2046ee9a6a401.png)

## 8.`pkill`

`pkill`或“进程终止”，终止一个正在运行的进程。当应用程序没有响应时，此命令特别有用。语法是:

[shell]
pkill[应用程序名称]
[/shell]

一个有趣/残酷的用例是当你有能力远程登录到别人正在使用的电脑上。检查他们正在运行什么应用程序，并为该应用程序运行`pkill`命令。当他们感到困惑时，试着表现得正常些，环顾四周，看看是谁搞的恶作剧。当然，您会希望确保它不是一个关键的应用程序或重要工作可能会丢失的东西！

## 9.`ddate`

[非基督教历法](http://en.wikipedia.org/wiki/Discordian_calendar)是另一种历法，以 1 YOLD 为公元前 1166 年。`ddate`显示不一致的日期。

![Discordian date](img/21b0082d81b8f158316b5fe581ef5a30.png)

## 10.`cal`

如果您只想查看普通的旧公历，只需键入`cal`即可获得当前月份的漂亮视图:

![Calendar](img/8c8a12ed5961ef2fd2d0587d96e43a91.png)

这只是默认视图。[`cal`](http://unixhelp.ed.ac.uk/CGI/man-cgi?cal)的手册页列出了各种选项，可以用不同的方式显示更多的月份。

## 11.`tac`

你可能听说过`cat`命令。它有一系列实用程序，包括创建、复制、合并和显示文本文件。`tac`命令做同样的事情，但是顺序相反！看一看。

![Use of tac](img/51545721e8d589a601995f527e9349f7.png)

## 12.`w`

`w`显示当前登录您系统的用户。它显示已登录用户的列表，以及更多信息，如系统正常运行时间和负载。

![who's logged in](img/e4671ca30fa91a7780d8e60bacf858b5.png)

## 13.`factor`

如果你想对一个数字进行因式分解，就不要再找了。只需运行以下程序即可获得一个数的质因数分解:

【外壳】
因子【数量】
[/外壳]

![prime factorization of 235](img/6008abfc6488f5b7b40c65115c82c440.png)

## 14.`yes`

回到一个有趣的命令，`yes`多次打印一个字符串。

【shell】
是【字符串】
[/shell]

如果您不提供字符串，它会递归地打印“y ”,直到您停止该命令。这个函数打印字符串的速度如此之快，以至于我无法在命令输出的同一个屏幕上抓取命令的截图！如果你正在做一些可疑的事情，有人碰巧经过，确保你运行`yes`来迷惑他们(前提是他们对 Shell 编程知之甚少)。

*注意:*如果你打算运行这个命令，你应该知道停止它的唯一方法是按`CTRL+C/CMD+C`(或者关闭终端)。

## 15.`nl`

`nl`给文本添加行号。最好是将其他命令的输出作为参数传递。另一个函数的输出使用管道(" | ")作为参数传递。让我们看两个例子:

![Use of nl - line numbers](img/4852bbbaa3bcf574e9e32c4b30839828.png)

## 知道其他的吗？

至此，我们到了这个您以前可能没有见过的 Unix 命令列表的末尾。你知道多少？你在日常工作中使用它们吗？请在下面的评论中告诉我们你知道多少——15/15 获胜！

## 分享这篇文章