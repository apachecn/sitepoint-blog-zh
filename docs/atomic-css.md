# 了解 CSS 架构:原子 CSS

> 原文：<https://www.sitepoint.com/atomic-css/>

下面是我们的书《CSS 大师》的摘录，作者是蒂芙尼·布朗。世界各地的商店都有出售，或者你可以在这里买到电子书。

如果 BEM 是业界宠儿，Atomic CSS 就是它叛逆的特立独行者。雅虎的 Thierry Koblentz 在他 2013 年的文章[挑战 CSS 最佳实践](http://www.smashingmagazine.com/2013/10/21/challenging-css-best-practices-atomic-approach/)中对原子 CSS 进行了命名和解释，原子 CSS 使用了一个紧密的类名库。这些类名通常是缩写的，并且与它们影响的内容无关。在原子 CSS 系统中，你可以知道类名是做什么的；但是类名——至少不是样式表中使用的那些——和内容类型之间没有关系。

让我们用一个例子来说明。下面是我们称之为传统 CSS 架构的一组规则。这些规则集使用描述其应用内容的类名:全局消息框，以及“成功”、“警告”和“错误”消息框的样式:

```
.msg {
  background-color: #a6d5fa;
  border: 2px solid #2196f3;
  border-radius: 10px;
  font-family: sans-serif;
  padding: 10px;
}
.msg-success {
  background-color: #aedbaf;
  border: 2px solid #4caf50;
}
.msg-warning {
  background-color: #ffe8a5;
  border-color:  #ffc107;
}
.msg-error {
  background-color: #faaaa4;
  border-color: #f44336;
}
```

要创建一个错误消息框，我们需要将`msg`和`msg-error`类名添加到元素的`class`属性中:

```
<p class="msg msg-error">An error occurred.</p>
```

让我们对比一下原子系统，在原子系统中，每个声明都成为自己的类:

```
.bg-a {
  background-color: #a6d5fa;
}
.bg-b {
  background-color: #aedbaf;
}
.bg-c {
  background-color: #ffe8a5;
}
.bg-d {
  background-color: #faaaa4;
}
.bc-a{
  border-color: #2196f3;
}
.bc-b {
  border-color: #4caf50;
}
.bc-c {
  border-color:  #ffc107;
}
.bc-d {
  border-color:  #f44336;
}
.br-1x {
  border-radius: 10px;
}
.bw-2x {
  border-width: 2px;
}
.bss {
  border-style: solid;
}
.sans {
  font-style: sans-serif;
}
.p-1x {
  padding: 10px;
}
```

那就多了很多 CSS。现在让我们重新创建我们的错误消息组件。使用原子 CSS，我们的标记变成:

```
<p class="bw-2 bss p-1x sans br-1x bg-d bc-d">
    An error occurred.
</p>
```

我们的标记也更加冗长。但是当我们创建一个警告消息组件时会发生什么呢？

```
<p class="bw-2 bss p-1x sans br-1x bg-c bc-c">
    Warning: The price for that item has changed.
</p>
```

更改了两个类名:`bg-d`和`bc-d`被替换为`bg-c`和`bc-c`。我们重用了五个规则集。现在，让我们创建一个按钮:

```
<button type="button" class="p-1x sans bg-a br-1x">Save</button>
```

嘿现在！这里我们重用了四个规则集，并避免向样式表中添加更多的规则。在一个健壮的原子 CSS 架构中，添加一个新的 HTML 组件，比如一个文章侧边栏，不需要添加更多的 CSS(尽管，实际上，可能需要添加更多)。原子 CSS 有点像在你的 CSS 中使用实用类，但是更极端。具体来说，它:

*   通过创建高度精细、高度可重用的样式，而不是为每个组件设置一个规则集，来保持 CSS 的整洁
*   通过使用低特异性选择器系统，大大减少了特异性冲突
*   一旦定义了初始规则集，就可以快速开发 HTML 组件

然而，原子 CSS 并非没有争议。

### 反对原子 CSS 的理由

原子 CSS 几乎违背了我们在编写 CSS 时所学的一切。感觉几乎和到处贴`style`属性一样错误。事实上，这是对原子 CSS 方法的主要批评之一:它模糊了内容和表示之间的界限。如果`class="fl m-1x"`将一个元素向左浮动并增加了 10 个像素的边距，当我们不再希望该元素向左浮动时，我们该怎么办？

当然，一个答案是从我们的元素中移除`fl`类。但是现在我们正在改变 HTML。使用 CSS 的全部原因是为了使标记不受表示的影响，反之亦然。(我们也可以通过从样式表中删除`.fl {float: left;}`规则来解决这个问题，尽管这会影响每个类名为`fl`的元素)。尽管如此，更新 HTML 可能是微调 CSS 的一个小代价。

在科布伦茨的原始帖子中，他使用了类名，如用`.M-10`代表`margin: 10px`，用`.P-10`代表`padding: 10px`。这种命名约定的问题应该是显而易见的。将边距改为 5 或 20 像素意味着我们需要更新我们的 CSS *和*我们的 HTML，否则会有类名无法准确描述其效果的风险。

使用类名如`p-1x`，如本节所做的，解决了这个问题。类名的`1x`部分表示一个比率，而不是定义的像素数。如果基础填充是五个像素(即`.p-1x {padding: 5px;}`，那么`.p-2x`将设置十个像素的填充。是的，这不太能描述类名的作用，但这也意味着我们可以在不更新 HTML 或者不创建误导性类名的情况下更改 CSS。

原子 CSS 架构不会阻止我们在标记中使用描述内容*的类名。您仍然可以将`.button-close`或`.accordion-trigger`添加到代码中。这样的类名更适合 JavaScript 和 DOM 操作。*

### BEM 与原子 CSS

当有大量开发人员并行构建 CSS 和 HTML 模块时，BEM 的效果最好。它有助于防止大型团队产生的错误和缺陷。它的伸缩性很好，部分原因是命名约定是描述性的和可预测的。BEM 不是大型团队的唯一选择；但是对于大型团队来说,*真的很有效。*

当有一个小团队或单个工程师负责开发一组 CSS 规则，由一个更大的团队构建完整的 HTML 组件时，原子 CSS 会工作得更好。有了原子 CSS，开发人员只需查看样式指南或 CSS 源代码，就可以确定特定模块需要哪组类名。

## 分享这篇文章