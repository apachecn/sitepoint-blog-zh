# CSS 中的 Rem:理解和使用 rem 单元

> 原文：<https://www.sitepoint.com/understanding-and-using-rem-units-in-css/>

深入了解 CSS 中的 **rem 单元，这是一个具有出色浏览器支持的相对大小调整单元，并学习如何有效地使用它们。**

## 什么是雷姆单位？

在 CSS 中，rem 代表“root em”，这是一个度量单位，表示根元素的字体大小。这意味着`1rem`等于`html`元素的字体大小，对于大多数浏览器来说，默认值为 16px。使用 rem 有助于确保整个 UI 中字体大小和间距的一致性。

根据 W3C 规范,一个 rem 单位的定义是:

> 等于根元素上`font-size`的计算值。当在根元素的`font-size`属性上指定时，rem 单位指的是属性的初始值。

## 雷姆单位与电磁单位

rem 单位和 em 单位的区别在于， [em 单位](https://www.sitepoint.com/power-em-units-css/)是相对于它们的**自己的**元素的字体大小，而不是**根**元素。因此，它们可以级联并导致意想不到的结果。让我们考虑下面的例子，我们希望列表的字体大小为`12px`，根字体大小是默认的`16px`:

```
html {
font-size: 100%;
}

ul {
font-size: 0.75em;
}
```

如果我们有一个列表嵌套在另一个列表中，内部列表的字体大小将是其父列表大小的 75%(在本例中为`9px`)。我们仍然可以通过使用以下方法来解决这个问题:

```
ul ul {
font-size: 1em;
}
```

这确实有用，但是我们仍然需要注意嵌套越来越深的情况。

有了 rem 单元，事情就简单多了:

```
html {
font-size: 100%;
}

ul {
font-size: 0.75rem;
}
```

因为所有的大小都是从根字体大小引用的，所以不再需要在单独的声明中包含嵌套的情况。

## 以雷姆为单位的字体大小

使用 rem 单位进行字体大小调整的先驱之一是 Jonathan Snook，他在 2011 年 5 月发表了一篇文章。像许多其他 CSS 开发人员一样，他不得不面对 em 单元在复杂布局中带来的问题。

当时，IE 的旧版本仍然有很大的市场份额，它们不能缩放像素大小的文本。然而，正如我们前面看到的，使用 em 单元很容易失去嵌套的踪迹并得到意想不到的结果。

使用 rem 进行字体大小调整的主要问题是这些值有点难以使用。让我们看一个用 rem 单位表示的常见字体大小的例子，当然，假设基本大小是 16px:

*   10px = 0.625rem 雷姆
*   12px = 0.75rem 雷姆
*   14px = 0.875rem 公尺
*   16px =元(基数)
*   18px = 1.125rem 公尺
*   20px = 1.25rem 雷姆
*   24px = 1.5rem
*   30px = 1.875rem 公尺
*   32px = 2rem

正如我们所看到的，这些值不太便于计算。为此，史努克用了一招叫做“ **62.5%** ”。无论如何，这不是一个新发现，因为它已经被用于 em 单元:

```
body { font-size:62.5%; } /* =10px */
h1 { font-size: 2.4em; } /* =24px */
p { font-size: 1.4em; } /* =14px */
li { font-size: 1.4em; } /* =14px? */
```

由于 rem 单元与根元素相关，Snook 的解决方案变型为:

```
html { font-size: 62.5%; } /* =10px */
body { font-size: 1.4rem; } /* =14px */
h1 { font-size: 2.4rem; } /* =24px */
```

人们还必须考虑到其他不支持 rem 的浏览器。因此，上面的代码实际上应该是这样写的:

```
html {
font-size: 62.5%;
}

body {
font-size: 14px;
font-size: 1.4rem;
}

h1 {
font-size: 24px;
font-size: 2.4rem;
}
```

虽然这种解决方案似乎接近“黄金法则”的地位，但有人建议不要盲目使用它。哈利·罗伯特写了他自己对雷姆单位使用的看法。在他看来，虽然 **62.5%** 的解决方案使计算更容易(因为`px`中的字体大小是其 rem 值的 10 倍)，但它最终会迫使开发人员显式地重写他们网站中的所有字体大小。

[第三种观点](https://css-tricks.com/rems-ems/)来自 CSS-Tricks 的克里斯·科伊尔。他的解决方案利用了我们到目前为止遇到的所有三个单元。他保留了在`px`中定义的根大小，用 rem 单元定义的模块，以及用 em 定义的模块内部的元素。这种方法使得操作全局大小变得更加容易，全局大小缩放模块中的类型，而模块内容基于模块字体大小本身进行缩放。路易斯·拉扎勒斯在[CSS](https://www.sitepoint.com/power-em-units-css/)中讨论了后来的概念。

在下面的示例中，您可以看到 Chris 的方法是什么样的:

参见 [CodePen](http://codepen.io) 上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )使用 CSS 中 ems 和 rems 的 Pen [One Method。](http://codepen.io/SitePoint/pen/XmRaGd/)