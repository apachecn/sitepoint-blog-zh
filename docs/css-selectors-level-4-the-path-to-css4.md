# CSS 选择器级别 4:到 CSS4 的路径

> 原文：<https://www.sitepoint.com/css-selectors-level-4-the-path-to-css4/>

最近我在 SitePoint 上发表了一篇文章，展示了使用 [CSS3 2D 和 3D 变换](https://www.sitepoint.com/advanced-css3-2d-and-3d-transform-techniques/)的不同例子。CSS3 让我们如此兴奋，你可能会认为这是 CSS 可用的最高级版本。

并非如此。

甚至现在，就在你还在学习使用 CSS3 和 HTML5 的时候，W3C 的那些天才们已经发布了 CSS 选择器的第 4 级标准的工作草案，制造了一个 CSS4 即将到来的谣言。

## CSS3 已经过时了吗？

绝对不行！在 CSS3 被广泛使用和完全支持之前，我们还有很多工作要做。然而，网络正处于一个不断发展变化的状态。选择者四级是 1 月 16 日发布的编辑稿。它仅供讨论，应被视为一项正在进行的工作。

还要注意，正在讨论的第 4 级选择器并不等同于即将发布的 CSS 第 4 版。

它向我们展示了在样式表中使用选择器的各种新方法。这些发展已经达到第 4 级，揭示了 CSS4 中可能包含的一些内容。所以我们正在寻找一条通向 CSS4 的道路，还有很长的路要走。

在这篇文章中，我将试图强调一些最不具挑战性和潜在意义的第 4 级选择器的使用方法。

## 什么是选择器？

选择器是 HTML 树结构中的模式匹配条件。从 CSS 的第一级开始，我们就一直在使用选择器。选择器的范围可以从简单的元素名称到复杂的表示。关于选择器的第 4 级文档主要由伪类组成，为它们添加效果。它记录了一些很酷很有趣的特性，我会解释的。

### 选择器的基本示例:

```
h1{ font-family: arial; } /* Element matching selector for matching heading1 statements */
```

```
a{ text-decoration: underline; } /* Element matching selector for hyperlinks in the documents */
```

```
h1, h2, h3{ font-color: blue; } /* combination of selectors */
```

## 逻辑组合::匹配和:不匹配

和 Mozilla 的`:-moz-any()`伪类一样，CSS 终于有了自己匹配的伪类，`:matches`。使用`:matches`你可以分组和选择元素。它是一个函数伪类，将选择器列表作为参数。在 4 级选择器中，您只能将复合选择器作为参数包含在`:matches`中。像空格、大于号等组合符都不是有效的参数。

你甚至不能将`:matches`嵌套在它自己或者像`:matches(:matches(…))`或`:not(:matches())`一样嵌套在`:not`伪类中。这些都是新规范中`:matches`的无效使用。

而不是:

```
a:link, a:hover, a:visited, a:focus{
    color:red;
}
```

您可以使用:

```
a:matches(:link, :hover, :visited, :focus){
    color:red;
}
```

虽然这看起来很简单，但是当您有更多的元素要分组时，您可以以各种复杂的方式使用它，例如:

```
div:matches(.active, .visible, #main){
    background: blue;
}
```

让我们看看如何使用第二个逻辑组合子`:not`。它是一个否定伪类，接受一系列其他选择器作为参数。有时，我们有更多的元素来应用特定的样式，并在列表中省略一些元素。在这种情况下，您可以使用`:not`,并传递您不想应用特定样式的选择器列表。

```
h1:not(#title, .active){
    color: #F2F2F2;
}
```

上面的代码会给所有不是`#title`和`.active`的`h1`元素一个颜色代码`#F2F2F2`。有意思不是吗？

## 基于位置的伪类:任意链接和本地链接

基于位置的伪类给了你更多设计链接元素的能力。4 级文档说明这是一个临时名称，将来可能会更改。它表示作为超链接的源锚点的任何元素。它把`a:visited`和`a:lin` k 合二为一，这样你就不用写两遍了。

例如:

```
a:link, a:visited{
    color: blue;
}
```

可以重写为:

```
a:any-link{
    color:blue;
}
```

上面的代码将所有的链接元素涂成蓝色，不管它们是否被访问过。

本文档中添加的另一个非常有趣的功能是`:local-link`。使用这个你可以应用指向当前文档的链接样式。这将有助于网站设计者给那些本地链接一个不同的外观，并且不会打开任何不同的网页。

```
a:local-link{
    color: green;
}
```

您也可以使用`:local-link`中的参数匹配本地链接 url 的级别。例如:

```
a:local-link(0){
    color: #888888;
}

a:local-link(1){
    color: green;
}

a:local-link(2){
    color: blue;
}
```

让我借助一个示例链接更清楚地解释给你听:[https://www.sitepoint.com/tag/css/](https://www.sitepoint.com/html-css/css/)

1.  链接[https://www.sitepoint.com](https://www.sitepoint.com)将按照上面的样式上色#888888。
2.  链接[https://www.sitepoint.com/tag/](https://www.sitepoint.com/blog/)将被涂成绿色
3.  链接[https://www.sitepoint.com/tag/css/](https://www.sitepoint.com/html-css/css/)将被涂成蓝色。

## 三维伪类::当前，:过去和:未来

这些伪类给网页中的文本以三维效果。您可以使用它来高亮显示当前正在语音中阅读的网页部分。例如:

```
:current(p, li, dt, dd) {
    background: yellow;
}
```

`:past`伪类表示在`:current`部分之前的文本部分，而`:future`伪类表示下一部分。使用这些伪类突出显示和模糊文本将会给读者一个舒适的文档视图。

## 网格结构伪类::COLUMN，:n 列和:n-最后一列

CSS3 中已经引入了基于列的设计，但是设计网页的每一列是很困难的。随着网格结构伪类的引入，设计人员肯定会发现为文档的不同列提供不同的样式变得更加容易。

例如:

```
:column(.full){
    background: #F2F2F2;
}

.nth-column(odd){
    color: #999999;
}
```

在上面的代码中，`.full`的所有列的背景将被更改为`#F2F2F2`，奇数列将有颜色代码`#999999`。

## 父选择器

我相信这是所有新选择器列表中最重要的一项。

当子元素处于活动状态时，设计者抱怨父元素失去焦点。当子元素有焦点时，不可能对父元素应用动态样式。

例如，当焦点在最低级别的`li`元素上时，在设计下拉菜单的情况下，主父`li`元素总是失去焦点。在`ul li a.active`中，焦点在链接元素上，而不是`li`。在父选择器的帮助下，你现在可以告诉浏览器你想要对`li`而不是`a`应用样式。让我们来看看一个工作示例。

```
ul li! a.active {
    color: red;
}
```

在上面的代码中，`li`元素是红色的，而不是`a:activ` e。

```
body! header a:hover {
    background: red;
}
```

在上面的代码片段中，body 被赋予了样式，而不是 link 元素。

## 结论

现在我们已经看到了 CSS 中选择器的新水平，我个人非常渴望在我的项目中使用它们。然而，在它们在我们的浏览器中实现之前，我们都必须等待一段时间。

选择器是 CSS 家族中最重要的成员。现在，随着 4 级文档的引入，他们再次证明了他们的重要性。

关于所有变化的细节，你可以去 W3C 网站的[完整文档](http://dev.w3.org/csswg/selectors4/)获取更多参考。

## 分享这篇文章