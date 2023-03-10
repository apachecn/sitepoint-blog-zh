# 脚本、加载和反语

> 原文：<https://www.sitepoint.com/scripting-loading-and-irony/>

在技术世界里，我们找到的解决方案和它们解决的问题一样好。我喜欢这里的讽刺。有一天解决了一个问题，实际上可能只是掩盖了一个更大的复杂问题的症状。让我详细说明一下…

## 脚本和加载

大约在 1999 年，当我第一次开始编写 JavaScript 时，没有人会过多地考虑组件加载时的同步问题。我们将所有的脚本都放在了`<head>`部分，并将所有东西都绑定到了`window.onload`中。我们这样做是因为这是公认的智慧，而公认的智慧也还没有考虑太多，因为*这个问题从未出现过*。人们很少使用 JavaScript。哦，总有例外，就像亚伦·布德曼的全滑动、全旋转网站；它让我们所有人都感到羞愧，并且在 Netscape 4 中工作得最好。在很大程度上，脚本只用于打开弹出窗口、图像滚动效果和偶尔的下拉菜单。但是随着时间的推移，随着页面变得越来越大，脚本变得越来越复杂——由于连接速度更快，人们使用越来越大的图像——很明显，在脚本运行之前等待所有内容加载现在是一种不可接受的延迟。2005 年前后，[多种方案](http://dean.edwards.name/weblog/2006/06/again/)涌现；其中最方便、最直接的方法现在已经成为标准方法，内置于各地的图书馆中。当然，最值得注意的是浏览器直接提供的事件:`DOMContentLoaded`事件。

## 讽刺的是

关于`DOMContentLoaded`的奇妙之处在于，它在图像加载之前触发*——只要 <abbr title="Document Object Model">DOM</abbr> 可用。至少，顾名思义就是这样；实际上，它根本不会在这个时候开火。它实际上是在 <abbr title="Document Object Model">DOM—</abbr> 和可能影响 <abbr title="Document Object Model">DOM—</abbr> 的外部依赖关系已经解决时触发的。实际上，这意味着这个事件远没有绕过依赖，实际上只是绕过了图像；在* `DOMContentLoaded`触发之前，必须加载并解析页面上的所有非延迟脚本和样式表*。当我们第一次开始使用`DOMContentLoaded`时，这并不明显。我只是开始认真考虑这个问题，因为我发现自己处于这样一种情况:在脚本和样式表解析之前，一旦`<head>`可用，我就需要脚本来执行。(准确的说，不一定是*之前的*，就是不能是*之后的*。)当`DOMContentLoaded`成为编写脚本的标准方法时，我们主要只考虑图像。但是图像不再是敌人。当你发现自己在等待一个页面加载完毕，似乎要等很长时间，你通常是在等待图片吗？或者是来自半打不太强大的广告服务器的半打广告联合脚本？所有这些都必须在`DOMContentLoaded`发射前解决，现在越来越多的是，这比图像要长得多。因为导致延迟的不是依赖关系的大小，而是等待所有这些服务器的响应。*

## 下一章

我做了一点研究，看看是否有人在谈论这个问题，我发现关于[是否应该在样式表](http://molily.de/weblog/domcontentloaded)之前或之后触发`DOMContentLoaded`有一点争论。(显然，Opera 不同于其他浏览器，Firefox 可能不一致。)就我个人而言，根据具体情况，我可以看出这两种方法都有所帮助。这一切都让我想起了我在 2005 年为最初的问题提出的解决方案。名为 [domFunction](http://www.brothercake.com/site/resources/scripts/domready/) ，它基于一个异步计时器，该计时器仅检查`<body>`元素的存在(以确保在 <abbr title="Document Object Model">DOM</abbr> 中引用和创建内容是安全的)，并在它一出现就触发回调。当时它从未真正流行起来，也许是因为它不像其他方法那样简单或优雅。令人高兴的是，这个解决方案也解决了我的新问题！就依赖性而言，它是真正的不可知论者，这也是我选择在我最新的脚本中采用的。(虽然最初的解决方案只每 250 毫秒循环一次，但我把它提高到了 20 毫秒，因为我知道这只是等待`<head>`的最短时间，实际上只需要一到两次迭代。)它也可以适于检查其他特定的依赖性；这是我接下来几周要考虑的事情。它可以检查`<head>`、`<body>`、`styleSheets`集合和/或 <abbr title="Cascading Style Sheets">CSS</abbr> 的渲染应用(通过`computedStyle`评估)；它可以检查特定元素的存在，或者特定的脚本对象，或者任何你需要它等待的东西。这种可能性很有趣。同样有趣的是由 [<abbr title="Document Object Model Level 2">DOM 2</abbr> 突变事件](https://www.w3.org/TR/DOM-Level-2-Events/events.html#Events-eventgroupings-mutationevents)和/或[通用对象观察器](http://stackoverflow.com/questions/1029241/javascript-object-watch-for-all-browsers#answer-1270182)提供的可能性。也许我们可以观察整个文档状态的变化，以了解特定节点是否处于可供脚本使用的状态。或者了解他们是否受到了特定 <abbr title="Cascading Style Sheets">CSS 的影响。这些都是好主意，等着瞧吧。*缩略图致谢:[戈德堡](http://www.flickr.com/photos/goldberg/50141464/)*</abbr>

**note:**Want more?

如果你想阅读更多詹姆斯的作品，请订阅我们每周的科技极客时事通讯， *[《科技时报》](https://www.sitepoint.com/newsletter/)* 。

## 分享这篇文章