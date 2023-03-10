# 用少量的 HTML 为你的 Firefox 扩展增添趣味

> 原文：<https://www.sitepoint.com/firefox-extensions-with-html/>

本周标志着 CodeBurner 参考工具的几个新版本[的发布:一个 OS X 桌面小部件、一个 Opera 小部件和一个独立的 AIR 应用。当然，您仍然可以获得该工具的原始版本，Firefox 的 CodeBurner 或 Firebug 的 CodeBurner。现在，无论您在哪里进行 web 开发，都可以访问 HTML 和 CSS 参考信息！](https://getfirebug.com/)

为了突出这个版本，我想我应该告诉你我在开发这些扩展时使用的一个关键技术的内幕:HTML。

是的，普通的 HTML。明智地使用，它可以成为让您的 Firefox 扩展脱颖而出的秘密武器。

本文假设读者了解开发 Firefox 扩展的基础知识。如果你是游戏新手，你可以用我的免费的[建立你自己的火狐扩展](https://www.sitepoint.com/books/byofirefoxpdf1/) PDF 电子书来加速。

## 为什么使用 HTML？

XUL 组件的一个重要方面是，它们的外观很大程度上不受我们的控制。这有助于界面的一致性和可访问性；无论是使用默认皮肤、自定义皮肤还是大字体之类的平台可访问性调整，它们看起来都与浏览器界面的其余部分一样。

然而，有时你需要给你的扩展增加一点样式或功能，而不仅仅是使用 XUL。在这种情况下，您可以考虑在 XUL 文档中使用 HTML。这使您在开发网页时可以访问所有的 CSS 和 JavaScript 功能。

Firebug 是部分界面需要 HTML 的扩展的一个典型例子，因为 XUL 缺少这项工作所需的元素。让我们看看 Firebug 的 HTML 面板，这是被检查的 DOM 的一个表示。想想你会如何在 XUL 建造它:

**图一。Firebug 中的 HTML 面板**

![The HTML panel in Firebug](img/0728a088a5ce35e110827cc0c8b8fde9.png)

这是个棘手的问题。在 XUL 建造它是不可能的，因为 XUL 缺乏任何可以被安排来创建一个具有语法突出的树状结构的元素。最接近的选项是`tree`元素，它可以用来创建类似的结构，但是只能在列和行的约束内。下面是在 DOM 检查器中运行的元素:

**图二。文档:DOM 检查器中的 DOM 节点面板**

![The document: DOM nodes panel in the DOM Inspector](img/6226de462674e5830513462850cc3b34.png)

当我第一次为 Firefox 构建 [CodeBurner 时，我搜索了整个](https://getfirebug.com/) [XUL 参考文献](https://developer.mozilla.org/en/XUL_Reference)，寻找一组可以用来构建 DOM 树的元素。当发现没有时，我决定用 HTML 构建它，用 CSS 样式化它，并用一些定制的 JavaScript 添加展开/折叠行为。

以下是我认为在开发 Firefox 扩展时使用 HTML 的其他一些例子:

*   用于创建“关于”页面或类似的对话框，只包含富文本，而不是实际的界面组件。
*   当需要测量一个元素的 offsetWidth 或 offsetHeight 时(这些属性对于 XUL 元素是不可用的)，您可以在 XUL 元素中创建一个临时的 HTML 元素，并测量它。
*   当元素需要在界面中绝对定位时(XUL 元素不支持绝对定位)。
*   如果需要更多的样式灵活性，如圆角或背景图像。例如，XUL `textbox`元素特别难以设计，所以使用 HTML `input`可能是达到预期效果的唯一方法。

如果你发现自己想使用 HTML 仅仅是因为它额外的样式潜力，你应该首先考虑你的 *是否真的需要* 的样式，或者它是否只是养眼，或者可以通过其他方式完成。

绝对定位尤其应该小心使用，因为它限制了界面的灵活性。在很多用例中，有其他方法可以获得最终结果:例如,`stack`元素(元素可以被绝对定位在其中),或者简单地将元素留在它们流动的位置。

界面真正需要增加样式潜力的一个很好的例子是删除搜索框内容的 X 图标；这是在 1.5 版本中添加到火狐的 [CodeBurner 中的。这被证明不可能用 XUL 实现，所以使用 HTML 是唯一的选择。](https://getfirebug.com/)

**图 3。火狐 1.5 版 CodeBurner 中的搜索框**

![The search box in CodeBurner for Firefox version 1.5](img/c25e718e7233e9b4be3ca11ab68aa54b.png)

我已经向您展示了一些何时适合使用 HTML 的例子，但是这个决定总是需要根据具体情况做出判断。也就是说，让我们来看看你实际上是如何实现它的。

## 分享这篇文章