# 独立 IE7–解决方案

> 原文：<https://www.sitepoint.com/standalone-ie7-the-fix/>

如果你看到了上周五的设计视图，你会看到关于将 IE7 作为一个独立的应用程序运行的说明——替代替换你的忠实(？)旧 IE6 安装。上周末，许多人报告说，尽管他们的 IE7 运行良好，但他们的 IE6 安装开始表现奇怪，最常见的是在被传递一个 URL 时将责任转移给 Firefox。

好消息是微软 ASP.net 博客的 Jon Galloway 也遇到了这个问题，并对其进行了报道。

> 问题是 IE7 写入了一个注册表项，导致 IE6 转换到一个同样未记录的“邪恶”模式，在运行 IE7 之后和运行 IE6 以及 12 月 5 日的累积安全更新之前，有必要删除这个注册表项。

令人欣慰的是，修复相当容易——一个小的批处理脚本删除了 IE7 生成的讨厌的 reg 键，并防止它再次出现。乔恩在这里对流程做了[详细的解释。简短的版本是:](http://weblogs.asp.net/jgalloway/archive/2005/12/28/434132.aspx)

*   右键单击[该链接](https://www.sitepoint.com/examples/IE7-fix/IE7.txt)并选择“将链接另存为…”
*   将文件保存在 IE7 文件夹中，命名为“IE7”。 ***蝙蝠***’。
*   从现在开始，每当你想启动 IE7 时，运行这个文件(' IE7.bat ')。

你会看到批处理文件启动它自己的小命令窗口——让它在 IE7 运行时保持打开(最小化),否则它会再次使用它的老把戏。IE7 一关闭就可以杀了。

谢谢乔恩。

2006 年 9 月:从 IE7 beta 3 开始，这个解决方案不再有效。目前，我所知道的唯一工作方法是 Tredosoft 的可下载应用程序——从这里获得。http://tredosoft.com/IE7_standalone

## 分享这篇文章