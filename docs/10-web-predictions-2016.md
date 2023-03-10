# 2016 年 10 大网络预测

> 原文：<https://www.sitepoint.com/10-web-predictions-2016/>

新年快乐尽管我对 2015 年的预测很糟糕，我还是要再试一次。我的符文显示网络与天王星对齐…

## 1.一场大规模的公司黑客攻击将会发生

让我们不要再散布末日论了。一家大型跨国公司将在 2016 年遭到黑客攻击。这是必然的。2015 年的目标包括国税局、联邦调查局、伟易达、Ashley Madison、T-Mobile、史考特、CVS、OPM、加州大学洛杉矶分校健康中心、Carphone Warehouse、TalkTalk、Trump Hotels，甚至是密码管理器 LastPass。个人数据被盗，在最糟糕的情况下，密码和信用卡信息被泄露。

尽管媒体报道称*有复杂的攻击*，但这些系统中的许多都是通过简单的 SQL 注入或暴力尝试被访问的。许多系统泄露了未加密的数据或者安全性很差。

除非公司认真对待安全问题，否则黑客攻击将会继续。没有一个系统是 100%安全的，但是我怀疑这些系统中的许多是由开发新手在几年前实现的。我的建议是:在为时已晚之前，雇佣一些黑客或者支付一笔 bug 赏金。

## 2.静态网站将成为主流

静态站点生成器如[杰基尔](http://jekyllrb.com/)、[中间人](http://middlemanapp.com/)和[铁匠](http://www.metalsmith.io/)已经有好几年了。当访问者访问时，典型的内容管理系统从存储在数据库中的模板文件和内容构建页面。静态站点生成器只完成一次构建步骤，并以 HTML 文件的形式生成完整的站点。静态网站的好处:

*   它很快。普通的 CMSs 可以实现缓存，但是静态站点从一开始就是完全生成和缓存的。
*   它很结实。“未能建立数据库连接”错误再也不会出现，因为静态站点只需要一个基本的 web 服务器。服务器端代码和数据库仍可用于搜索和表单填写等功能，但处理保持在最低限度。
*   很安全。与 CMS 不同的是，破解普通的 HTML 文件很困难，而且尝试的动力也较小。如果有人设法进入，该网站可以被清除，并重新生成。
*   很简单。内容经理可以保留他们现有的 CMS，但增加构建和部署步骤。

静态站点生成器对于更新很少的内容站点很有意义。它们在技术作家中很受欢迎，但 2016 年将会有更多的机构和公司采用它们。

## 3.Chrome 的市场份额将保持稳定

Chrome 是目前世界上最受欢迎的网络浏览器，根据使用量计算，其市场份额为 [54%。我预计这个数字不会下降，但我怀疑 Chrome 在年底前不会达到 60%。原因包括:](https://www.sitepoint.com/browser-trends-december-2015-fight-firefoxs-future/)

1.  增加膨胀，内存使用和不稳定性。Chrome 可能是一个资源猪，用户开始注意到这一点。
2.  怀疑加剧。谷歌比其他供应商更密切地跟踪你的在线活动。
3.  竞争加剧。替代应用程序也同样有能力。

浏览器竞争比以往更加健康。这是一件好事:我们永远不想回到 IE6 式的单一文化。

## 4.维瓦尔第会吸引注意力

Vivaldi 是一款从 Opera 的灰烬中重生的新浏览器。当 Opera 切换到 Blink 时，许多用户失望地发现 12 版的功能很少被移植。几位前员工联手创建了维瓦尔第，让浏览器恢复了昔日的辉煌。它包括网站特定的标签颜色、快速命令、注释、电子邮件、面板和完全定制等功能。

Vivaldi 不太可能超过一位数的市场份额，但在 2016 年期间，人们的兴趣将会增加，特别是在以前的 Opera 和 power 用户中。

## 5.苹果必须解决 Safari 的缺点

虽然我不一定同意 Safari 是新的 IE 浏览器，但它已经明显落后于其他浏览器，包括微软的浏览器:

*   Safari 支持[更少的网络技术](https://html5test.com/results/desktop.html)。它所支持的那些往往被搁置在前缀后面。
*   尽管 iPhone 和 iPad 销售强劲，Safari 的使用量却在下降。
*   苹果不允许在 iOS 上使用其他浏览器(Chrome 和 Firefox 是 Safari 皮肤)。
*   不接触苹果硬件是不可能测试 Safari 的。
*   苹果似乎不愿意参与网络社区或透露他们的意图。

随着 HTML5 成为原生应用的可行替代品，Safari 容易受到攻击。当浏览器提供不合标准的体验时，最忠实的苹果粉丝会考虑放弃他们的 iPhone。

苹果有一些简单的选择。它可能允许 iOS 上的竞争浏览器或切换到 Blink 引擎。无所作为会威胁到他们设备的长期成功。

## 6.CSS 网格布局将是可用的

CSS 网格布局模块已经成为 W3C 的工作草案将近四年了。只有[一款浏览器提供一些支持](http://caniuse.com/#search=grid) : IE/Edge。Chrome、Opera 和 Firefox 都有实验性的实现，但 CSS 网格最终将在 2016 年实现。

网格比 Flexbox 有几个优势:

*   可以在内容加载之前定义网格，以提高呈现性能
*   项目可以定位在行和列中
*   项目可以以任何方式排列和排序
*   支持匹配的列高度和垂直居中，而无需求助于黑客！

网格和 Flexbox 可以共存。网格主要用于页面布局——页眉、页脚、边栏等。Flexbox 将用于容器内的布局，如表单和小部件。无论发生什么，令人费解的浮动时代即将结束。

## 7.设计移动化

我通常不预测设计趋势，但这似乎是不可避免的。移动优先将成为一种布局理念，而不仅仅是一种技术方法。尽管有响应式网页设计的可能性，相似的布局将在所有设备上使用。菜单可以通过汉堡图标来访问。自表格布局时代以来，侧边栏一直是网页设计的主要部分，将会变得多余。

网络的大部分将变得更简单、更时尚、更有性能意识。这是一件好事。谷歌和脸书即时通讯将被扔进科技垃圾压缩机。

## 8.平均页面重量将会下降

我每年都预测这一点，但是，如果更简单的设计趋势继续下去，这也许会发生？平均页面重量在三年内翻了一番，现在[超过了 2.2MB](https://www.sitepoint.com/average-page-weight-increased-another-16-2015/) 。它浪费时间、带宽和用户理智，但我们沉迷于无意义的高分辨率照片、未充分利用的社交媒体整合和侵入式广告。

至少，我希望肥胖率从每月 1.3%的增速放缓。

## 9.WebAssembly 将是一项利基技术

WebAssembly 是 2015 年最受炒作的公告之一。通过将源代码编译成更简单、处理速度更快、与 JavaScript 引擎兼容的字节码，并将其打包到一个紧凑的二进制文件中，它使得 JavaScript 交付更加高效。本质上，它减小了有效负载的大小，因此应用程序启动得更快(尽管它不一定运行得更快)。

C/C++到 WebAssembly 编译器应该会在 2016 年到来，这将有利于那些创建复杂游戏或大型基于浏览器的应用程序的人。我怀疑在我们大多数人考虑编译 JavaScript 资产之前，还需要几年时间。像[即将到来的 HTTP/2](https://www.sitepoint.com/http2-the-pros-the-cons-and-what-you-need-to-know/) 这样的技术可以毫不费力地改进 JavaScript 交付。

## 10.搜索引擎优化的死亡

RIP 搜索引擎优化，1996-2016。

在早期，SEO 意味着用恰当和不恰当的关键词的空洞重复来欺骗搜索引擎。谷歌用一种 PageRank 算法杀死了它，这种算法使用超链接来评定相关性。这导致了以链接特定网站为唯一目的的自动化网页农场的激增。谷歌进一步改进了他们的算法，这个过程就夭折了。

这种技术上的往复一直持续着，因为人们想要快速简单的方法在谷歌中排名第一。SEO 就像[蛇油](https://en.wikipedia.org/wiki/Snake_oil)一样被出售——一种神秘的技术，没有它就不能保证你的网站会失败。SEO 公司:你做得很好。你有 20 年可疑的技术实践和不应得的收入，但现在是继续前进的时候了。谷歌打败了你。客户对你的荒唐主张和恐吓战术很了解。

从一开始就有一个行之有效的解决方案:*写出让别人感兴趣的好内容*。就是这样。您可以通过代码优化和搜索引擎营销(SEM)技术(如广告和社交媒体活动)产生一些影响。然而，将 SEO 作为一项昂贵的独立服务的日子已经结束了。

抱歉，如果你在一个伟大的搜索引擎优化公司工作，它不会做出未经证实或误导的承诺。我肯定外面有一些——我只是从来没有遇到过一个。

* * *

你同意还是不同意我的任何预测？你有更好的预见吗？

祝 2016 年一切顺利。

## 分享这篇文章