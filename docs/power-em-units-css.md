# CSS 中 em 单位的威力

> 原文：<https://www.sitepoint.com/power-em-units-css/>

对于 CSS 中的每一个复杂特性，当你看到这个特性有多么强大时，你总会有一个转折点。而且，信不信由你，在我写了一篇充满双关语的关于主题的介绍之后很久，我个人对 ems 的转折点才到来。

尽管那时我对 ems 已经相当了解，但当我读到 [Simurai](https://twitter.com/simurai) 在媒体上发表的名为 [Sizing (Web) components](https://medium.com/front-end-development/8f433689736f) 的帖子时，我才真正开始看到它们的强大。

所以在这篇文章中，我将借助他的力量。在这里你可以找到一个 em 单位的快速介绍，然后是他描述的技术的现场演示。

## CSS 中的 ems 是什么？

在 CSS 中，em 单位等于为 em 应用到的元素计算的`font-size`。当 em 单元在没有定义`font-size`的子元素上声明时，它们将从它们的父元素或另一个祖先元素继承它们的`font-size`，可能一直追溯到文档中的根元素。

看看下面的 CSS:

```
.example {
font-size: 20px;
}
```

在这种情况下，该元素或其子元素(假设没有其他`font-size`定义)上的 1em 将等于 20px。所以如果我们增加一行:

```
.example {
font-size: 20px;
border-radius: .5em;
}
```

`.5em`的这个`border-radius`值计算为 10px(即 20 * 5)。类似地:

```
.example {
font-size: 20px;
border-radius: .5em;
padding: 2em;
}
```

`2em`的填充值等于 40px (20*2)。如上所述，这种类型的计算也适用于任何子元素——除非这些子元素中的任何一个都有明确定义的`font-size`值，在这种情况下，em 值将基于该值进行计算。如果 CSS 中没有定义字体大小，em 单位将等于浏览器为文档设置的默认字体大小，通常是 16px。

我想这应该能清楚地说明 ems 是如何工作的。现在让我们看看如何使用这种技术来制作可轻松调整大小的 web 组件，正如 Simurai 在最初的 Medium 文章中所讨论的那样。我将通过提供一个演示来进一步了解他的想法。

## 什么时候在 CSS 中使用 em

Em 单元可以很好地与模块化 CSS 技术一起工作，例如组件级样式或一般的代码封装部分。例如，它们可以用于确保组件(例如，卡)的元素都相对于彼此调整大小，并允许其容易地调整大小。

这种技术基本上是这样工作的:Simurai 称,`font-size`属性被用作“特洛伊木马”,为组件或模块内部的各种元素创建基本单元。如上所述，由于 em 单位是基于父元素上根定义的`font-size`计算的，这使得整个组件可以通过简单地改变父元素上的`font-size`来轻松调整大小。

让我们通过 CodePen 演示来看看这一点:

通过 [CodePen](http://codepen.io) 上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看使用 ems 的可调整大小组件的笔[。](http://codepen.io/SitePoint/pen/BtJFn/)