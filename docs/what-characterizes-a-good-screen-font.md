# 好的屏幕字体有什么特征？

> 原文：<https://www.sitepoint.com/what-characterizes-a-good-screen-font/>

我将以一个挑衅性的声明开始:我相信 CSS 是我们所拥有的最难掌握的计算机语言之一。它没有复杂的语法，你当然不需要一个 <acronym title="Information Technology">IT</acronym> 的博士学位来理解它。然而，它是唯一一种非“逻辑”的流行语言——我指的是最字面的意思。

不像其他熟悉的 web 开发语言，如 JavaScript、PHP 甚至是 SQL，问题不是通过通用逻辑解决的。像“if X then do Y，otherwise do Z”或“select all of Y then do X with them”这样的口头算法不能翻译成像 <acronym title="Cascading Style Sheets">CSS</acronym> 这样的语言。简单来说；这是一种风格语言。一种面向*设计师*而非开发者的语言。正是因为这个原因，一些和我一起工作过的最有经验的程序员很难理解 CSS。

级联是对 <acronym title="Cascading Style Sheets">CSS</acronym> 背后语法的隐喻术语。它通过使用特殊的字形(称为*组合子*)来计算元素，如起源、特异性、顺序和重要性，以目标元素(和伪元素)为目标，并相应地对它们进行样式化。没有任何一篇文章能够做到 CSS 语法应有的公正——这就是 W3 规范和丹的作用。然而，假设你已经对 <acronym title="Cascading Style Sheets">CSS</acronym> 略知一二，让我们检查一些鲜为人知的组合子，不是*如何*，而是*何时*使用它们。

在设计学校，我们都被告知类和 id，分别使用`.`和`#`来直接定位元素。这对于构建一个功能性网站来说已经足够了，但是对于处理一个完整的设计转变来说还不够灵活。通过在标记中使用*表示性的*值，它也创造了比需要更多的工作。让我们来看看另一种针对那些难以获得的元素的方法。

## 相邻兄弟组合子

我们将用一个在微妙情况下很好使用的选择器来开始—使用相邻的兄弟组合符。相邻的兄弟组合符通过用符号`+`连接两个元素来表示:

```
h1 + p
```

这将选择直接出现在 DOM 中一个`h1`元素之后的所有`p`元素。排印理论认为，我们应该在正文中缩进段落，但前提是它们必须在另一个段落之后。这个选择器的一个实际用途是将`text-indent`值添加到段落中，而不是针对树中的第一个，就像这样；

```
p + p {
    text-indent: 1em;
    }
```

这总比用`text-indent`设计所有段落，然后随时用`class="first"`将第一个段落归零要好。三行，没有类和坚实的浏览器支持。如果你在你的`p`标签中嵌套了你的内容级`img`标签(你应该这样做)，那么我们可以简单地用负值`-1em`将它们的左边距拉回:

```
p + p img {
    margin-left: -1em;
    }
```

很简单，对吧？如果我们想在不影响任何其他段落的情况下，对直接跟随标题的所有段落的第一行进行样式化，会怎么样呢？同样，我们可以避免使用表示类来做到这一点。一个简单的选择器由相邻的兄弟组合子和一个[伪元素](https://www.w3.org/TR/css3-selectors/#pseudo-elements)组成:

```
h1 + p::first-line {
    font-variant: small-caps;
    }
```

**注意:**虽然`:first-line`是一个 <acronym title="Cascading Style Sheets">CSS</acronym> 2.1 批准的伪元素，但是为了在伪类和伪元素之间建立区别，在 <acronym title="Cascading Style Sheets">CSS</acronym> 第 3 级引入了`::`符号。

## 子组合子

一种常见的标记协议是将顶级部分包装在一个名为 something 的元素中，类似于`#page`或`#wrap`:

```
<div id="page">
    <header></header>

    <article>
        <section id="main"></section>
        <aside></aside>
    </article>
    <footer></footer>

</div>
```

不管你使用的是 <acronym title="HyperText Markup Language">HTML</acronym> 5 还是 <acronym title="eXtensible HyperText Markup Language">XHTML</acronym> 1.1 语法，这种基本格式对你来说应该很熟悉。如果你运行 960 像素的固定宽度，将文档居中对齐，每个元素水平填充包装器，你的 <acronym title="Cascading Style Sheets">CSS</acronym> 可能类似于:

```
#page {
    width: 960px;
    margin: 0 auto;
    }

header,
article,
footer { width: 100%; }
```

或者，您可能会更具体一点，在`#page`父项前面加上前缀，以避免在选择范围之外遇到它们:

```
#page header,
#page article,
#page footer { width: 100%; }
```

有更好的方法。我们都见过[通用元素选择器](https://www.w3.org/TR/css3-selectors/#universal-selector)；`*`，可能通过浏览器重置或类似方式。当我们将它与子选择器结合使用时，我们可以将所有元素作为`#page`的直接后代，而不会影响它们的孙辈或更远的后代:

```
#page > * { width: 100%; }
```

如果我们想在顶层结构中添加或删除元素，这将使我们的文档经得起未来的考验。回到我们最初的标记方案，这将触及`header`、`article`和`footer`元素，而不触及`article`内的`#main`和`aside`。

## 字符串和子字符串属性选择器

[属性选择器](https://www.w3.org/TR/css3-selectors/#attribute-selectors)是我们拥有的最强大的属性选择器之一。自 <acronym title="Cascading Style Sheets">CSS</acronym> 2.1 以来，它们也在*出现了一定程度的*，并且通常以`input[type="text"]`或`a[href="#top"]`的形式出现。然而， <acronym title="Cascading Style Sheets">CSS</acronym> 3 以[字符串和子字符串](https://www.w3.org/TR/css3-selectors/#attribute-substrings)的形式引入了更深层次的控制。

**注意:**到目前为止，我们讨论的一切都是 <acronym title="Cascading Style Sheets">CSS</acronym> 2.1 标准，但我们现在正步入 <acronym title="Cascading Style Sheets">CSS</acronym> 3 的领域。我们将把它留在表示层，所以现在使用这些是可以的。

我们有四个主要的属性字符串选择器，其中‘v’=值，而‘a’=属性:

*   *v* 是空格分隔的*值* : `element[a~="v"]`列表中的一个
*   *a* 恰好以 *v* : `element[a^="v"]`开始
*   *a* 恰好以 *v* : `element[a$="v"]`结尾
*   *一个*包含*值* : `element[a*="v"]`

属性字符串选择器的潜力几乎是无穷无尽的，但一个完美的例子是图标。也许你有一个无序的社交媒体档案链接列表:

```
<ul id="social">
    <li><a href="http://facebook.com/designfestival">Like on Facebook</a></li>
    <li><a href="http://twitter.com/designfestival">Follow on Twitter</a></li>

    <li><a href="http://feeds.feedburner.com/designfestival">RSS</a></li>
</ul>
```

对它们进行样式化就像通过它们的`href`属性运行子串查询来查找关键字一样简单。我们可以接着 *[逐步增强](http://en.wikipedia.org/wiki/progressive_enhancement)* 这些环节，就像这样:

```
#social li a::before {
    content: '';
    background: left 50% no-repeat;
    width: 16px;
    height: 16px;
    }

#social li a[href*="facebook"]::before {
    background-image: url(images/icon-facebook.png);
    }

#social li a[href*="twitter"]::before {
    background-image: url(images/icon-twitter.png);
    }

#social li a[href*="feedburner"]::before {
    background-image: url(images/icon-feedburner.png);
    }
```

类似地，我们可以使用后缀子字符串选择器将所有链接指向 PDF 文档:

```
a[href$=".pdf"]::before {
    background-image: url(images/icon-pdf.png);
    }
```

不支持 <acronym title="Cascading Style Sheets">CSS</acronym> 3 属性子字符串的浏览器不会显示这些图标，但这没关系——它们对功能来说并不重要，它们只是一个“好东西”。

## 结构伪类

最后，我想概述一下结构化伪类的好处，不要与[伪元素](https://www.w3.org/TR/css3-selectors/#pseudo-elements)或[链接和状态伪类](https://www.w3.org/TR/css3-selectors/#pseudo-classes)混淆。我们可以使用这些方法根据元素在 DOM 中的位置而不是内容来定位元素。当使用一个结构化伪类时，一个很好的*的例子可以是以元素树中的第一个(或最后一个)元素为目标，或者在奇数和偶数元素之间交替:*

```
<ul>

    <li>List Item 1</li>
    <li>List Item 2</li>
    <li>List Item 3</li>
    <li>List Item 4</li>

    <li>List Item 5</li>
    <li>List Item 6</li>
</ul>

ul li { border-top: 1px solid #DDD; }
ul li:last-child { border-bottom: 1px solid #DDD; }
ul li:nth-child(even) { background: #EEE; }
```

**注意:** `:first-child`是 <acronym title="Cascading Style Sheets">CSS</acronym> 2.1 规范中唯一可用的伪元素。其他所有伪元素，包括`:last-child`，都是 <acronym title="Cascading Style Sheets">CSS</acronym> 3 标准。

然而，结构化伪元素的关键在于什么时候*而不是*使用它们。它们应该严格地保留在选择器与元素的*位置*相关而不是与元素内容相关的时候。如果不管元素在 DOM 中的位置如何，都必须以某种方式对其进行样式化，这时就应该使用更有意义的、*语义*选择器，比如类、ID 或字符串。

## 摘要

今天，您可能已经在使用这些组合子和选择器中的一部分或全部——也许是以正确的方式，也许不是——但是提醒您什么时候可以使用它们，而不是将类或 ID 应用于元素，这并没有什么坏处。因为这是我们中最优秀的人也会犯的错误。

## 分享这篇文章