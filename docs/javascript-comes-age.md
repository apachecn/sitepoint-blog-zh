# JavaScript 成熟了

> 原文：<https://www.sitepoint.com/javascript-comes-age/>

1995 年 12 月 4 日，网景通信公司和太阳微系统公司联合发布了一份新闻稿，宣布 JavaScript 的到来。这种语言得到了 28 家行业领先公司的支持，尽管它们中很少能存活到今天。

JavaScript 被大胆地描述为*“一种开放的、跨平台的对象脚本语言，用于在企业网络和互联网上创建和定制应用程序。”*声明透露:

> JavaScript 语言是对 Java 的补充，Java 是 Sun 业界领先的面向对象的跨平台编程语言。
> 
> JavaScript 类似于 Visual Basic，很少或没有编程经验的人可以使用它来快速构建复杂的应用程序。

哦真的吗？！

Brendan Eich 快速设计的语言在 1995 年 9 月 Netscape Navigator 2.0 的测试版中首次发布。该项目代号为 Mocha，最初命名为 LiveScript。JavaScript 的变化与 Netscape 在 Navigator web 浏览器中增加 Java applet 支持相一致:

> JavaScript 脚本被设计成在客户机和服务器上运行，修改 Java 对象的属性和行为。

这在 1995 年是一个聪明的营销策略，当时 Java 被宣布为所有 IT 灾难的解决方案。新闻声明令人困惑地暗示 JavaScript 与 Java 有某种联系——这一点至今仍在引起困惑。JavaScript 既不是 Java，也不是简化的脚本语言。引用杰里米·基思的话:

> Java 对于 JavaScript 就像 ham 对于仓鼠一样。

不到一年，微软就将 JScript 添加到了 Internet Explorer 中。这种语言在功能上完全相同——只是命名不同，以避免 Sun 的商标问题。

尽管推出了 Netscape Enterprise Server、classic ASP 和其他几个系统，JavaScript 仍在服务器端苦苦挣扎。它从未取得 PHP 的广泛成功。NET 和 Java 本身。Node.js 现在开始产生影响，但还为时过早。

然而，客户端 JavaScript 很快受到了欢迎。在 CSS 出现之前的日子里，简单的浏览器功能是很困难的，沙盒 Java 小程序不能与整个页面交互。不久，开发人员添加了简单的表单验证器、计算器和动画。

这种语言在 Internet Explorer 中与微软的 VBScript 有过一些早期的竞争，但是当 Netscape Navigator 仍然是顶级浏览器时，这种竞争从未有过机会。(奇怪的是，VBScript 是服务器端 ASP 中使用最多的语言，尽管 JScript 也可以使用。)

1996 年 11 月，Netscape 向 ECMA International 提交了 JavaScript，考虑将其作为行业标准，不久之后，ECMA-262 规范的第一版出现了。

然后是黑暗时代。

在 20 世纪 90 年代末的第一次浏览器大战中，JavaScript 的地位和作用受到了质疑。网景公司和微软公司实现了完全不同的浏览器模型，这种模型被松散地命名为“动态 HTML”或 DHTML。编写一个跨浏览器的应用程序几乎是不可能的，所以许多开发人员放弃了客户端开发，转而使用更简单的服务器环境。

公平地说，问题不是由 JavaScript 引起的，而是可笑的不兼容的浏览器 API。如果你认为供应商的关系现在被玷污了，网景和微软肯定是对立的。网景公司宣称操作系统已经死亡，所有的应用程序都将基于浏览器。虽然这可能具有前瞻性，但该技术无法与现实相匹配，并很快转向了 vaporware。作为报复，微软将 IE 免费与 Windows 捆绑销售，并切断了网景的收入来源。这个决定导致了昂贵的诉讼和罚款，尽管现在很难想象没有默认浏览器的操作系统。

IE 打赢了第一场浏览器大战。很少有用户注意到竞争对手的小冲突；IE 比网景好，网景在第 4 版时是一个糟糕的软件。单一的浏览器对 JavaScript 开发者来说应该是好的，但是 IE 的开发工具基本到了无法使用的地步。我仍然会做噩梦，梦见无数的*“Object not a instance of a Object at line 0”*错误。微软随后放弃了浏览器开发，JavaScript 的情况看起来很糟糕。

幸运的是，JavaScript 因多种因素而重生:

*   2002 年火狐(Phoenix)的出现。网景公司开源了他们的浏览器代码，并于 1998 年启动了 Mozilla 项目。旧的代码库被 Gecko 渲染引擎抛弃了，Gecko 渲染引擎最初出现在臃肿、有缺陷且缓慢的 Mozilla 套件中。Firefox 是一个实验性的独立浏览器，它很快受到开发人员的欢迎，因为它比老化的 IE6 更好，并提供了卓越的调试工具，最终导致了革命性的 Firebug。
*   2005 年 Ajax 的崛起。XMLHttpRequest 是微软在 1999 年推出的，但是很少有开发者开发它。杰西·詹姆斯·加勒特的文章[Ajax:Web 应用的新方法](http://www.adaptivepath.com/ideas/ajax-new-approach-web-applications/)给了我们一个很酷的时髦词，让我们重新考虑可能性。
*   谷歌地图(2005 年)和 Gmail (2004 年)。在 Ajax 这个术语被构思出来之前，Google 就已经在创建前沿的项目了。我们现在有了一个流行词和一些很好的例子来评估潜力。
*   客户端库的引入。Prototype、script.aculo.us、Mootools 和 jQuery 等项目消除了浏览器的不一致性，使开发变得更加容易。
*   对客户端开发的重新评估，包括来自 [SitePoint](/store/simply-javascript/) 的书籍和视频以及道格拉斯·克洛克福特 2007 年[的 JavaScript:精彩部分](http://www.yuiblog.com/blog/2007/06/08/video-crockford-goodstuff/)。

网络的指数级扩张已经使 JavaScript 成为有史以来最常用的计算机语言，事实上是一代程序员的第一语言。到目前为止，我们最受欢迎的书名是 [JavaScript:新手到忍者](https://www.sitepoint.com/premium/books/javascript-novice-to-ninja-2nd-edition)，它让学习者从一张白纸开始，全面教授这门语言。[学习用 JavaScript 编码](https://www.sitepoint.com/premium/books/learn-to-code-with-javascript/)从另一个角度看待事物，在教授编程基本概念的同时，使用 JavaScript 来传达这些知识。今天，JavaScript 是初级开发人员旅程的一部分，就像 20 年前的 Python 或 BASIC 一样。

JavaScript 可能是匆忙拼凑而成的，多年来一直享有与之相称的声誉。但是到了 2000 年中期，开发者开始意识到它可以用于复杂的网络应用程序，这些应用程序与桌面应用程序一样好，如果不是更好的话。今天，JavaScript 无处不在。该行业在该语言上进行了大量投资，它已经超越了浏览器，进入了服务器开发(Node.js)、操作系统开发(Windows 8、Chrome 和 Firefox OS)，甚至是嵌入式硬件控制器(T1)。

尽管如此，JavaScript 还是被误解了。名称和最初的 Netscape press 声明没有帮助，但许多开发人员继续痛斥这种语言。如果您来自基于 C 的环境，比如 Java、C++或 C#，JavaScript 在语法上看起来很熟悉— *但不是*。进入 JavaScript 思维模式并认识到原型继承背后的潜力和简单优雅需要时间。你用得越多，你就会越喜欢它。

所以 18 岁生日快乐，JavaScript。我们中很少有人期望你能达到这个里程碑——但我很高兴你做到了。

JavaScript 还能再活 18 年吗？有什么东西能取代一种在 web 和桌面开发中如此根深蒂固的语言吗？

## 分享这篇文章