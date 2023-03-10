# 看看 EC markup:ECMAScript 规范的定制 HTML

> 原文：<https://www.sitepoint.com/look-at-ecmarkup-ecmascript-spec-custom-html/>

正如你们很多人可能已经知道的，几周前[宣布](https://github.com/tc39/ecma262/releases/tag/es2016-draft-1)ECMAScript 规范现在已经[转移到 GitHub](http://tc39.github.io/ecma262/) 。以前它有 PDF 和 HTML 格式的，但是实际的编辑过程是在 Word 文档中完成的——这显然不是最好的选择——所以转到 GitHub 是个好消息。

关于这一举动，最有趣的事情之一可能是文档本身现在基于一种叫做 [Ecmarkup](http://bterlson.github.io/ecmarkup/) 的东西，它被描述为:

> 许多定制元素和一个工具链，适用于正式指定 ECMAScript 提案的语义。

简而言之，Ecmarkup 是专门为 ECMAScript 规范文档设计的 HTML 扩展。前述公告将其描述为“为编写 ECMAScript 规范而构建的 HTML 定制方言”。通过检查 spec 页面的源代码，您可以对标记有所了解。以下是截图:

![Example of Ecmarkup's custom tags](img/4283d940c3d021bf1b1338716a21eb4d.png)

您可以看到正在使用的自定义`<emu-clause>`和`<emu-xref>`元素，并且`<emu-clause>`元素在 CSS 中被设置为`display: block`。这与开发人员对旧 IE 做的事情是一样的，让它识别新的 HTML5 元素。未知元素开始时没有样式，计算到内联，[Mark Pilgrim](http://diveintohtml5.info/semantics.html#unknown-elements)解释得很好，所以这些需要`display: block`被添加到 CSS 中(当然，假设它们被用作块元素)。

很自然，如果你试图验证这样一个页面，你会得到[吨的错误](https://html5.validator.nu/?doc=http%3A%2F%2Ftc39.github.io%2Fecma262)类似于下面的截图所示:

![Ecmarkup's validation errors](img/23cfccbc143479ce1e91b7cd2edb66e1.png)

但是在讨论像这样使用定制 HTML 的任何可能的缺点之前，让我们更深入地了解一下 Ecmarkup 提供了什么。

## Ecmarkup 的功能

Ecmarkup 中几乎所有的自定义元素都以`emu-`前缀开头。这与[长相滑稽的澳大利亚大鸟](https://en.wikipedia.org/wiki/Emu)无关；它只是“Ecmarkup”的缩写。 [Ecmarkup 规范](http://bterlson.github.io/ecmarkup/)本身就是建立在 Ecmarkup 之上的，所以你可以在这里插入你的 [Inception](http://www.imdb.com/title/tt1375666/) 笑话。

这是这种语言的一些特点:

*   **子句**:`<emu-clause>`元素是最常用的元素之一。作为其使用的一个例子，规范中覆盖全局对象的部分被包装在一个单独的`<emu-clause>`元素中，并在其中嵌套了其他的`<emu-clause>`元素。所以这个元素有点像 HTML5 的`<section>`和`<article>`元素。
*   **注释**:这些用`<emu-note>`元素表示，不言自明；它们表示子句中的旁注。在[底部的 hasOwnProperty()方法](http://tc39.github.io/ecma262/#sec-object.prototype.hasownproperty)部分可以找到一个例子。
*   **内联元素**:除了块级元素，Ecmarkup 还提供了内联元素，包括`<emu-const>`和`<emu-val>`。
*   **交叉引用**:这些元素使用 ID 属性交叉引用规范的其他部分。他们可能会指出从句、注释、例子、图表等。这些不会取代常规链接，而是用来包装它们。
*   **算法和方程**:使用`<emu-alg>`和`<emu-eqn>`元素表示，在整个规范中使用。
*   **表格和数字**:分别用`<emu-table>`和`<emu-figure>`表示。就像交叉引用一样，这些不会取代常规的表格和图形元素；他们把它们包起来。[这里有一个表格的例子](http://tc39.github.io/ecma262/#table-9)和[这里有一个图](http://tc39.github.io/ecma262/#figure-1)。如果您检查页面上的元素，您会看到这些定制元素在源代码中使用。

在规范文档的源代码中还包含了 Ecmarkup 规范中没有讨论的其他元素(例如`<emu-geq>`)。当我向规范的编辑布莱恩·泰尔森询问此事时，他解释说，有些元素是存在的，是在构建过程中出于样式目的而生成的，但作者并不希望使用它们。

## 自定义 HTML 的可能缺点

从技术上来说，正如泰尔森通过电子邮件确认的那样，ECMAScript 规范的定制 HTML 不符合定制元素的标准方式。例如，他们没有按照规范的要求[注册元素](http://webcomponents.org/articles/introduction-to-custom-elements/)。仅这一事实就可能引发一些危险信号。

此外，通过避免 HTML 的标准元素，可访问性的好处可能会被扼杀。例如，大多数浏览器[通过屏幕阅读器和其他辅助技术的可访问性 API](http://www.html5accessibility.com/)来公开语义。通过使用未知元素，这些潜在的好处就失去了。当然，如果以非标准的方式完成，定制元素在语义上将与普通的`<div>`处于同一层次，因此不一定会损失可访问性，只是没有收益。

## 自定义 HTML 的好处

尽管就语义而言，HTML 是一种相当丰富的语言，但有时可用的选择不够具体。除了缺点之外，规范对这些定制元素的处理是独一无二的，为开发新项目的开发人员树立了一个有趣的先例。

一些复杂的应用程序需要大量的标记。例如，尝试在 Gmail 等网络应用程序上检查源代码，如下图所示:

![Gmail's div soup](img/3872e06de884f4ec005ed2dc0c8e3bde.png)

也许 Gmail 的开发人员有某种方法可以更容易地处理所有这些标记(大部分可能是通过 JavaScript 生成的)，但如果有一组自定义的标记(如 Ecmarkup 中的标记)，很多标记会更容易维护。

当然，我并不是说 ECMAScript spec 文档是第一个做这种事情的文档，但是这个项目的可见性确实让这项技术受到了一些关注。

我还应该指出，在这种情况下使用前缀`emu-`是正确的。这确保了这些元素不会有一天真的存在，然后在未来的浏览器中崩溃。例如，尽管文档使用了`<code>`和`<var>`元素(两者都是有效的 HTML 元素)，但所有新元素都有前缀，以避免将来与 HTML 规范发生冲突。

## 最后的想法

我仍然希望更好地理解 Ecmarkup 在新 ECMAScript 规范中是如何使用的，但这应该足以让您大致了解他们做了什么，以及可能的优点和缺点。[亲自查看新的规格](http://tc39.github.io/ecma262/)并查看来源。

你有什么想法？你认为以这种方式使用自定义元素会让更多的应用程序受益吗？或者说这样超出标准的问题太多了？

## 分享这篇文章