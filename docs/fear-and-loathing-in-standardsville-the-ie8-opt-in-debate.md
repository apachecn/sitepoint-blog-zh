# 标准镇的恐惧和厌恶:IE8 选择加入辩论

> 原文：<https://www.sitepoint.com/fear-and-loathing-in-standardsville-the-ie8-opt-in-debate/>

正如 Matt Magain 上周在 SitePoint 上报道的那样，微软宣布 IE8 将引入一个新的“浏览器版本定位”系统，该系统将看到浏览器默认呈现与 IE7 相同的符合标准的页面，并将要求开发人员明确选择 IE8 的新的符合 T2 酸 2 的呈现引擎。

对于那些将美好世界的希望寄托在网络标准上的网络开发者来说，微软试图做出的改变是一件可怕的事情。

一个基本的新想法是，能够将页面“锁定”到特定的浏览器版本是一件好事，这需要一点时间来适应，但它正在逐渐被接受。毕竟，浏览器版本比新的 web 标准出现得更频繁，这使得当前浏览器中基于标准的呈现模式，如 [DOCTYPE 开关](https://reference.sitepoint.com/css/doctypesniffing)不够用。

当你安装一个新的浏览器版本时，看到你最喜欢的网站突然变得不稳定，这对于像你我这样的开发者来说可能是可以接受的，但对于最终用户来说，这永远是没有意义的，他们自然希望网络变得更好。浏览器版本定位让我们完全避开了这种不愉快，并使浏览器开发人员能够花更多的时间开发新功能，而不是花更少的时间权衡修复漏洞的利弊，许多网站都依赖于这些漏洞。如果你仍然不相信，我推荐阅读埃里克·迈尔的《从开关到目标 》。

## “现在生活扼杀了我的梦想”

但是，即使我们原则上接受浏览器版本定位，还有几十亿(几万亿？)的网页，其中没有一个当前指定了为其编写的浏览器版本。微软将会看到这些页面呈现为 IE7 当前在其余时间呈现的样子。

一些人认为这是让世界上懒惰的开发人员——他们只为当前版本的 IE 创建网站，而不做其他事情——编写劣质、不兼容的代码。其他人认为这是强迫开发者在他们所有的页面上贴上难看的“微软印记”,仅仅是为了获得对 IE 本应支持多年的标准的支持。

还有一些人只是不愿意放弃这样的观念，即网页是按照标准编写的，除非另有指示，否则按照这些标准或尽可能接近地呈现这些页面是浏览器的庄严职责。

杰里米·基思是最后一批人之一。在 [*破碎*](http://adactio.com/journal/1402/) 中，他呼吁开发者联合起来反对微软，努力说服他们在缺乏版本定位信息的情况下，默认行为应该是尽可能提供最符合标准的渲染。

作为渐进式改进的最坚定支持者之一，Keith 认为微软的默认提案扼杀了我们今天所创建的网站的光明未来。

令一些人震惊的是，[网络标准项目](http://webstandards.org/)的联合创始人[杰弗里·泽尔德曼](http://zeldman.com/)不同意。在他的文章 [*中，Zeldman 站出来为版本定位*](http://www.zeldman.com/2008/01/22/in-defense-of-version-targeting/) 辩护，并讲述了一个丑陋的事实:强迫渐进增强的实践者——开发者社区中的一小部分精英——在他们的旧网站上添加`<meta>`标签以受益于新的 IE 功能，这是为了让微软在更好的网络标准支持的道路上前进而付出的小小代价。

## "这是我们所知的世界末日(我感觉很好)"

从表面上看微软的提议，你可能会认为这对注重标准的开发人员来说是一个悲伤的日子，他们多年来一直在想象一个未来，在这个未来，按照 web 标准编写网站就足够了，它们可以在所有浏览器中按预期显示。

你可以说我疯了，但我相信还是有希望的，这可以从我们看到的其他浏览器供应商的一些回应中看出。在[*<META HTTP-EQUIV = " X-BALL-CHAIN ">*](http://weblogs.mozillazine.org/roc/archives/2008/01/post_2.html)和[*Slipping The BALL And CHAIN*](http://weblogs.mozillazine.org/roc/archives/2008/01/slipping_the_ba.html)中，Mozilla 开发者罗伯特·奥卡拉汉(Robert O'Callahan)讨论了从长远来看实际实施微软版本定位提案的挑战。他认为其他主流浏览器都不支持版本定位，原因有几个，包括:

1.  所有其他主要浏览器的标准支持现在都非常好，至少在最近的历史上，新浏览器版本中的错误修复没有对现有网站造成重大破坏。
2.  在确保安全性和互操作性的同时，为每个新版本的浏览器运送和维护越来越多的传统渲染引擎是不切实际的，甚至是完全不可能的。

事实是，如果这种逻辑可以应用于今天的 Firefox、Safari 和 Opera 等浏览器，那么它很可能在几年后应用于 Internet Explorer。去年年底，当我在 Web Directions South 采访克里斯·威尔逊时，我问了他这个问题:

> SP:“现在，随着 Internet Explorer […]最终采用这种开发者选择加入模式，随着时间的推移，浏览器是否会有五、六、七、十、十八种不同的呈现模式[…]，或者随着时间的推移，这种差异趋于零的趋势是否意味着有一天这种情况会消失？”
> 
> 克里斯·威尔逊:“我认为，我个人的希望当然是有一天这种情况会消失，浏览器版本之间的差异不是我们内部所说的‘突破性变化’[…]"

Chris 接着说，他担心即使是最好的标准规范也总有解释的余地，这将要求浏览器做出可能破坏现有页面的更改，但今天在标准方面所做的工作肯定比以前好得多。

这一点，再加上维护安全性、互操作性和向后兼容多种呈现模式的不断增加的维护负担，可能会让微软有理由怀疑当 IE10 推出时，版本目标是否仍有必要。

## IE8:意味着结束？

总结一下我对这个问题的想法，我认为微软增加针对 IE8 的浏览器版本的举动对于注重标准的开发者来说是难以接受的，但最终这是一个对目前阻碍当今网络发展的实际问题的可靠解决方案。如果仅仅是为了解决阻碍它完全接受网络标准的问题，微软应该受到表扬。它可以简单地把 Internet Explorer 再搁置六年！

让 HTML4/XHTML1 页面在默认情况下以 IE7 呈现模式为目标的决定，虽然可以理解会引起争议，当然也值得进一步讨论，但我相信它在当前现实世界的需求和符合标准的未来愿景之间取得了正确的平衡。

标准化组织的一线希望是，其他主要供应商认为在他们的浏览器中支持版本定位没什么用——当然不足以证明所涉及的巨大开发负担的合理性。这可能是 Internet Explorer 未来发展的一个迹象。

版本定位可能会被证明是让 Internet Explorer 停止在通往我们一直梦想的 web 标准乌托邦的道路上拖后腿的关键。一旦我们到达那里，微软很可能会发现自己有能力重新加入其他浏览器的行列，默认支持最新的标准，而不是要求支持。

## 分享这篇文章