# 你会在云端编码吗？

> 原文：<https://www.sitepoint.com/bespin-cloud-coding/>

Mozilla 实验室[最近发布了 Bespin](https://www.sitepoint.com/mozilla-launches-very-slick-cloud-based-ide/) (看起来发音是“Beh-spin”而不是“Bee-spin”)。Bespin 将提供一个全功能的代码编辑器，直接集成到您的网络浏览器中。

Mozilla 的目标是创建一个快速、易用、不吓人的编辑器，并且可以从任何地方访问。该系统将提供一个可扩展的 API 来鼓励第三方扩展和类似自然语言的命令来完全控制编辑器及其界面。

然而，Bespin 最有趣的功能可能是实时协作。您和您的编码同事将能够同时处理相同的文件，并且可以看到彼此发生的变化。这肯定会吸引所有使用敏捷开发方法的键盘共享程序员。

如果你用的是 Firefox 3，Bespin 的 0.1 版本已经上线，可以在 bespin.mozilla.com 进行测试。这在很大程度上是一个概念验证，缺乏一些最基本的编辑器中的功能。支持 HTML、CSS 和 JavaScript 文件，尽管我不建议现在就做任何实际的工作。

在线代码编辑器并不是一个新想法。然而，大多数项目都是非常基础的，通常实现一个带有行号和颜色编码的 HTML `<textarea>`的增强版本。即使在这个阶段，Bespin 实现的功能也比任何竞争对手都要先进。

Bespin 最有趣的方面是编辑器本身背后的技术。Mozilla 使用了一个 HTML5 `[canvas](http://en.wikipedia.org/wiki/Canvas_(HTML_element))`元素来实现文本编辑、颜色编码、行号、高亮、滚动条和整个界面。令人惊讶的是，只用了 62Kb 的压缩 JavaScript 就做到了。以这种方式使用`canvas`是一个新颖的想法，我怀疑它会影响许多其他项目。

Bespin 可能有点超前了。这对于网站的快速更新或者在线直播来说很方便，但是离开发者放弃他们最喜欢的基于桌面的编辑器还有很长的路要走。

你会相信在线代码编辑器如 Bespin 的代码吗？它能提供您当前 IDE 的速度、特性、安全性和灵活性吗？实时协作是有用的，但是你会每天都需要它吗？

## 分享这篇文章