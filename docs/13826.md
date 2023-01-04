# FireBug——Firefox 验证器之王

> 原文：<https://www.sitepoint.com/firebug-releases/>

作为 2006 年的人，你可能会认为很难对 Firefox 的标记验证扩展感到兴奋。然而，我不得不说 [Joe Hewitt 的新产品 Firebug](https://addons.mozilla.org/extensions/moreinfo.php?id=1843&application=firefox) 是我最近见过的最令人印象深刻的新开发者扩展(好吧，好吧，..自从 [IETab](https://addons.mozilla.org/extensions/moreinfo.php?id=1419&application=firefox) 。

**自动验证器:**

![Firebug in action](img/3efee0bfb948c1af533f6f53f3b5fb27.png)

在其非活动模式下，Firebug 的自动验证器在有效页面上是完全不可见的，在有错误的页面上，它会在状态栏中显示一个简单的“错误计数器”。绝对是少即是多。单击计数器会展开反馈工具栏，详细列出每个错误。

虽然错误描述非常方便，但对我来说，最重要的特性是能够点击错误位置，并立即在源代码中突出显示它——对于像缺少“；”这样的微妙问题尤其有用或者 CSS 中多了一个'，'。

对于这个工具，我能给的最好的支持是，我实际上已经发现自己解决了一些琐碎的验证问题，因为它们现在很容易识别。

**元素检查器:**

![Firebug' Element Inspector](img/37927b7ee6ed0452d79435e592826a60.png)

Firebug 的 Element Inspector 在很多方面与 Nicolas Moreau 的[优秀的“Inspect Element”扩展](https://addons.mozilla.org/extensions/moreinfo.php?id=434)相似，但在操作上更优雅一些。激活它可以让您用光标高亮显示任何可见的页面元素。选择一个元素会将它拖到下面的工具栏中，让您可以立即访问它的 HTML 源代码、直接针对它的 CSS 规则、影响它的属性的“框模型”视图以及对象的 DOM 级别视图——所有这些都不会影响您正在处理的页面。

非常快。非常圆滑。

正如我所说的，以前没有做过的事情不多，但我怀疑它是否曾经如此有用地结合在一起。看看这个。

## 分享这篇文章