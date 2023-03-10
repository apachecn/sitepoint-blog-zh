# CSS 表格中的行跨度和列跨度

> 原文：<https://www.sitepoint.com/rowspans-colspans-in-css-tables/>

“…我们已经实现了 CSS 2.1 中的每一个特性，并且在我们发布的时候，离我们完全支持 CSS 2.1 规范的目标越来越近了。”

如果你要猜测谁最近发表了这样的声明，你会被原谅认为它来自 Opera、Safari 或 Firefox 团队；他们似乎一直是最注重标准的浏览器供应商。事实上，这句话来自微软的道格·斯坦普(Doug Stamper)，[关于 Internet Explorer 8](http://blogs.msdn.com/ie/archive/2008/09/08/internet-explorer-8-beta-2-platform-improvements.aspx) 。

看起来 web 设计者一直要求的事情——在所有主流浏览器上对 CSS2.1 的成熟支持——实际上即将发生。早在《科技时报》第 185 期，我在《基于表格的布局是下一个大事件 中写道这对网页设计师意味着什么。简而言之，我说过 CSS 表格会成为 CSS 页面布局的最佳工具。

对那篇文章有不同的反应，尤其是在行和列的跨度上。HTML 表格允许您创建跨越多行或多列的单元格，但 CSS 表格不提供同样的自由。

嗯，在我为一本尚未公开的、可能会引起争议的关于 CSS 的书所做的研究中，我已经弄清楚了如何在 CSS 表格中模拟行和列的跨度。与其让你等着看这本书，我想我应该马上向你展示这个有用的技巧！

## 我袖子里什么都没有…

如果你有使用 HTML 表格构建布局的经验，你会熟悉`td`元素的`colspan`和`rowspan`属性的用法。这些属性为简单的表格提供了复杂的可能性，使单元格能够跨越列和行。

CSS 表格没有任何行或列跨越的概念，这使得使用一个单一的布局结构比使用表格更困难。然而，通过使用嵌套的 CSS 表格可以实现类似的布局。

当然，嵌套表并不是完美的解决方案。例如，当您想要匹配嵌套表格中单元格的尺寸时，事情可能会变得很混乱，并且额外的`<div>`标记确实开始增加。

事实证明，在许多情况下，还可以使用表格单元格的绝对位置来模拟行和列的跨越。在本例中，我们将使表格第一行的第二个单元格跨越表格的两行(就好像它有一个`2`的`rowspan`)。首先，让我们看看 HTML 代码:

```
<div class="tablewrapper">
  <div class="table">
    <div class="row">
      <div class="cell">
        Top left
      </div>
      <div class="rowspanned cell">
        Center
      </div>
      <div class="cell">
        Top right
      </div>
    </div>
    <div class="row">
      <div class="cell">
        Bottom left
      </div>
      <div class="empty cell"></div>
      <div class="cell">
        Bottom right
      </div>
    </div>
  </div>
</div>
```

您会注意到我们已经用一个额外的`div`和一个`"tablewrapper"`的`class`包装了我们的表格`div`。需要这个额外的`div`来提供 CSS 定位上下文——我们通过给它相对定位来创建它:

```
.tablewrapper {
  position: relative;
}
```

根据 CSS 规范，我们应该能够简单地对表`div`应用相对定位，但是当前的浏览器似乎不支持这一点。

## 灵巧的手法和绝对的定位

现在，我们可以用绝对定位来控制`div`与`class` `"rowspanned cell"`的大小和位置:

```
.cell.rowspanned {
  position: absolute;
  top: 0;
  bottom: 0;
  width: 100px;
}
```

当`top`和`bottom`属性都设置为零时，单元格将拉伸以填充表格的整个高度，模拟一个行跨度。根据布局的需要，您可以对`top`和`bottom`使用不同的值，甚至直接设置单元格的`height`来实现其他的跨行布局。

您还需要指定单元格的宽度。通常，最简单的方法就是设置它的`width`属性，但是根据你对周围表格单元格尺寸的了解，你也可以通过设置`left`和`right`来实现。

因为被定位的单元格实际上并没有*跨越表格的多行，所以表格中的每一行都必须包含一个相应的单元格。不过，这些单元格只是空的占位符；注意上面 HTML 代码中带有`class` `"empty cell"`的`div`。该单元格的功能是保持“spanned”单元格将占据的空间开放，因此我们必须确保其宽度与我们为`"rowspanned cell"`指定的宽度相匹配:*

```
.cell.empty {
  width: 100px;
}
```

这就是全部了！为了完成这个例子的样式表，我们只需要设置适当的`display`属性值，并添加一些边框，这样我们就可以看到发生了什么:

```
.tablewrapper {
  position: relative;
}
.table {
  display: table;
}
.row {
  display: table-row;
}
.cell {
  border: 1px solid red;
  display: table-cell;
}
.cell.empty
{
  border: none;
  width: 100px;
}
.cell.rowspanned {
  position: absolute;
  top: 0;
  bottom: 0;
  width: 100px;
}
```

本质上，通过使用绝对定位，我们是在告诉浏览器，“让我来处理这个表格单元格的布局，剩下的就交给你了。”结果如下所示:

![](img/812f683d2efd4e21f638ef6a5f52fd01.png)

你自己试试。此示例适用于除 Internet Explorer 7 之外的所有主流浏览器，也适用于当前的 IE8 Beta 2 版本。

你怎么想呢?当您的 Internet Explorer 用户转向 IE8 时，您能想象自己切换到 CSS 表格进行布局吗？

## 分享这篇文章