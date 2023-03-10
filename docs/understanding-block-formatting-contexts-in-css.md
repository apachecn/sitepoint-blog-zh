# 了解 CSS 中的块格式上下文

> 原文：<https://www.sitepoint.com/understanding-block-formatting-contexts-in-css/>

一个[块格式化上下文](https://www.w3.org/TR/CSS21/visuren.html#block-formatting)是网页的可视 CSS 渲染的一部分，其中块框被布局。其所属的[定位方案](https://www.w3.org/TR/CSS2/visuren.html#positioning-scheme)为[正常流程](https://www.w3.org/TR/CSS21/visuren.html#normal-flow)。根据 [W3C](https://www.w3.org/TR/CSS2/visuren.html#block-formatting) :

> 浮动、绝对定位元素、内联块、表格单元格、表格标题和具有“溢出”而非“可见”的元素(除非该值已传播到视口)建立新的块格式上下文。

上面的引用很好地总结了块格式上下文是如何形成的。但是让我们用一种更容易理解的方式重新定义它。块格式上下文是至少满足下列条件之一的 HTML 框:

*   `float`的值不是`none`
*   `position`的值既不是`static`也不是`relative`
*   `display`的值为`table-cell`、`table-caption`、`inline-block`、`flex`或`inline-flex`
*   `overflow`的值不是`visible`。

## 创建块格式上下文

可以显式触发块格式化上下文。因此，如果我们想创建一个新的块格式上下文，我们只需要添加上面提到的任何一个 CSS 条件。

例如，看看下面的 HTML:

```
<div class="container">
  Some Content here
</div>
```

通过向容器中添加任何一个必要的 CSS 条件，如`overflow: scroll`、`overflow: hidden`、`display: flex`、`float: left`或`display: table`，可以创建一个新的块格式化上下文。尽管上述任何条件都可以创建块格式化上下文，但还会有一些其他影响，如:

*   可能会在响应性方面产生问题
*   可能会显示不想要的滚动条
*   将元素推到左边，其他元素环绕在它周围
*   `overflow: hidden`将溢出的元素剪辑

因此，每当我们创建一个新的块格式上下文时，我们都会根据自己的要求选择最佳条件。为了统一，我在本文给出的所有例子中都使用了`overflow: hidden`。

```
.container {
  overflow: hidden;
}
```

除了`overflow: hidden`之外，你可以随意玩其他的声明。

## 块格式上下文中的方框对齐方式

W3C 规范声明:

> 在块格式上下文中，每个框的左外边缘接触包含块的左边缘(对于从右到左的格式，右边缘接触)。即使在存在浮动的情况下也是如此(尽管盒子的*行盒子*可能会由于浮动而收缩)，除非盒子建立了新的块格式化上下文(在这种情况下，盒子本身 [*可能会由于浮动而*变得更窄](https://www.w3.org/TR/CSS21/visuren.html#bfc-next-to-float))。

![alignment](img/610a6b1d56911314a4092e886f19d23a.png)

简而言之，正如我们在上面的图表中看到的，属于块格式上下文的所有框都是左对齐的(对于从左到右的格式)，并且它们的左外边缘接触包含块的左边缘。在最后一个框中，我们可以看到，即使左边有一个浮动元素(棕色)，另一个元素(绿色)仍然接触到包含块的左边界。关于为什么会发生这种情况的原理将在下面的文本换行部分讨论。

## 块格式上下文会导致页边距折叠

在正常流程中，箱子从包含块的顶部开始一个接一个地垂直放置。两个同级框之间的垂直距离由两个同级框各自的边距决定，但不是两个边距的总和。

为了理解这一点，让我们考虑一个例子。

![collapsing margins](img/4258b7f8db7524d9a7758b30733ff29b.png)

在上图中，我们考虑已经创建的块格式化上下文，其中红框(a `div`)包含两个绿色兄弟(`p`元素)。

```
<div class="container">
  <p>Sibling 1</p>
  <p>Sibling 2</p>
</div>
```

而对应的 CSS 是:

```
.container {
  background-color: red;
  overflow: hidden; /* creates a block formatting context */
}

p {
  background-color: lightgreen;
  margin: 10px 0;
}
```

理想情况下，两个兄弟之间的边距应该是两个元素的边距之和(20px)，但实际上是 10px。这被称为[折叠边距](https://www.sitepoint.com/web-foundations/collapsing-margins/)。如果同级的边距不同，则以较高的边距为准。

通过[码笔](http://codepen.io)上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [OVzrer](http://codepen.io/SitePoint/pen/OVzrer/) 。