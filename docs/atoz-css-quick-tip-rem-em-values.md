# AtoZ CSS 快速提示:rem 和 em 值的好处

> 原文：<https://www.sitepoint.com/atoz-css-quick-tip-rem-em-values/>

本文是我们 AtoZ CSS 系列的一部分。你可以在这里找到这个系列[的其他条目。
你可以在这里](https://www.sitepoint.com/blog/)查看`:required`伪班[相应视频的完整文字稿和截屏。](https://www.sitepoint.com/atoz-css-pseudo-elements)

欢迎来到我们的 AtoZ CSS 系列！在这个系列中，我将探索不同的 CSS 值(和属性),每个值都以字母表中不同的字母开始。我们知道有时仅仅截屏是不够的，所以在本文中，我们添加了关于使用`rem`和`em`值的快速提示。

![R2-01](img/3cc4155dc6fc468504f8408e83e704f0.png)

## r 代表`rem`和`em`

在最初的截屏视频中，我们了解了所有关于[`:required`伪类](https://www.sitepoint.com/atoz-css-screencast-required)的知识，它对于设计必须填写字段的表单非常有用。

虽然表单、验证和样式化状态是很大的话题，但是我们第一次讨论`:required`的时候没有涉及太多的内容。因此，让我们来看几个使用`rem`测量单位的快速提示。但首先，让我们看看另一种类型的相对单位:`em`。

### 使用`em`的利弊

当在一个响应式项目中工作时，使用像`em`这样的相对单位来调整文本大小和元素内部及周围的间距比使用像素更灵活。这是因为这个单位是相对于其父元素的字体大小，允许元素的大小、间距和文本内容随着父元素的`font-size`变化而成比例增长。

使用这些相对单位使您能够构建一个比例系统，其中改变一个元素上的`font-size`值会对其中的子元素产生级联效应。比例系统是个好东西，但是`em`的这种行为也有不好的一面。

以下面的 HTML 片段为例:

```
<ul> 
  <li>lorem ipsum</li> 
  <li>dolor sit 
   <ol> 
    <li>lorem ipsum</li> 
    <li>lorem ipsum</li> 
    <li>lorem ipsum</li> 
    <li>lorem ipsum</li> 
   </ol> 
 </li> 
</ul>
```

这种嵌套列表并不是世界上最常见的东西，但可能会出现在条款和条件页面或其他类型的正式文档中。

如果我们想突出列表项，我们可以将它们的`font-size`设置为基本大小`16px`的 1.5 倍。

```
li {
  font-size: 1.5em; /* 24px/16px */
}
```

但是这将导致嵌套的`li`出现问题，因为它们的大小也将是其父级的 1.5 倍。嵌套的项目将是 1.5 倍的`24px`，而不是 1.5 倍的`16px`。结果是，任何嵌套的列表项都将随着嵌套的每一级呈指数增长。这可能不是设计者的意图！

嵌套元素和小于 1 的`em`值也会出现类似的问题。在这种情况下，任何嵌套的项目都会随着嵌套的每一级而逐渐变小。

那么我们能做些什么呢？

### 使用`rem`设置文本大小

我们可以使用替代单位，而不是冒不断增加或减少的风险。

如前所述，我们可以使用像素，但相对单位在响应项目中更灵活。相反，我们可以使用`rem`单位，因为它总是基于根元素的`font-size`来计算，在网站或 web 应用程序的情况下，根元素通常是`html`元素。在. svg 或。xml 文档根元素可能有所不同，但是这些类型的文档不是我们在这里关心的。

如果我们用`rem`来设定`font-size`,这并不意味着卑微的`em`永远不能进去。我倾向于使用`em`来设置元素中的`padding`，这样间距总是相对于文本的大小。

### 使用 Sass 帮助支持`rem`浏览器

只有 IE9 及以上版本才支持`rem`单元。如果您需要支持 IE8(或更低版本),那么您可以使用 JS polyfill 或以下列方式提供一个`px`回退:

```
li {
  font-size: 24px;
  font-size: 1.5rem;
}
```

如果你正在使用 Sass，你可以创建一个 mixin 和一个函数来计算期望的大小并自动提供回退。

```
@function rem-calc($font-size, $base-font-size: 16) {
  @return ($size/$base-font-size) *1rem;
}
@mixin rem-with-px-fallback($size, $property:font-size) {
  #{$property}: $size * 1px;
  #{$property}: rem-calc($size);
}
li {
  @include rem-with-px-fallback(24);
}
```

这就是了。使用`rem`的几个快速提示。如果您没有在当前的项目中使用它们，我强烈建议您尝试一下。

## 分享这篇文章