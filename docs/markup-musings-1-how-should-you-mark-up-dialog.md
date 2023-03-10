# 标记思考#1:你应该如何标记对话框？

> 原文：<https://www.sitepoint.com/markup-musings-1-how-should-you-mark-up-dialog/>

语义标记。几乎每个理解这个概念的开发人员都同意它，那么为什么这么多人(包括我自己)经常在应用这些原则时遇到困难呢？

虽然毫无疑问，有时这是普通的懒惰，但我知道，就我个人而言，有时我想做出正确的决定，但最终却抓耳挠腮。虽然 HTML 标准在模仿我们从传统书籍中理解的基本形式和结构(即页面、段落、标题、表格和列表)方面做得相当不错，但我越来越经常地发现自己面临着标记一个不太适合这些结构的文档——例如餐厅菜单、电影剧本和连环漫画。每一种都有一种成熟的格式，不一定能无缝地转移到网络上。

虽然我不能说我对所有这些问题都有明确的答案，但我认为至少把这个问题抛出来，并获得一些不同的观点是有用的——包括我自己。

标记对话框是我们要研究的第一个难题。

![Casablanca screenplay - page 126](img/a746a6949506f5c093cfac2be2f7235e.png)让我们从一个经典的片段开始——在网络上标记[电影剧本](http://www.casalinx.com/script/casablanca_screenplay.pdf)的最后一页最明智的方法是什么？

乍一看，它似乎不仅仅是一个标准的段落系列(`<p>`)。它有非常特殊顺序的单元，但是它肯定不是一个有序列表(`<ol>)`)。在结构上，它 ***看起来*** 非常像一个定义列表(`<dl>)`——一个项目列表，每个项目都有一个附加的文本块。引号(`<q>`)和块引号(`<blockquote>`)是如何融入其中的？

幸运的是，在这种情况下，我们不是第一个问这个问题的人，在 Accessify 论坛上已经有了关于对话框标记的重要讨论，我们可以借鉴。Brothercake，Andrew K，Kev 和我考虑了各种选择，这就是我们的最终结果。

最简单的方法无疑是将整个事情标记为简单的一系列段落，用几个跨度来表示说话者。虽然这可能不是站不住脚的，但它也没有在文档中嵌入太多有用的信息。

一些人建议的另一种方法是使用定义清单(`<dl>`)。然而，尽管定义列表非常符合视觉效果,但在这种情况下，它们在语义上是非常明确的禁忌。认为每个说话者都是一个定义类型(`<dt>`)并且每一段附加的文字都是一个“定义描述”(`<dd>`)是难以成立的。

有趣的是，目前[正提议在 HTML5](http://www.whatwg.org/specs/web-apps/current-work/#dialog) 中使用基于当前`<dl>`结构的`<dialog>`标签。我也不认为这是正确的方法。

因为理论上每个对话块都是引用的语音，所以似乎是一个有意义的结构来接收每个对话片段。此外,`blockquote` s 还允许我们在其中嵌入块级和内联元素，为我们提供了一个灵活的工作基础。事实上，W3C 建议 blockquote 中的文本应该总是包含在 block 元素中。

所以，我们从这样的事情开始:

```
 <blockquote>
<p>Louie, I think this is the beginning of a beautiful friendship</p>
</blockquote> 
```

由于说话者的数量总是有限的，所以我认为为每个说话者创建一个类，并将该类附加到每个块引用上是有意义的。我们没有强迫 T1 去使用它，但是它给了我们“钩子”,让我们在以后做更有用的事情——例如，突出、强调、隐藏或着色特定人物的对话。

```
 <blockquote class="rick">
<p>Louie, I think this is the beginning of a beautiful friendship</p>
</blockquote> 
```

根据 W3.org[的说法](https://www.w3.org/TR/html4/struct/text.html#h-9.2.1)`<cite>` 标签“*包含了对其他来源*的引用或参考。在我们的例子中，我们将每一段都归属于一个发言人，所以使用`<cite>`标签来表示谁在发言是有意义的。为了清楚起见，我也给了它一个“speaker”类，但是这是可选的。

所以，现在我们有了这样的东西:

```
 <blockquote class="rick">
<cite class="speaker">Rick</cite>
<p>Louie, I think this is the beginning of a beautiful friendship</p>
</blockquote> 
```

最后，舞台指导是几乎所有剧本都有的另一个元素。因为这些是用更传统的描述性格式写的，所以它们非常适合普通的旧段落。如果您希望在同一个文档中有其他类型的段落，例如，演职员表、前言或其他注释，您可以选择使用特定于它们的类来标识您的舞台指示。如果没有，简单的段落标记就足够了。

```
 <blockquote class="rick">
<cite class="speaker">Rick</cite>
<p>Louie, I think this is the beginning of a beautiful friendship</p>
</blockquote>

<p  class="directions">The two walk off together into the night.</p>
<p  class="directions">FADE OUT.</p>
<p  class="directions">THE END</p> 
```

下面是叠加在原始页面上的相同结构标记的可视化表示。

![A Visual representation of the markup super-imposed over the original Casablanca script](img/9e3776df70afa5a3be2fca92116aa0c7.png)

那么，你的结论是什么？

你能找到更好的方法吗？

有没有其他的文档格式你在翻译到网络上时遇到了困难？

另外，如果你发布 HTML 代码片段，记得“转义”你的代码(即

## 分享这篇文章