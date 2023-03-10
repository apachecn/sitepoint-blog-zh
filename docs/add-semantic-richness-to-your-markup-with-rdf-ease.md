# 轻松地为您的标记添加语义丰富性(RDF)

> 原文：<https://www.sitepoint.com/add-semantic-richness-to-your-markup-with-rdf-ease/>

![park-bench](img/98416a382cdfac46fd0fcd21d5d9d949.png "park-bench")
*形象功劳:[保罗·戈耶特](http://www.flickr.com/photos/pgoyette/131302633/)*

虽然每个人都同意语义是有用的，并且一直变得越来越有用，但是很少有人同意如何开发和使用它们。约翰·奥尔索普最近关于 HTML 5 中的语义的文章从实用的角度强调了 HTML 的局限性所固有的一些问题，其中最主要的是缺乏一个*可扩展的*机制来支持未来的语义。John 的文章提出的另一个问题与 Web 本身的语义编码机制有关。

最近，一种向(X)HTML 页面添加语义的新机制受到了一些关注，这种机制被称为<acronym title="Resource Description Framework">RDF</acronym>–<acronym title="Extracted Attributes from Styled Elements">EASE</acronym>(RDF Extracted Attributes from Styled Elements 的缩写)。尽管现在还为时过早，RDF-EASE(可以与 CSS 混合使用)有一些有趣的特性，可以让我们一窥语义网技术的未来。

## 语义离“真实内容”有多远才算太远？

RDF-EASE 决定大量借用 RDFa 和 CSS。结果是文档底层标记的类似 CSS 的语义抽象。在草案规范页面上，RDF-EASE 的十秒推销词是:

CSS 是指定文档外观的外部文件；RDF-EASE 是一个外部文件，它指定了文档*的含义*。

例如，这意味着你可以像这样替换 RDFa 中标记的阅读列表

```
<ul>
    <li typeof="biblio:book"><cite property="dc:title">One Hundred Years of Solitude</cite> by <span property="dc:creator">Gabriel García Márquez</span></li>
    <li typeof="biblio:book"><cite property="dc:title">The Alchemist</cite> by <span property="dc:creator">Paulo Coelho</span></li>
</ul> 
```

使用 [<acronym title="Plain Old Semantic HTML">时髦的</acronym> HTML](http://microformats.org/wiki/posh) (完全没有任何 RDFa)并与 RDF 配对，如下所示:

```
ul li {
    -rdf-typeof: "biblio:book";
}
ul li cite {
    -rdf-property: "dc:title";
}
ul li span {
    -rdf-property: "dc:creator";
} 
```

开发人员或(更有趣的是)自动化抓取者可以使用 XHTML 文档，在其上添加一层 RDF-EASE，为他们想要的任何机器可读目的自动创建 RDF 文档。此外，将来改变本体不需要改变标记。从表面上看，RDF-EASE 比其他形式的语义标记更容易被 web 设计者制作出来，因此它可以作为一种催化剂，将语义引入 Web 的更多领域。

虽然“更多的语义是件好事”没有受到质疑，但 RDF-EASE 确实给我带来了一个概念上的问题:它使文档的语义远离了内容本身。我认为 RDF-EASE 的一些缺点与它“类似 CSS”的事实有关，即它:

*   通过混合表示和语义，潜在地违反了“关注点分离”,因为它允许将语义(以 RDF-EASE 的形式)直接嵌入到表示样式表中。
*   创建了一个额外的间接层，因此可能*进一步*混淆语义问题，并且实际上使语义概念对于新手来说更难理解，而不是更容易理解。
*   不提供与 RDFa 相同的向后兼容性，因为级联规则和语法中微妙的、必要的差异意味着现成的 CSS 解析器并不真正适合 RDF-EASE。

同样，我看到的潜在问题是内容的语义与内容本身的明确分离。微格式和 RDFa 的美妙之处在于它们就在你的内容所在的地方，所以人类和机器都可以从同一个文件的标记中得到他们需要的东西。这降低了 CMS 程序和其他软件的复杂性(从而降低了成本),并且——我认为——降低了人为错误的可能性。

话又说回来，也许将语义从内容的 XML 式标记中分离出来并不是一件大事。RDF-EASE 并不完全是负面的。我认为 RDF-EASE 的一些优点是:

*   更容易被更多的人理解和掌握，这要归功于它更简单的范围和更熟悉的类似 CSS 的语法(通常替代的是 [XSLT](//en.wikipedia.org/wiki/XSLT#XSLT_examples) )。
*   通过将语义级联到元素组上来节省击键，而不是像 RDFa 那样要求将语义属性应用于单个元素。
*   支持微格式和 RDFa 之间更好的兼容性和更容易的转换(通过提供一个`nearest-ancestor()`“作用域机制”)。

双方都有争论，这些天我们把语义放在我们能放的任何地方，包括元素(像 [HTML5 提出的`header`、`section`和`aside`](/2008/11/25/w3c-markup-validation-service-adds-experimental-html5-support/) )、属性(像`rel`和`rev`)和属性值(像`abbr`元素中的 [`title`)。因此，考虑到对未来可扩展性的明显需求，问题就变成了这些语义的目的是否足够明确，从而保证它们有自己的接口。语义似乎已经进入了所有这些技术机制，现在甚至是“语义样式表”，这是好事还是坏事？老实说，我认为还没有人知道。](/2008/06/25/bbc-rejects-hcalendar-microformat-because-of-accessibility-concerns/)

## RDF-EASE 能成为我们需要的“个性化本体论”的催化剂吗？

话虽如此，我想知道 RDF-EASE 及其语义抽象的最大用例是否在一个最意想不到的地方借鉴了 CSS:用户样式表。

CSS 的承诺之一是访问者对网页内容的个性化。可悲的是，这还没有成为普遍现象。尽管如此，[用户样式表可以给网站带来惊人的变化，并且是一个非常强大的个性化网页外观的机制。](/2007/05/28/user-style-sheets-come-of-age/)

那么，RDF-EASE 最大的用例可能是在*个性化本体*的创建和分发中。RDF-EASE 可以——理论上——给你一种方式来*个性化站点内容的含义*,类似于用户样式表让你个性化站点外观的方式，而不是被迫依赖文档作者的语义。如果工具构建得足够灵活，允许即插即用的词汇和本体，也许您甚至可以使用 RDF-EASE 来个性化站点的行为方式。

起初，这听起来可能不像是勾选偏好框，但随着我们进一步进入一个从根本上依赖于文档语义的自动化工具时代，让用户更容易定义自己的语义无疑将证明对言论自由和自我表达等原则的重要性，正如知识共享运动对今天的“重新混合”和开源倡议对软件行业的重要性一样。

不喜欢你的关系被定义为“很复杂”，或者你在成千上万的网站上被问及性别，而你唯一的选择是“男性”或“女性”？也许如果这些网站使用语义本体论作为数据的模型，你就能够编写自己的 RDF-EASE 转换，用你提供的选项覆盖网站的选项。此外，由于你的选择会与其他本体联系起来，你的工具会突然以对你更有意义的方式与你互动，*而不需要开发者事先意识到或同意你这样做*。

这就是我所说的个性化网络。

## 分享这篇文章