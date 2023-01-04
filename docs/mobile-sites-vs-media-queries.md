# 移动网站与媒体查询

> 原文：<https://www.sitepoint.com/mobile-sites-vs-media-queries/>

在*科技时报*的第 324 期[中，](https://www.sitepoint.com/responsive-web-design-with-html5-and-the-less-framework-3/) Louis 谈到了响应式网页设计，这是一种使用 CSS3 媒体查询让你为各种设备提供不同布局的技术。响应式布局技术已经获得了 web 开发人员的大量关注，因为它承诺提供我们一段时间以来一直希望实现的东西——只构建我们内容的一个版本，并让它在遇到它的任何设备上都能完美地工作。我们中的很多人都有迫不及待地想加入移动网络潮流的客户。

我们能不能用一个新的手机友好型包装来提供整个网站？我们当然可以。*我们应该*吗？嗯……这不仅仅是简单的是或不是。

## 移动用户如何浏览网页？

作为极客，我们喜欢了解不同移动和手持平台的所有有趣的细节——浏览器的渲染引擎、原生分辨率、插件支持、是否允许地理定位等等。移动设备变得越来越好——我们现在享受的不是小巧、缓慢的翻盖手机，而是大而多彩的屏幕和有趣、直观的触摸屏界面。iPhone 和 iPad 获得了媒体和关注的最大份额，尤其是在网络开发人员中，但是还有大量的移动设备，它们都需要被考虑。

然而，每个移动设备都有一个共同点。看看这些人，看看你是否能明白我在想什么:

[![Iced tea at Georgia's](img/f45a12b01f0ce33fed7d0160f5d94664.png)](https://www.flickr.com/photos/yourdon/2675323741/ "Iced tea at Georgia's by Ed Yourdon, on Flickr")

[![You think you're so smart? Let's see you try to IM with gloves, while you're holding a cup of coffee, and trying to squint through these funny-looking glasses](img/04c19feba64ccf7917866af908298ceb.png)](https://www.flickr.com/photos/yourdon/4236402727/ "You think you're so smart? Let's see you try to IM with gloves, while you're holding a cup of coffee, and trying to squint through these funny-looking glasses by Ed Yourdon, on Flickr")

[![Snow and Things 19](img/8d2a03ce779a8544e7dda811c1c89757.png)](https://www.flickr.com/photos/morganeverett/5341922176/ "Snow and Things 19 by Morgan.Everett, on Flickr")

[![Mobiles on Monday Morning](img/722337ffc386df5304b6e29bf52ab86e.png)](https://www.flickr.com/photos/adders/4953024585/ "Mobiles on Monday Morning by Adam Tinworth, on Flickr")

[![Pavement Texting](img/2afcd802355ad0e9b61a8738993ee00d.png)](https://www.flickr.com/photos/kamshots/523413820/ "Pavement Texting by kamshots, on Flickr")

没错——这是*使用它的上下文*。这些人都没有坐在办公桌前；他们都出去走动了。这听起来是显而易见的(当然*是*显而易见的)，但它有时仍会迷失在所有关于 CSS3 漂亮技巧或苹果是否会克服其反闪光灯的讨论中。

人们在桌面上浏览你的网站，享受大屏幕、真正的键盘和鼠标，当然，他们也有更多时间和注意力的好处。他们经常在家或者在一个漂亮安静的办公室里；他们通常有稳定的互联网连接；他们不太可能被周围发生的事情分散注意力。

相比之下，使用移动设备的人通常都很匆忙。他们正在去某个地方的路上，他们想知道哪个公共汽车站最近；他们在咖啡馆等朋友，想快速查看新闻标题；他们在酒吧里，想要解决一场关于黛安娜·基顿哪一年赢得奥斯卡奖的争论。

此外，移动中的人们通常仍然会受到较慢的互联网连接的影响。[移动](https://forums.wowdata.org/ "World of Warcraft Mobile Forums")宽带可用性一直在提高，但网络拥塞和信号强度仍然会影响速度，即使在覆盖最广的地区也是如此。流行的公共 WiFi 网络可能遭受同样的问题。即使这些网络*表现良好，消费超过你的手机计划允许的移动数据也会严重影响你的钱包。*

因此，虽然添加响应式设计可以帮助你的网站在一些移动设备上的外观，但这仍然只是拼图的一部分。在某些情况下，为移动访问者服务的最佳方式可能是创建一个单独的移动友好型网站，该网站可以消除不必要的干扰，将移动友好型内容放在最前面，并避免高额的带宽成本。为了达到这个目的，你需要的不仅仅是一些媒体的询问。

## 移动用户如何浏览你的网站？

如果你在一个现有的网站上工作，有可能移动用户已经成群结队地访问了——而且随着时间的推移，这个数字只会越来越大。使用您最喜欢的统计软件包来查看您网站的哪些页面和功能在移动用户中很受欢迎。有可能你会看到*上下文*的问题反映在他们的使用模式中——例如，如果你在一个场馆的网站上工作，联系方式、开放时间和方向将在这个群体中最受欢迎。

也看看和你相似的网站——他们的网站有手机版吗？他们的移动网站包含或排除了哪些功能？这些网站成功吗？你觉得在移动设备上浏览这些网站容易吗？试着想象在火车上、街上或你最喜欢的酒吧使用这些网站；更好的是，在你实际做这些活动的时候去拜访他们。在这种情况下使用这个网站是容易还是有挑战性？

想想你自己最喜欢的手机网站——退后一步，用批判的眼光来看待它们。你认为这些网站最有用的地方是什么？在这个网站上导航是如何工作的？和桌面版有什么区别？排除或包含了哪些内容？你有没有漏掉什么？你可能会发现这些网站的两个版本之间有很大的差异——从主页上的项目数量，到导航的安排，到图像和风格的使用。你可能还会为你自己的网站的移动版本找到一些好主意——一定要做大量的笔记！

## 媒体询问能解决问题吗？

一旦你对你的移动网站有了概念，再看看你的桌面网站。你需要添加或删除什么？为你的网站提供类似桌面的体验还有意义吗？你在网上看到过哪些很酷的技术，你想把它们放在你的网站上吗？最重要的是——您能简单地通过巧妙使用样式表来实现这一切吗？

这些问题的答案，以及更多的问题，将帮助你决定是否只依赖于手机友好的风格，或者是否要更进一步。不过，我们敢打赌，你将更多地致力于打造新的移动体验。

照片:来自 Flickr 的由 Ed Yourdon、Morgan Everett、Adam Tinworth 和 kamshots 创作的共享许可照片。

## 分享这篇文章