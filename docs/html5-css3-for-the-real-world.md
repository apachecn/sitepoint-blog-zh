# 现实世界的 HTML5 和 CSS3

> 原文：<https://www.sitepoint.com/html5-css3-for-the-real-world/>

![htmlcss2thumb](img/d1b531ecd770df998ea32dbc2cf0f23c.png)

以下是我们的书的摘录，由 Alexis Goldstein、Louis Lazaris 和 Estelle Weyl 撰写的《真实世界的 CSS3，第二版。世界各地的商店都有出售，或者你可以在这里买到电子书。

尽管 HTML5 对内容的标记方式进行了重大改变，但值得注意的是，这些改变不会导致旧浏览器堵塞，也不会导致布局问题或页面错误。

这意味着，你可以使用任何包含有效 HTML4 或 XHTML 标记的旧项目，将 doctype 改为 HTML5(我们将在第 2 章中介绍)，页面将像以前一样出现在浏览器中。HTML5 中的变化和增加已经被实现到语言中，以确保向后兼容旧的浏览器——甚至旧版本的 Internet Explorer！当然，这并不能保证这些新特性会起作用，这仅仅意味着它们不会破坏你的页面或者引起任何明显的问题。

即使对于更复杂的新功能(例如，API)，开发人员也想出了各种解决方案来为不支持的浏览器提供等效的体验，同时拥抱 HTML5 和 CSS3 提供的令人兴奋的新可能性。有时，这就像提供后备内容一样简单，比如为没有本地视频支持的浏览器提供 Flash 视频播放器。不过，在其他时候，有必要使用脚本来模拟对新特性的支持。

这些“填隙”技术被称为**。在构建高性能的 web 应用程序时，依靠脚本来模拟本机特性并不总是最好的方法，但随着我们不断发展以包含新的增强功能和特性，这是一个不可避免的痛苦，就像我们将在本书中讨论的那些一样。幸运的是，在撰写本文时，像 Internet Explorer 6 到 9 这样的老浏览器不支持 HTML5 和 CSS3 中的许多新功能，现在只有不到 10%的网络访问者在使用[。越来越多的人使用被称为“常青树”的浏览器；](https://gs.statcounter.com/#browser_version-ww-monthly-201502-201502-bar)也就是自动更新的浏览器。这意味着，随着旧浏览器份额的减少，新功能将对更大的受众起作用，并最终对所有人起作用。**

 **在本书中，我们可能偶尔会推荐回退选项或聚合填充来填补浏览器不兼容的漏洞；我们还会尽力提醒您使用这些选项的潜在缺点和陷阱。

当然，值得注意的是，有时根本不需要回退或聚合填充；例如，当使用 CSS3 在你的设计中创建圆角时，老浏览器的用户看到方形框通常没有坏处。网站的功能没有退化，那些用户也不会知道他们错过了什么。

随着课程的进展和新主题的引入，如果您计划在项目中使用其中一个主题，我们强烈建议您咨询浏览器支持参考，例如前面提到的[我可以使用…](http://caniuse.com/) 吗？这样，您将知道如何以及是否提供回退或多填充。必要时，我们会偶尔讨论如何确保不支持的浏览器有可接受的体验，但好消息是，随着时间的推移，这越来越不是问题。

## 不断增长的移动市场

现在开始学习和使用 HTML5 和 CSS3 的另一个令人信服的原因是爆炸式增长的移动市场。根据一个消息来源，2009 年，只有不到 1%的网络使用是在移动设备和平板电脑上。到 2014 年年中，这一数字已经上升到超过 35%的[！在五年多一点的时间里，这是一个惊人的增长率。那么这对学习 HTML5 和 CSS3 的人来说意味着什么呢？](https://gs.statcounter.com/#desktop+mobile+tablet-comparison-ww-monthly-201408-201408-bar)

HTML5、CSS3 和相关的前沿技术在许多移动 web 浏览器中得到了很好的支持。例如，iPhone 和 iPad 等 iOS 设备上的移动 Safari、Opera Mobile、Android 浏览器和 UC 浏览器都提供了强大的 HTML5 和 CSS3 支持。其中一些浏览器支持的新功能和技术包括 CSS3 动画、CSS flexbox、Canvas API、Web 存储、SVG、离线 Web 应用等。

事实上，我们将在本书中介绍的一些新技术是专门为移动设备设计的。设计出离线网络应用和网络存储等技术，部分是因为越来越多的人用移动设备访问网页。这种设备通常对在线数据的使用有限制，因此可以从离线访问 web 应用程序的能力中受益匪浅。

我们将在第 11 章触及这些主题，以及整本书的其他主题，为你提供为各种设备和平台创建网页所需的工具。

## 开始真正的东西

推进新技术并期望只为一个级别的浏览器创作页面和应用是不现实的。在现实世界中，在一个我们希望 HTML5 和 CSS3 取得更大进展的世界中，我们需要准备好开发跨各种环境工作的页面。这一格局包括现代浏览器、遗留下来的旧版本的 Internet Explorer，以及爆炸式增长的移动设备市场。

是的，在某些方面，为不同的用户代理提供不同的指令集类似于早期的 Web，其混乱的浏览器嗅探和代码分叉。但这一次，新代码更加经得起未来的考验:当旧浏览器不再普遍使用时，你需要做的就是删除所有的后备和聚合填充，只留下针对现代浏览器的代码库。

HTML5 和 CSS3 是引领 web 页面创作进入一个更加激动人心的世界的领先技术。因为所有现代浏览器都为大量 HTML5 和 CSS3 特性提供了出色的支持，所以创建功能强大且易于维护的面向未来的网页比以往任何时候都更容易。

所以，关于“为什么”已经说得够多了，让我们开始探究“如何”吧！

## 分享这篇文章**