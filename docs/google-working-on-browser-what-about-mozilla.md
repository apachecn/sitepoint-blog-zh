# 谷歌开发浏览器:Mozilla 怎么样？

> 原文：<https://www.sitepoint.com/google-working-on-browser-what-about-mozilla/>

![](img/83bb28eb2d7db8e14fdb8af601970e41.png "google-chrome") * **更新** : Windows 用户可以在这里下载浏览器[。](http://www.google.com/chrome/)*

关注谷歌的博客 Philipp Lenssen [今天收到了谷歌寄来的一个包裹](http://blogoscoped.com/archive/2008-09-01-n47.html),里面有一本 38 页的漫画书，详细介绍了他们一个传闻已久的副业项目:谷歌网络浏览器。伦森把整个《T2》漫画放到了网上，这是一个有趣的阅读。但是谷歌为什么要规划自己的浏览器呢？

首先，一些快速的事实(基于漫画):

*   Chrome 是一款基于 Webkit 的完全开源的浏览器，与苹果的 Safari 和 iPhone 浏览器的底层架构相同。
*   它将是多线程的，每个进程都有自己的内存和全局数据结构的副本。
*   因为每个选项卡都将使用自己的进程运行，所以理论上，崩溃、内存泄漏或 JavaScript 变节不会导致整个浏览器瘫痪——只是那个选项卡。
*   它将有一个类似操作系统的进程管理器，这样你就可以一个标签一个标签地查看哪些网络应用占用了你的 CPU 和带宽。
*   谷歌声称，浏览器如何处理 JavaScript 的漂亮的新实现(像内置的称为 V8 的 JavaScript 虚拟机)显著提高了网络应用的速度。
*   [Gears](http://gears.google.com/) 是预装的——这意味着[对网络标准的承诺](http://www.readwriteweb.com/archives/google_gears_turns_one.php)。
*   标签位于地址栏的上方，而不是下方(像其他现代浏览器一样)，这意味着更加强调你正在浏览的页面。每个选项卡都有自己的控件(前进、后退、地址栏等)。)
*   地址栏内置了自动完成和关键字建议。
*   当你打开一个新标签页时，一个“新标签页”会显示你最常访问的 9 个站点，搜索，最近加书签的站点，以及最近关闭的标签页。
*   Chrome 内置了隐私模式(想想 IE 8 现在臭名昭著的 InPrivate 模式，网络毫不客气地称之为“色情模式”)。
*   弹出窗口被限制在它们原来所在的标签中，除非你想把它们拖到它们自己的窗口中。
*   网络应用可以在他们自己的无工具栏浏览器窗口中启动——还有 Gears 支持，听起来很像 Mozilla Prism 的领地。
*   Chrome 将内置基于不断更新的不良网站列表的网络钓鱼防护。

那么谷歌为什么要打造 Chrome 呢？根据漫画的说法，原因是他们想要一个完全基于 web 应用程序(就像他们创建的那样)构建的浏览器。谷歌表示，他们想要一款“更稳定、更快、更安全、用户界面简洁高效”的浏览器。显然，谷歌几乎从一开始就支持的来自 Mozilla 的开源浏览器项目 Firefox 并不是这些东西中的任何一个。

据传闻，谷歌已经开发浏览器至少 4 年了，所以他们真的在开发一款浏览器并不奇怪。但是为什么要从零开始开发他们自己的产品，而不是投入更多的精力来支持 Mozilla 呢？考虑到上周谷歌[续签了对 Mozilla](http://www.betanews.com/article/Google_extends_its_investment_in_Mozilla_restores_MPL_license/1219963665) 的投资至 2011 年，这是一个特别有趣的问题。

谷歌表示，Chrome 旨在通过构建“现代网络应用的坚实基础”，帮助“所有浏览器变得更加强大”，并希望其他浏览器开发者——包括 Webkit 和 Mozilla 开源项目——将借鉴他们在那里提出的一些想法。

这令人钦佩，但这也很符合谷歌想要控制一切的趋势。是的，这是一个开源项目，但它是按照谷歌的形象制作的(就像知识单元是谷歌形象中的维基百科，或者像谷歌朋友连接是[以谷歌的方式完成的开放标准](http://www.readwriteweb.com/archives/google_friend_connect_manages.php))。正如[谷歌操作系统观察到的](http://googlesystem.blogspot.com/2008/09/google-os-is-actually-browser-google.html)，Chrome 实际上是传闻已久的“谷歌操作系统”如果像许多人认为的那样，网络是未来的操作系统，那么一个专门为运行网络应用程序而设计的浏览器将非常重要，并有可能比其他玩家更有优势。

也就是说，谷歌在漫画中提出的一些想法非常有趣，所以我们迫不及待地想尝试一下。你呢？

**更新:** Blogoscoped 有[截图](http://www.readwriteweb.com/archives/mozilla_prism.php)，Google 有[有官方公告](http://googleblog.blogspot.com/2008/09/fresh-take-on-browser.html)。有传言说 Windows beta 将于明天发布。

## 分享这篇文章