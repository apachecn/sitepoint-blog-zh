# 下一代 CSS 选择器:第 4 级

> 原文：<https://www.sitepoint.com/future-generation-css-selectors-level-4/>

早在 2014 年 1 月，我就写过文章[当代 CSS3 选择器](https://www.sitepoint.com/current-generation-css3-selectors/)。那篇文章的目标是介绍通常属于“CSS3”范畴的新一代选择器。Thabet 选择器组在很多地方都有很好的文档，浏览器对这些特性的支持也非常强大(包括 IE9+在内的所有浏览器)。

CSS 选择器的未来看起来也很光明，[选择器 4 级规范](https://www.w3.org/TR/selectors4/)目前处于工作草案状态，同一规范的[编辑草案仍在进行中(编辑草案通常被视为更权威)。](http://dev.w3.org/csswg/selectors/)

本文将关注我上一篇文章中没有讨论的新选择器。浏览器对这些的支持很差，所以我不建议在生产中使用这些。请将这篇文章看作是对规范进一步发展和浏览器开始实现时将会发生什么的一个窥视。我已经为那些有支持的人包括演示。

## `:read-only`和`:read-write`

这些选择器非常简单。用户可编辑的任何元素都处于“读写”状态。否则，元素处于“只读”状态。

以下面的 HTML 为例:

```
<input type="text" readonly>
<input type="text" disabled>
<input type="text">
<div contenteditable></div>
```

现在考虑这个 CSS:

```
:read-only {
  outline: solid 1px blue;
}

:read-write {
  outline: solid 1px red;
}
```

下面是这个 CSS 与 HTML 相关的功能的分类:

*   前两个元素将有蓝色轮廓，因为它们在 HTML 中分别被设置为“readonly”和“disabled”。
*   第三个元素将有一个红色的轮廓，因为它是自然可编辑的(“读写”)，默认情况下所有输入都是如此。一个`textarea`会是一样的。
*   由于`contenteditable`属性，最后一个元素(`div`)将有一个红色的轮廓。

在 CSS 中，我普遍使用这些选择器(也就是说，不将它们应用到任何元素)。这意味着红色轮廓将应用于所有 div、spans 和其他自然不可编辑的元素。更具体地说，这更可能用于特定的表单元素或应用了类的元素。

在编辑的草稿中，`:read-write`伪类被列为“处于危险中”,因此它可能会被删除。

**浏览器支持:只读和:读写**
Chrome、Opera、Firefox、Safari。

注意:如下面的演示所示，支持这些选择器的浏览器将“禁用”输入标识为“读写”，根据规范，这是不正确的。

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )进行只读和读写见笔[演示。](http://codepen.io/SitePoint/pen/LVLZQO/)