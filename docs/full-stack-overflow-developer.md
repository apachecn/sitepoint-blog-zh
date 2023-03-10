# 全栈溢出开发人员

> 原文：<https://www.sitepoint.com/full-stack-overflow-developer/>

本文是微软网站开发系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

在一些谈话和采访中，我对我们市场中的一个现象感到遗憾，这个现象一直存在，但现在似乎很猖獗:全栈溢出开发者之一。[在 Stephen Hay 在 Twitter](https://twitter.com/stephenhay/status/621953048986976256) 上的提示下，我现在将谈谈这意味着什么。

![Cut-Copy-Paste](img/f95b1888025beaef5767f21f3fb24c52.png)

**全栈溢出开发者的工作几乎完全是从栈溢出中复制粘贴代码，而不是理解他们在做什么。他们不是研究一个话题，而是先去那里问一个问题，希望人们会给他们答案。**

在许多情况下，这是可行的。你可以通过粘贴你不理解的东西来获得惊人的成就，那些知道自己在做什么的人会把它们放在那里。

我在这里没有尝试堆栈溢出。这是一个令人难以置信的资源，很难创建一个这样的社区，而不是淹没在垃圾邮件和平庸(相信我，我是几个技术脸书组的管理员)。

这个问题我们已经有很长时间了。我向所有学习 PHP 的人挑战，不要简单地复制笔记中的代码示例。多年来，代码参考网站已经给了我们想要但不需要的答案。见鬼，甚至[马特的脚本存档](http://www.scriptarchive.com/)也可能是许多垃圾邮件的来源，因为人们在不知道它是做什么的情况下使用了 formmail.pl。

然而，我担心这种行为在今天有多猖獗。当然，这是可以理解的:

*   创造东西比研究如何创造东西更有趣。
*   使用能立即起作用的东西，即使你不知道它是如何起作用的，也比遭遇无法修复某样东西的挫败感要好得多。
*   你觉得自己欺骗了系统——捷径很有趣，让你觉得自己比所有花时间学习的傻瓜都聪明。
*   我们的工作是主流，对开发人员有大量的需求。我们被要求交付的速度大大提高了。人们希望结果更快，而不是更干净。

我们，作为一个社区，对滋生这种开发者负有部分责任:

*   当我们回答问题时，我们倾向于给出答案，而不是分析这个人真正需要什么。这是更多的工作，所以我们倾向于避免它。
*   发布“一个真正的解决方案”并在堆栈溢出上赢得一个线程感觉很棒——即使随着环境的变化，如果它不再是一个好主意，我们也不打算以后再回来。
*   因为给出解决方案而获得认可、因果报应和支持票，比因为提出正确问题以找到问题根源而获得认可、因果报应和支持票要容易得多。
*   人们很容易对一遍又一遍地得到相同的问题失去耐心，并且“只使用 jQuery”很容易被粘贴。

## 那又怎样？为什么人们发布产品更快更有效会有问题？

当然，你现在可以说我是一个脾气暴躁的老某某，告诉我学习软件基础知识的概念是一个过时的概念。当今产品的复杂性使得我们几乎不可能知道所有的事情，在其他非常成功的环境中，使用大量的包和库是理所当然的。好吧，尽管我们[似乎正在理解](http://www.nytimes.com/2015/07/09/business/dealbook/new-york-stock-exchange-suspends-trading.html?_r=0)T2 软件作为一个整体可能比我们愿意承认的更糟糕，这可能是原因之一。

全栈溢出开发有几个问题:

*   它认为最简单、最具技术细节的答案是最好的。这是危险的，会导致很多复制粘贴的例子，有很多问题在网上存在了很多年。
*   最容易被复制的答案被使用、投票和链接意味着解决问题的更好的解决方案不太可能成功取代它们。没有“更深入的挖掘”，所以即使是重要的修复也不会被发现。
*   任何专家社区都很可能对什么构成“专业环境”有很多假设。这意味着在那些社区中给出的答案很可能在一个伟大的、新的和复杂的开发者环境中工作，但是对我们的终端用户不一定有用。向一个项目添加另一个库或 npm 包或引导解决方案是非常容易的，但是它增加了 web 上已经满满的过时代码的垃圾。
*   它延续了我们对简洁的喜爱，而不是编写可理解的代码。最小的解决方案——无论多么不可读——总是赢得线程的解决方案。当人们在不理解的情况下复制和粘贴它们时，他们没问题。然而，对于调试和维护来说，这是最糟糕的解决方案。一个很好的例子就是[使用||作为默认参数](http://www.codereadability.com/javascript-default-parameters-with-or-operator/)。越短越好，只是工作量越少。
*   它贬低了我们的手艺。如果我们，作为交付工作的人，对它没有任何尊重，只是简单地把东西放在一起，并希望它们有效，那么如果我们的经理和同事不把我们视为专业人士，我们也不应该感到惊讶。

然而，最大的问题是，这对开发商本身不利。

## 在工作中找到自豪感是最大的回报

在网上找到一个解决方案并复制粘贴它是很容易的——太容易了。这里面没有努力，也不是你的作品——是别人的。你不会为你所取得的成就感到骄傲，你更有可能感到压力，因为你不想被发现是一个冒牌货，他使用别人的作品，并作为自己的作品出售。

许多开发人员讨厌重复。不要重复自己(DRY)在代码中是一个很好的概念，但是对于学习和工作来说却是一个可怕的想法。我们需要重复，来建立肌肉记忆。你重复一项任务越多，它就变得越容易，你的身体无需思考就能完成它。

当你开始开车时，你可能会坐在座位上，完全被所有的齿轮、杠杆、踏板和需要注意的事情所淹没。过了一会儿，你甚至不再想你在做什么，甚至从右舵驾驶切换到左舵驾驶都不是问题。失败并从中吸取教训比简单地使用某样东西更能让我们记忆深刻。我们付出了更多的努力，感觉更值得。

[丹·艾瑞里的 TED 演讲“是什么让我们对自己的工作感觉良好”](https://www.ted.com/talks/dan_ariely_what_makes_us_feel_good_about_our_work?language=en)在这个话题上有一些非常好的观点:

[https://www.youtube.com/embed/5aH2Ppjpcho?feature=oembed](https://www.youtube.com/embed/5aH2Ppjpcho?feature=oembed)

作为人类，我们渴望得到认可。如果我们不拥有我们所做的事情，我们就无法得到认可。你完全可以通过复制粘贴，一个接一个的解决方案，一个接一个的抽象来实现。但是，迟早，你会觉得你没有实现或创造任何东西。许多这样工作的开发人员很快就筋疲力尽，停止了开发，停止了关心。这是一个巨大的损失，因为你可能是提出下一个伟大想法的人，这个想法会改变我们很多人的生活。这是一个你不应该剥夺的选择。不要做满栈溢出开发者。你应该变得更好。

## JavaScript 的更多实践

这篇文章是微软技术倡导者的 web 开发系列的一部分，内容涉及实用的 JavaScript 学习、开源项目和互操作性最佳实践，包括[微软 Edge](http://blogs.windows.com/msedgedev/2015/05/06/a-break-from-the-past-part-2-saying-goodbye-to-activex-vbscript-attachevent/?WT.mc_id=13426-DEV-sitepoint-article50) 浏览器和新的 [EdgeHTML 渲染引擎](http://blogs.windows.com/msedgedev/2015/02/26/a-break-from-the-past-the-birth-of-microsofts-new-web-rendering-engine/?WT.mc_id=13426-DEV-sitepoint-article50)。

我们鼓励您使用 [dev.modern.IE](http://dev.modern.ie/tools/?utm_source=SitePoint&utm_medium=article50&utm_campaign=SitePoint) 上的免费工具跨浏览器和设备进行测试，包括 Windows 10 的默认浏览器 Microsoft Edge:

*   [扫描你的网站，寻找过时的库、布局问题和可访问性](http://dev.modern.ie/tools/staticscan/?utm_source=SitePoint&utm_medium=article50&utm_campaign=SitePoint)
*   [在 Mac、Linux 和 Windows 上使用虚拟机](http://dev.modern.ie/tools/vms/windows/?utm_source=SitePoint&utm_medium=article50&utm_campaign=SitePoint)
*   [在您自己的设备上远程测试 Microsoft Edge](https://remote.modern.ie/?utm_source=SitePoint&utm_medium=article50&utm_campaign=SitePoint)
*   [GitHub 编码实验室:跨浏览器测试和最佳实践](https://github.com/deltakosh/interoperable-web-development/?WT.mc_id=13426-DEV-sitepoint-article50)

来自我们的工程师和布道者的关于 Microsoft Edge 和 Web 平台的深入技术学习:

*   【2015 年微软 Edge 网络峰会(对新浏览器、新支持的网络平台标准以及来自 JavaScript 社区的演讲嘉宾有何期待)
*   哇，我可以在 Mac 电脑上测试 Edge & IE 浏览器& Linux！(来自雷伊·班戈)
*   [在不破坏网络的情况下推进 JavaScript】(来自 Christian Heilmann)](http://channel9.msdn.com/Events/WebPlatformSummit/2015/Advancing-JavaScript-without-breaking-the-web/?WT.mc_id=13426-DEV-sitepoint-article50)
*   使网络正常工作的边缘渲染引擎(Jacob Rossi)

http://dev.modern.ie/tools/vms/windows/?utm_source=SitePoint&utm_medium=article50&utm_campaign=SitePoint

*   [用 WebGL 释放 3D 渲染](https://channel9.msdn.com/Events/WebPlatformSummit/2015/Unleash-3D-rendering-with-WebGL-and-Microsoft-Edge/?WT.mc_id=13426-DEV-sitepoint-article50)(来自大卫·卡图赫，包括[伏龙。JS](http://vorlonjs.com/?WT.mc_id=13426-DEV-sitepoint-article50) 和 [babylonJS](http://babylonjs.com/?WT.mc_id=13426-DEV-sitepoint-article50) 项目)
*   [托管网络应用和网络平台创新](https://channel9.msdn.com/Events/WebPlatformSummit/2015/Hosted-web-apps-and-web-platform-innovations/?WT.mc_id=13426-DEV-sitepoint-article50)(来自律师奶爸和基里尔·赛克谢诺夫，包括[流形。JS](http://manifold.js.com/?WT.mc_id=13426-DEV-sitepoint-article50) 项目)

更多面向网络平台的免费跨平台工具和资源:

*   [适用于 Linux、MacOS 和 Windows 的 Visual Studio 代码](https://code.visualstudio.com/?WT.mc_id=13426-DEV-sitepoint-article50)
*   [用节点编码。JS](https://www.microsoftvirtualacademy.com/en-US/training-courses/building-apps-with-node-js-jump-start-8422/?WT.mc_id=13426-DEV-sitepoint-article50) 和[在 Azure 上免费试用](https://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=13426-DEV-sitepoint-article50)

## 分享这篇文章