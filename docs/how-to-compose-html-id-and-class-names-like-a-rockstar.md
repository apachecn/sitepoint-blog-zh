# 如何像摇滚明星一样编写 HTML ID 和类名

> 原文：<https://www.sitepoint.com/how-to-compose-html-id-and-class-names-like-a-rockstar/>

如您所知，`id`和`class`值都可以用来为前端代码中的表示(CSS)和行为(JavaScript)效果提供实现挂钩。当然，它们也会影响语义；[微格式是一种设计模式](https://www.sitepoint.com/article/microformats-meaning-markup)，它以一种特定的方式大量使用了`class`属性。但是你知道这两个核心属性还能做什么吗，我认为*应该用来做什么？*

我认为，经过深思熟虑，`id`和`class`属性还可以改善我们代码的组织性、可读性，并且可以直接改善[开发人员之间交流的许多方面，这对于大型团队](https://www.sitepoint.com/how-semantic-markup-helps-server-side-developers-write-reusable-code/)来说尤其重要。事实上，微格式的成功部分来自于创建深思熟虑的约定，即何时以及为什么使用哪些`class`名称。

## ID 和类名注意事项

当组合`id`和`class`属性的值时，我问自己以下问题，以便帮助我找到一个好的(可重复的)名称:

1.  元素有什么作用？(为什么会在那里？它的目的是什么？)
2.  是否有其他元素具有类似的功能？(有多相似？它们之间有什么区别？)
3.  有没有其他网站也使用元素来达到同样的目的？(那些叫什么？)

回答第一个问题有助于我通过想出一个与代码的功能和目的相匹配的名字来生成[自文档化代码](http://en.wikipedia.org/wiki/Self-documenting)。例如，像这样的代码一眼就能看出它是一个导航列表，因此在其名称中引用“导航”显然是有意义的。

```
<ul>
    <li><a href="/">Home</a></li>
    <li><a href="/about/">About</a></li>
    ...
</ul>
```

回答第二个问题有助于阐明重用的机会。例如，除非整个代码库中只有一个导航元素，否则仅通过功能(“导航”)来识别上述代码是不可能被充分区分的。因此，它可能会被命名为“主导航”、“站点导航”、“导航栏”、“全局导航”或这些名称的组合，而另一个导航元素则被命名为其他名称之一。为了这个例子，让我们使用“站点”名称:

```
<ul id="sitenavigation">
    <li><a href="/">Home</a></li>
    <li><a href="/about/">About</a></li>
    ...
</ul>
```

回答第三个问题有助于代码在技术上的互操作(就像使用微格式一样)，但也有助于保持命名方案本身的清晰和简单。“Nav”是一个常见的缩写，甚至是一个一级的 HTML5 元素，你今天可以使用它。通过从现有工作中获取灵感，你减少了制定和维护自己的惯例所需的认知努力。

```
<ul id="sitenav">
    <li><a href="/">Home</a></li>
    <li><a href="/about/">About</a></li>
    ...
</ul>
```

## 命名约定的重要性

命名约定只是开发人员之间的一种协议，即使用同一种名称来指代相同或相似的事物，而使用不同的模式来指代其他事物。这类协议简单得令人难以置信，但却非常有效。例如，我敢打赌你能读到下面这句话，因为英文字母遵循广泛认同的规则:我真的很爱看！

惯例使任何熟悉其一般原则的人能够补充细节，即使那些细节丢失了。人类基于惯例和先验知识推断信息的天生能力不仅仅在填空时有用。同样的技术也可以用来表达目的和意图。

**对于大多数网站通用的元素，比如全球导航列表和搜索栏，我使用``TitleCase``名称。然而，对于特定于一个站点的元素，我使用``lower-case-dashed``名称。**我将命名约定作为一种微语法来使用，以暗示关于元素用途的“通用”(又名“全局”)和“特定”(又名“本地”)范围，这种区别可以帮助开发人员在代码库的宏观视图中定位。

按照这个惯例，`sitenav`变成了`SiteNav`:

```
<ul id="SiteNav">
    <li><a href="/">Home</a></li>
    <li><a href="/about/">About</a></li>
    ...
</ul>
```

注意，``TitleCase``与``lower-case-dashed``名称的使用与它们是否出现在 CSS `class`或`id`属性值中无关。下面是我在搜索表单中标记文本输入字段的方法:

```
<input type="text" class="SearchField" id="widgetco-search" title="Find a Widget" value="" />
```

我们知道“搜索字段”是一个通用的概念，所以根据我的习惯，它得到了``TitleCase`` d。同样(明显)的令牌，Widget，Co .的特定搜索字段在其他网站上不存在，所以它得到了一个``lower-case-dashed``名称。在我的[关于漂亮标记的 5 个顶级技巧](https://www.sitepoint.com/5-top-tips-to-beautify-your-html-and-enrich-your-content/)中有*始终如一地使用标记模式*；将这个概念扩展到`id`和`class`值是一种自然的进化。

## 从惯例到共同准则

最终，你选择的命名约定没有坚持它重要。然而，显而易见的约定更容易快速掌握，所以测试你的名字的一个方法是试着像说英语一样大声说出你的代码。你选择的名字的语法听起来有意义吗，还是听起来很别扭？

考虑表单字段。在英语中，它可能是这样的:"**输入的**`widgetco-search``text`**是一个** `SearchField`"。它让你`Find a Widget`这在语法上是有意义的，并且`class`属性的[“是”关系](http://en.wikipedia.org/wiki/Is-a)，以及`id`属性的“the”指定都是清楚的。

更重要的是，你已经知道带有`Skyscraper`的`class`的元素可能会使用相对广泛和标准的表示和行为(事实上，[摩天大楼是一种 <acronym title="Interactive Advertising Bureau">IAB</acronym> 标准网页广告格式](http://www.iab.net/iab_products_and_industry_services/1421/1443/1452)，而带有`wonderful-sale`的`class`的元素可能不会。因为您立即知道了这一点，所以您可以更加自信地以一种有机的方式将您的定制代码与可重用的可重用组件隔离开来。

## 分享这篇文章