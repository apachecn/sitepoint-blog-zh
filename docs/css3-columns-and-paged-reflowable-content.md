# CSS3 列和分页可回流内容

> 原文：<https://www.sitepoint.com/css3-columns-and-paged-reflowable-content/>

当涉及到数字形式的书面文字的消费时，传统书籍的爱好者的主要抱怨仍然是不能利用数字书籍来建立翻页的规定、纸张及其颜色的选择、字体以及书籍特有的其他物理属性帮助创建的联系。

虽然以数字形式重现整个体验的想法——包括新翻开的书籍从其一尘不染、未经触摸的印刷页面中飘出的气味——在重现过去时可能仍然是完全不切实际、完全无利可图的做法，但现在可以用最少的努力来重现这种体验的一个方面，这一切都归功于 CSS3 专栏模块背后的大脑。

## 在 CSS3 列之前

尽管 columns 模块的引入(目前仍是候选推荐标准)使得重建传统书籍阅读体验的某些方面变得更加容易，但依靠 JavaScript 魔法或复杂的服务器端逻辑已经可以实现。谷歌 Chrome 团队的 20 件我学到的关于浏览器和网络的事情提供了一个非常吸引人的证据，证明了重建传统阅读体验的一些元素的概念，尽管这种方式严重依赖于 HTML 和 CSS 逻辑。

在不依赖 CSS3 columns 模块的情况下，实施者很可能需要生成特定于每个用户的操作环境的样式信息，并且这样做的决定将完全取决于他们是否愿意创建适合所有大小的设备的阅读体验。然而，那是过去，这当然是现在。

## 新时代的开始

好吧，在我进入新时代之前，在这个时代，数字内容在页面中的呈现不需要掌握 JavaScript 魔法或一些服务器端 PHP 知识，门外汉可能会喜欢一本关于 CSS3 专栏的入门书。

### CSS3 列

不像 CSS3 的一些更受关注和吸引人的特性，如过渡和动画，它们的基本思想可能需要一些专注的研究，使用 CSS3 的基于列的表示需要最少的学习和实现时间。最简单的是，CSS 作者需要声明他或她希望将容器内容拆分成的列数，然后浏览器会完成剩下的工作。

```
#ThreeCols        
/* If you're planning to have more containers than just one with three columns, 
   then declare it a class. */

{

    -webkit-column-count:        3;

    -moz-column-count:           3;

    column-count:                3;   

/* Currently, only Opera supports column-specific properties without prefixes. */

}

<div id = 'ThreeCols'>

    <p>

        Insert Content Here.

    </p>

    <p>

        Insert Content Here.

    </p>

    <p>

        Insert Content Here.

    </p>

</div>
```

只需这么多代码，您就可以将容器的内容分布到三列中(如果没有指定容器的高度，则通过将内容平衡到指定数量的列中来确定)。在溢出的情况下(当容器具有声明的高度时可能发生)，溢出的内容将被分布到宽度等于最初三列的列中，并且这些列将沿着内联轴生成，在英语的情况下，该轴成为 x 轴。

通过改变`column-count`的值(相应地加上前缀)，您应该能够将内容分成任意数量的列。但是，如果只指定了列数，而没有其他可用的处理指令，浏览器将尝试在指定的列数中平衡容器内容的分布——本质上是高度或数量。

为了覆盖跨列的默认内容分布策略，您需要显式地设置容器元素的`column-fill`属性。

```
/*

    In addition to the previously included values, add the following

    to control the distribution of content across columns.

*/

#ThreeCols

{

    -webkit-column-fill:    auto; /* Status undefined: undocumented */

    -moz-column-fill:       auto;

    column-fill:            auto;

}
```

相应地使用前缀，因为基于列的设计仍然是一个候选的建议。属性的默认值是 balance，它要求 UA 应该尽量减少列长度的变化。当`column-fill`的值设置为`auto`时，用户代理需要顺序填充列，这意味着可以有部分填充的和空的列。当使用`column-fill`和`auto`时，你需要记住以下几个细节:

*   只有当容器具有指定的高度时，才会参考`column-fill`属性。如果没有指定高度，UAs 将总是试图平衡列的高度，基本上忽略指定的值`column-fill`。
*   请记住，目前，当在指定高度的容器中进行基于列的内容分发时，除 Opera 之外的所有浏览器的默认行为都不符合 CR 的要求。

让我们转到更大更好地使用基于列的设计。

### CSS 列和分页内容

将`column-count`设置为 1 或将`column-width`设置为容器元素的最大宽度，指示浏览器生成一个与容器宽度相等的列，这意味着所有溢出的内容将分布在沿 x 轴或内联轴等宽的列中。理论上，通过使用上述两个属性中的一个指定值，CSS 作者应该能够创建一个页面的幻觉，其中内容被分成与容器的高度和宽度相等的部分。

在 Firefox 和 Opera 的情况下，当`column-count`设置为 1 并且容器具有指定的高度时，浏览器生成一个足够宽的单列来占据父元素的整个空间，这意味着无需显式指定`column-width`的值就可以实现分页布局效果。然而，基于 webkit 的浏览器——Safari、Chrome 和现在的 Opera——不能以列的形式分发内容。

[伪算法](https://www.w3.org/TR/css3-multicol/#pseudo-algorithm)非常清楚地表明，如果`column-width`或`column-count`具有除了`auto`之外的指定值，那么容器元素必须被视为多列元素，因此目前基于 webkit 的浏览器的行为不符合候选推荐的要求。

抛开特定的浏览器问题不谈，为了让它在大多数现代浏览器中工作——Chrome、Firefox、Opera 和 Safari——您需要将容器上的`column-width`属性的值设置为等于容器的宽度(百分比值不起作用；容器和`column-width`宽度的精确像素值或 em 值当前产生期望的结果)。

考虑到这些问题，下面的代码应该包含尽可能简单的容器级 CSS 定义，以生成页面的幻觉(请注意，它需要 JavaScript 才能工作):

```
#Paged

{

    width:        80%;

    height:       100%;

    margin:       0 auto;

    -moz-column-count: 1

    column-count:      1;

    /* Will not work in webkit based browsers, so no need to add another declaration. */

}
```

理论上，前面的 CSS 声明应该将容器的内容分成列，每列的宽度等于容器块的宽度，并将`id`设置为 Paged。然而，尽管它在目的上是完整的，但是为了使它能够跨浏览器工作，尤其是基于 webkit 的浏览器，您将需要使用下面的容器级声明来代替:

```
#Paged

{

    width:             1050px;   /* or any value of your liking but still an exact value. */

    height:            100%;

    margin:            0 auto;   /* Centered content. */

    column-width:      1050px;   /* Prefix accordingly, as this should work in most of the 
                                 modern browsers. */

}
```

如果你*有*使用基于百分比的流体设计，你总是可以依靠一些 JavaScript 和`window.innerWidth`找到实际宽度，并在`DOMContentLoaded`触发后设置容器元素的计算值。

## 翻过这一页

目前，为了重新创建页面的幻觉，您将不得不使用一些 JavaScript 指令(不需要魔法)。要完成这个任务，您需要使用 container 元素的`scrollWidth`属性的值来确定浏览器为给定内容生成的列数。为了得到`scrollWidth`的值，你必须首先将`overflow`设置为`auto`。

假设您的给定内容产生了不止一列，那么您将拥有一个带有水平溢出和水平滚动条的容器。当`DOMContentLoaded`触发时，检索`scrollWidth`的值，并将容器元素上的溢出值设置为 hidden 这将导致滚动条和溢出的内容消失。

通过将`scrollWidth`的值除以容器的实际宽度，您应该得到浏览器生成的页面数，或者宽度等于容器宽度的列数。每当用户点击适当的按钮时，您应该根据容器的总宽度增加或减少`scrollLeft`的值。

通过这样做，您应该能够向查看者呈现当前文档的下一页或上一页。在增加或减少`scrollLeft`的值时，通过保持一个值来指示当前页面并将其与页面计数的值进行比较，您应该能够调整按钮的状态，以允许用户移动到下一页或上一页。

## 结论

在为分页媒体生成内容模块的[分页表示](http://dev.w3.org/csswg/css-gcpm/#paged-presentations)部分获得完全支持之前，上述解决方案为您提供了一种简单且计算资源消耗低的解决方案，您可以在当今几乎所有的浏览器中使用该解决方案，尽管带有前缀，以创建分页内容的吸引人的幻觉。

考虑到一个吸引人的演示加上最小的加载时间(两者都可以使用上述解决方案实现)可以产生很大的影响，尽可能真实地再现传统书籍阅读体验的能力是可以实现的。

## 分享这篇文章