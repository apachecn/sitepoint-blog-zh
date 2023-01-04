# 全面了解 jQuery 选择器

> 原文：<https://www.sitepoint.com/comprehensive-jquery-selectors/>

这篇文章由[马特·史密斯](https://github.com/AllThingsSmitty)和[蒂姆·塞韦里恩](https://www.sitepoint.com/author/tseverien/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！

无论您想要操作网页上的元素内容、将事件附加到它上面，还是做其他事情，您都需要首先选择它。这就是 jQuery 选择器发挥作用的地方:它们构成了库的关键部分。

在本教程中，我将涵盖所有这些选择器，并指出使用它们时需要记住的重要事项。

## jQuery 选择器

这些选择器的主要目的是选择网页上符合特定标准的元素。标准可以是任何东西，比如它们的 id、类名、属性或者任何或所有这些的组合。jQuery 中的大多数选择器都基于现有的 CSS 选择器，但是该库也有自己的自定义选择器。

### 基本选择器

你可以使用元素的 ID `$("#id")`、类`$(".class")`或标签名`$("li")`来选择网页上的元素。您也可以使用这些选择器的组合，如`$(".class tag")`或选择多个选择器的组合结果，如`$("selectorA, selectorB, selectorC")`。

jQuery 还提供了一些其他的基本选择器，我在下面列出了它们:

*   [:标题](http://api.jquery.com/header-selector/)选择器——假设你要选择一个`<section>`中的`<h1>`、`<h2>`、`<h3>`等所有标题。在这种情况下，您可以使用冗长的`$("section h1, section h2, section h3")`选择器或者更短的`$("section :header")`选择器。两者将做同样的工作，后一个相对更容易阅读。在[本演示](http://codepen.io/SitePoint/pen/xOJxJv)中，标题选择器已将所有标题的背景设置为黄色。

*   [:目标](http://api.jquery.com/target-selector/)选择器——该选择器返回其`id`与文档 URI 的片段标识符或散列相匹配的元素。例如，如果 URI 是“https://sitepoint.com/#hash”。然后，选择器`$("h2:target")`将选择元素`<h2 id="hash">`。

*   [:动画](http://api.jquery.com/animated-selector/)选择器—当选择器运行时，该选择器返回所有正在播放动画的元素。这意味着任何在选择器执行后开始动画的元素都不会被返回。另外，请记住，如果您正在使用不带 effects 模块的自定义 jQuery 构建，该选择器将会抛出一个错误。在[这个演示中](http://codepen.io/SitePoint/pen/zBLYJX)只有动画框因为选择器而变成橙色。

### 基于索引的选择器

除了我们上面讨论的基本选择器之外，您还可以根据元素的索引来选择元素。jQuery 提供了自己的一组基于索引的选择器，这些选择器使用从零开始的索引。这意味着要选择第三个元素，必须使用索引 2。

以下是所有基于索引的选择器的列表:

*   [:eq(n)](http://api.jquery.com/eq-selector/) 选择器——该选择器将返回索引`n`处的元素。从版本 1.8 开始，它接受正和负的索引值。当提供负值时，从最后一个元素开始向后计数。
*   [:lt(n)](http://api.jquery.com/lt-selector/) 选择器——该选择器将返回索引小于`n`的所有元素。从版本 1.8 开始，它也接受正值和负值。就像`:eq(n)`选择器一样，当提供负值时，从最后一个元素开始向后计数。
*   [:gt(n)](http://api.jquery.com/gt-selector/) 选择器——这个选择器就像`:lt(n)`。唯一的区别是它返回索引大于或等于`n`的所有元素。
*   [:第一个](http://api.jquery.com/first-selector/)选择器——这将返回网页上第一个匹配的 DOM 元素。相当于`:eq(0)`和`:lt(1)`。`:first`和`:first-child`选择器的一个区别是`:first-child`可以选择多个元素，每个元素都是其父元素的第一个子元素。
*   [:最后一个](http://api.jquery.com/last-selector/)选择器——这个类似于`:first`选择器，但是返回最后一个孩子。
*   [:even](http://api.jquery.com/even-selector/) 选择器—这将返回索引为偶数的所有元素。因为 jQuery 中的索引是从零开始的，所以选择器选择第一个孩子、第三个孩子等等。这似乎违背直觉，但这就是它的工作方式。
*   [:奇数](http://api.jquery.com/odd-selector/)选择器——这个选择器的工作方式类似于`:even`选择器，但是返回奇数索引的元素。

在下面的例子中，你可以点击三个按钮`:lt`、`:gt`和`:eq`，它们会随机生成一个索引并将结果选择器应用到一个列表中:

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看笔[基于索引的选择器](http://codepen.io/SitePoint/pen/VjBwEZ/)。