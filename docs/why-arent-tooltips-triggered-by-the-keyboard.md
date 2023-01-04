# 为什么工具提示不是由键盘触发的？

> 原文：<https://www.sitepoint.com/why-arent-tooltips-triggered-by-the-keyboard/>

为什么 <abbr title="Graphical User Interface">GUI</abbr> 工具提示不是键盘触发的？是有确凿的原因，还是只是集体盲点？

我特别喜欢博客发布的一个方面是反思方法和技术是如何随着时间的推移而变化的。革命性的想法如何变得司空见惯，或者那些看似进步的想法如何最终被遗忘和无用。

但是就工具提示的行为来说，十年来都没有什么变化——那时它们只是鼠标才有的，今天依然如此。

我第一次思考这个问题是在 2004 年，当时我的反应是写一些 JavaScript 来填补这个漏洞。[这是我为](http://www.brothercake.com/site/resources/scripts/tooltips/)想出的剧本，它确实完成了任务。如果我今天重写，我会在 [`DOMFocusIn`](https://www.w3.org/TR/DOM-Level-2-Events/events.html#Events-eventgroupings-uievents) (大多数现代浏览器和气泡都支持的 <abbr title="Document Object Model Level 2">DOM 2</abbr> 用户界面事件)和 <abbr title="Internet Explorer">IE</abbr> 的专有 [`onactivate`](http://msdn.microsoft.com/en-us/library/ms536787(VS.85).aspx) 事件(其行为类似于`onfocus`，但也是气泡)之间分叉事件模型。这里重要的区别是标准的`onfocus`事件不应该冒泡，而且在大多数实现中也不会冒泡(主要是在 Firefox 中除外)。但是如果我们使用*被*设计成冒泡的事件，我们可以用单个事件监听器实现行为，而不是像我最初的解决方案那样，必须为每个可聚焦的元素单独添加一个监听器。

我仍然在我的网站上实现这个脚本，经常在我开发的新应用程序中添加类似的功能，无论是个人项目还是客户项目。例如，最近，我一直在更新所有五个版本的[CodeBurner](https://getfirebug.com/)——我们为 web 开发人员提供的参考工具——对于其中一些环境，我需要脚本化的解决方案。例如，在 Adobe AIR 运行时，鼠标或键盘根本不会为 <abbr title="HyperText Markup Language">HTML</abbr> 元素生成工具提示；相反，我实现了一个更广泛的解决方案来响应所有此类事件。

然而，每当我实现一个焦点驱动的解决方案时，我想知道为什么我还必须这样做。而且说实话，我也在想我*是不是应该*。如果界面通常缺乏焦点驱动的工具提示，那么单个应用程序忽略这种行为规范并实现它们是正确的吗？

抛开所有必要因素不谈，JavaScript 解决方案永远不可能像由 <abbr title="Operating System">OS</abbr> 本身生成的解决方案那样紧密集成；它永远不会真的只是一个备用解决方案。

举个例子，定位是个问题，因为文档生成的元素不能移动到窗口之外。即使它们可以，我们仍然局限于来自文档元素的工具提示。我们无法为窗口按钮或浏览器之外的任何东西提供它们。

Opera 是一款以某种方式提供键盘触发工具提示的浏览器；例如，当链接工具提示通过[空间导航](http://www.opera.com/browser/tutorials/nomouse/)聚焦时，它会显示链接工具提示。但即使在那里，这些工具提示也仅限于它所显示的文档。如果这是个好主意，为什么对浏览器 chrome 本身不是个好主意呢？

那么，这么多年来，操作系统已经有了能够生成工具提示的成熟技术，以及迎合只使用键盘的用户的可访问性框架，为什么这两个特性从来没有结合在一起呢？

为什么工具提示仍然是鼠标才有的功能？

**note:**Want more?

如果你想阅读更多詹姆斯的作品，请订阅我们每周的科技极客时事通讯， *[《科技时报》](https://www.sitepoint.com/newsletter/)* 。

## 分享这篇文章