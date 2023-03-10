# sIFR 和 html 覆盖

> 原文：<https://www.sitepoint.com/sifr-and-htmloverlays/>

这里有几个有趣的新玩具在过去的 24 小时内浮出水面。首先，迈克·戴维森、肖恩·因曼和托马斯·乔金发布了 [sIFR](http://www.mikeindustries.com/blog/archive/2004/08/sifr) ，这是对肖恩·因曼臭名昭著的 [IFR](http://www.shauninman.com/mentary/past/ifr_an_fir_alternative.php) 闪光灯替换技术的新尝试。Flash 替换是一种建立在语义 HTML 之上的智能技术，其中部分文本被替换(使用 JavaScript)为 Flash 文件中嵌入的自定义字体的 Flash 等价物。sIFR 改进了以前的版本，确保替换的闪存文件考虑了标题的确切大小(s 代表可伸缩)。它也可以应用于页面上的任何元素，而不仅仅是标题。

虽然这种技术对于增加标题的趣味是非常好的，但是我建议避免在大块的正文中使用它。虽然 Flash 渲染的文本现在可以被复制和粘贴，但在可访问性和可用性方面，它在许多方面仍然落后于普通的 HTML 文本。例如，在 sIFR 中，使用普通浏览器字体大小控件调整文本大小只有在整个页面刷新后才会生效。这并不意味着放弃这项技术:这是一项了不起的工作，也是 HTML、JavaScript 和 Flash 一起增强页面设计而不降低底层标记的语义价值的一个很好的例子。

第二个玩具是 [HTMLoverlays](http://disruptive-innovations.com/zoo/20040830/HTMLoverlays.html) ，一个来自 Mozilla Composer/Nvu fame 的丹尼尔·格拉次曼的巧妙黑客，基于[Laurent jouan eau](http://ljouanneau.com/blog/2004/08/30/337-html-overlays)的一个想法。叠加是 Mozilla XUL 语言的一个受启发的特性，它允许通过将两个文档合并在一起，将额外的元素移植到 XUL 文档中——有点像 XSLT，但语法更简单。HTMLoverlays 是一个小 JavaScript，它为现代浏览器(IE/Windows、Safari 和 Mozilla)提供了与 HTML 文档相同的功能。它的工作方式类似于客户端包含——定义一个基本的文档结构，然后使用文档头中的<link rel="overlay">元素将它与一个覆盖文件相关联。如果这没有任何意义，查看 [Daniel 的示例](http://disruptive-innovations.com/zoo/20040830/demo.html)上的源代码应该可以澄清一些事情。

很高兴看到 JavaScript 创新的新浪潮继续。

## 分享这篇文章