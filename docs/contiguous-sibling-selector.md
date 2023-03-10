# 连续兄弟选择器

> 原文：<https://www.sitepoint.com/contiguous-sibling-selector/>

我不时会想到一个新的 <abbr title="Cascading Style Sheets">CSS</abbr> 选择器来解决一个特定的问题。不久前我想到了[正则表达式匹配属性选择器](https://www.sitepoint.com/regex-matching-attribute-selectors/)，它根据正则表达式匹配属性值。

最近，我有了另一个想法，灵感来自我当时正在做的一个项目——我选择称之为**连续兄弟选择器**。

为了检查它是如何工作的，让我们从一个我们可以参考的 <abbr title="HyperText Markup Language">HTML</abbr> 代码示例开始:

```
<div>
    <h2>First heading</h2>
    <p>First paragraph</p>
    <p>Second paragraph</p>

    <h3>Second heading</h3>
    <p>Third paragraph</p>
</div>
```

<abbr title="Cascading Style Sheets">CSS</abbr> 目前提供了两个兄弟选择器。第一个是[相邻兄弟选择器](https://reference.sitepoint.com/css/adjacentsiblingselector)，它选择指定类型的**第一个兄弟元素**(或其他选择)，它跟在同一父上下文中的第一个元素之后:

```
h2 + p { ... }
```

因此，在我们的 <abbr title="HyperText Markup Language">HTML</abbr> 示例中，它会选择紧跟在第一个标题之后的*第一个*段落，而不是其后的第二个段落，也不是第二个标题之后的第三个段落。

然后是通用同级选择器，它选择同一上下文中第一个元素之后的所有***同级元素****，因此会选择代码示例中的所有三个段落*:**

```
h2 ~ p { ... }
```

在这两个极端之间还有第三种可能——它会选择前两个段落，而不是第三个；换句话说，*连片的*兄妹。

它可能看起来像这样:

```
h2 ~+ p { ... }
```

我们可以用这个组合选择器来表达这种情况:

```
h2 + p, h2 + p + p { ... }
```

但是通常情况下，只有当涉及的元素数量**很少并且已知**时，这才是理想的；如果它非常大——比如十几个或者更多的元素——会怎么样？如果元素的数量是未知的，并且可能达到数百个，那会怎样？你可能会用选择器使这个看起来更紧凑:

```
h2 + p,
h2 + p + p,
h2 + p + p + p,
h2 + p + p + p + p,
h2 + p + p + p + p + p,
h2 + p + p + p + p + p + p,
h2 + p + p + p + p + p + p + p,
h2 + p + p + p + p + p + p + p + p,
h2 + p + p + p + p + p + p + p + p + p,
h2 + p + p + p + p + p + p + p + p + p + p,
h2 + p + p + p + p + p + p + p + p + p + p + p,
h2 + p + p + p + p + p + p + p + p + p + p + p + p { ... }
```

而正是针对这种情况，我产生了这个想法。

我想要一个纯粹的 <abbr title="Cascading Style Sheets">CSS</abbr> 组件，用于技术手册中各部分的显示/隐藏机制。手动标记由一系列二级标题构成，每个标题后面都有一个或多个段落(就像这里看到的代码示例的第一部分)。默认情况下，段落都是隐藏的，所以一开始你看到的只是标题。

您可以通过单击标题来查看每个标题后面的内容，该行为由脚本处理。但是你也可以通过一个包含 hash 的 <abbr title="Uniform Resource Locator">URL</abbr> 进入页面，它匹配一个标题 ID，这样它就直接跳到那个部分，我希望那个部分自动显示；例如，显示(并且只显示)特定标题后面的每个段落。

因为每个部分可能包含几十个段落，所以用相邻的兄弟选择器来定义似乎有些过分——每个段落对应一个可能的段落，就像上面这个庞大的例子一样。当然，一般的兄弟选择器不会做这项工作，因为它会选择所有后面部分中的所有段落，而不仅仅是目标部分。

所以，最终，我用脚本也做到了这一点。

但我不禁想起，如果我有连续的兄弟姐妹选择器，事情会变得多么简单:

```
h2 ~+ p { display:none; }

h2:target ~+ p { display:block; }
```

*缩略图鸣谢:[莫蒂默？](http://www.flickr.com/photos/mortimer/327062027/)** 

## *分享这篇文章*