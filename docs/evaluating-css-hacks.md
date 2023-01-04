# 评估 CSS 黑客

> 原文：<https://www.sitepoint.com/evaluating-css-hacks/>

Vincent Garcia 报道了一个针对 Safari 的 [CSS 黑客(由](http://blog.vinniegarcia.com/oldstuff/000137.html) [Anne Van Kesteren](http://annevankesteren.nl/archives/2004/04/min-height-in-safari) 发现)，该黑客利用 CSS 2.1 表格模型为最小高度属性提供支持。IE/Windows 不支持表格模型，但无论如何都将高度视为最小高度。文森特在他的条目中进一步解释了黑客攻击。

对于任何 CSS 黑客来说，在使用之前进行评估以确保它有意义是很重要。CSS 黑客在 web 开发社区中是一个有争议的问题，一些人认为它们弊大于利，而另一些人则认为它们对于确保 CSS 在现实世界中的可行性至关重要。PPK 的专栏[保持 CSS 简单](http://www.digital-web.com/columns/keepitsimple/keepitsimple_2003-11.shtml)给出了反对使用黑客的有力论据。

如果可能的话，我倾向于避免黑客攻击(例如，可以通过不在同一个元素上声明 padding 和 width 来避免 box model 黑客攻击)，但是当它们无法避免时，理解它们的含义是很重要的。CSS-discuse Wiki 上的 GoodCSSHack 很好地概述了可维护的黑客的品质。

在 Safari 最小身高黑客的情况下，我目前还没有决定。如果我理解正确的话，它根本不是真正的黑客，因为它利用了 CSS 规范中记录的行为。这意味着黑客应该在未来的浏览器版本中表现正确。然而，考虑到 Safari 浏览器微小的市场份额和 Safari 用户越早升级越好的趋势，为 Safari 提供适当的最小高度支持似乎并不值得。

尽管如此，它还是工具箱的另一个工具。

## 分享这篇文章