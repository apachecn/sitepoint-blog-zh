# CSS 定位属性概览指南

> 原文：<https://www.sitepoint.com/properties-glance-guide/>

作为 [CSS 主题周](https://www.sitepoint.com/css-theme-week-more-css-than-you-can-handle)的一部分，本周的每一天，我们都会发表一篇由我们编辑精心挑选的文章。

**这篇文章的目标读者是有经验的 CSS 开发人员，他们需要参考 CSS 2 中与定位相关的属性。本文的每一部分都包含了一个链接，链接到网站 CSS 参考资料和 W3C CSS 2 规范的相关部分。**

本文并不打算深入涵盖规范中包含的所有与定位相关的信息，而只是描述那些在一些或所有现代浏览器中实现的、对您今天的现实世界有用的属性和值。

##### 定义

首先让我们看看一些需要解释的概念的定义。如果你愿意，你可以直接跳到前面，用这个列表作为参考来检查你是否发现了什么。当然，不要忘记 [SitePoint CSS 引用](https://reference.sitepoint.com/css/)包含了所有属性的全面信息，而不仅仅是这里整理的那些。

***视口***

[https://www . w3 . org/tr/rec-CSS 2/visren . html # Q2](https://www.w3.org/TR/CSS2/visuren.html)

[视窗](https://reference.sitepoint.com/css/viewport)是显示网页的屏幕区域。这与初始包含块不同。

***初始包含块***

[https://www . w3 . org/TR/REC-CSS2/visuren . html # initial-containing-block](https://www.w3.org/TR/CSS2/visuren.html)

[初始包含块](https://reference.sitepoint.com/css/containingblock)是分配给文档的整个空间。在提到整个空间时，我们包括用户必须滚动才能看到的页面的任何部分(在视窗中)。

可以通过设置根元素的`width`和`height`属性来限制初始包含块的大小。

***根元素***

根元素是`html`元素。当我们将 CSS 规则应用于根元素时，将它们应用于`html`和`body`元素会更安全(允许浏览器随心所欲):

```
html, body { 

  /* Style Rules Here */ 

}
```

然而，这种方法必须小心，因为净效应有可能复合。例如，执行以下操作将导致 100 像素的填充应用于页面的每个边缘，每个元素 50 像素。

```
html, body {  

  padding:50px; 

}
```

***包含积木***

[https://www . w3 . org/TR/REC-CSS2/visuren . html # containing-block](https://www.w3.org/TR/REC-CSS2/visuren.html)

文档中的每个元素都有一个包含块的[。这是它所在的块元素，例如:](https://reference.sitepoint.com/css/containingblock)

```
<div id="content"> 

  <div id="example"> 

  </div> 

</div>
```

`div#content`是`div#example`的包含块。

***框类型***

盒子有多种不同的类型。在本文中，我们只关心`[block](https://reference.sitepoint.com/css/blockformatting)`盒子和`[inline](https://reference.sitepoint.com/css/inlineformatting)`盒子。

*积木盒子*
[https://www.w3.org/TR/REC-CSS2/visuren.html#q5](https://www.w3.org/TR/CSS2/visuren.html)

[块框](https://reference.sitepoint.com/css/blockformatting)是由`p`和`div`等元素生成的框的类型(默认)。块盒周围有空间，后面的元素从盒的下面开始，而不是紧挨着它。

例如:

```
<p>Test</p> 

<p>Text</p>
```

将显示为(默认情况下):

```
Test 

Text
```

而不是:

```
Test Text
```

…就像`p`是(默认)内联元素一样。

*内嵌框*
[https://www.w3.org/TR/REC-CSS2/visuren.html#q7](https://www.w3.org/TR/CSS2/visuren.html)

[行内元素](https://reference.sitepoint.com/css/inlineformatting)并排显示。例如:

```
<strong>Test</strong> 

<em>Text</em>
```

将显示为(默认情况下):

```
Test Text
```

而不是:

```
Test 

Text
```

…忽略这些元素通常产生的任何其他可视格式。

##### 定位方案

[https://www . w3 . org/TR/REC-CSS2/visuren . html # positioning-scheme](https://www.w3.org/TR/CSS2/visuren.html)

CSS 2 中有[五种风格的定位。](https://reference.sitepoint.com/css/positioning)

***正常流量***

[https://www.w3.org/TR/REC-CSS2/visuren.html#normal-flow](https://www.w3.org/TR/CSS2/visuren.html)

[正常定位](https://reference.sitepoint.com/css/formattingcontext)是根据已经存在了近十年的“正常”规则设定的。

***相对定位***

[https://www . w3 . org/TR/REC-CSS2/visuren . html #相对定位](https://www.w3.org/TR/CSS2/visuren.html)

元件按正常流程定位，然后移动。跟在相对定位的元素后面的元素的行为就好像相对定位的元素处于它的“正常流动”位置，即使这意味着它们占用相同的屏幕空间。

***浮动定位***

[https://www.w3.org/TR/REC-CSS2/visuren.html#floats](https://www.w3.org/TR/CSS2/visuren.html)

使用[浮动](https://www.sitepoint.com/atoz-css-screencast-float-and-clear/)定位的箱子使用正常流程定位，然后向左或向右移动，直到它们能够移动为止。出现在它们之后的元素将向上移动以填充留下的任何空隙，但将围绕盒子流动——它们不会占据相同的屏幕空间。

***绝对定位***

[https://www . w3 . org/TR/REC-CSS2/visuren . html # absolute-positioning](https://www.w3.org/TR/CSS2/visuren.html)

[绝对定位的](https://reference.sitepoint.com/css/absolutepositioning)元件不受正常流量的影响。通过指定它们的边和它们包含的块之间的精确距离来定位它们。

***固定定位***

[https://www . w3 . org/TR/REC-CSS2/visuren . html #固定定位](https://www.w3.org/TR/CSS2/visuren.html)
 [固定位置](https://reference.sitepoint.com/css/fixedpositioning)元素的工作方式与绝对定位元素相同，只是它们的位置是相对于视口的。因此，如果页面滚动，它们不会移动–它们相对于视窗保持不动。

##### CSS 可视显示属性

***`display`地产***

[https://www.w3.org/TR/REC-CSS2/visuren.html#display-prop](https://www.w3.org/TR/CSS2/visuren.html)

属性用来控制一个元素的盒子类型。这允许您执行以下操作:

*   将粗体文本设置为块级别，
*   有内嵌段落，或者
*   阻止元素显示或占用屏幕空间。

*T2`display:block`*

将选定的元素设置为块级别。

*T2`display:inline`*

将选定的元素设置为内嵌。

```
*display:none*
```

停止显示它所分配到的元素。该元素完全从显示屏上移除，不占用任何屏幕空间。

对于`display`属性，还有许多其他的值，我们在本文中并不关心。它们处理[列表](https://reference.sitepoint.com/css/listformatting)、[表格](https://reference.sitepoint.com/css/tableformatting)和[其他盒子类型](https://reference.sitepoint.com/css/replacedelements)我们在这里不会涉及，但是在 SitePoint 的在线 [CSS 参考](https://reference.sitepoint.com/css)中会详细介绍。

***`position`地产***

[https://www.w3.org/TR/REC-CSS2/visuren.html#choose-position](https://www.w3.org/TR/CSS2/visuren.html)

`position`属性用于指定以下定位方案:

*   正常流程，
*   相对定位，
*   绝对定位，以及
*   固定定位。

这是通过将位置设置为下列值之一来实现的:

```
*position:static;*
```

将此框设置为正常流动，这是所有元素的默认值。

```
*position:relative;*
```

使用`top`、`right`、`bottom`和`left`属性对该框进行相对定位。

```
*position:absolute;*
```

使用`top`、`right`、`bottom`和`left`属性对该盒子进行绝对定位。

```
*position:fixed;*
```

使用`top`、`right`、`bottom`和`left`属性对该框进行固定定位。

***`top``right``bottom``left`属性***

[https://www.w3.org/TR/REC-CSS2/visuren.html#position-props](https://www.w3.org/TR/CSS2/visuren.html)

所有的定位方案都需要你设置这些，以便知道如何执行你的定位请求。您应该始终设置`top`或`bottom`中的一个和`left`或`right`中的一个。同时设置`top`和`bottom`或者同时设置`left`和`right`通常不会在 Internet Explorer 中实现您想要的效果——通常，您最终只会分别使用`left`或`top`。

您可以为这些属性使用下列值:

*固定长度*
你可以使用固定的距离，比如 20 像素的 20px。

*百分比*
您可以使用一个百分比值，如 20%，它是包含块的`width`或`height`值的 20%(分别用于`left` / `right`或`top` / `bottom`)。

***`float`地产***

[https://www.w3.org/TR/REC-CSS2/visuren.html#float-position](https://www.w3.org/TR/CSS2/visuren.html)

`float`属性有三个值。如果您将`position`属性设置为`absolute`或`fixed`，则`float`将被忽略。

```
*float:right;*

The box is floated to the right of the screen.
```

```
*float:left;*

The box is floated to the left of the screen.
```

```
*float:none;*

The box is not floated.
```

***`clear`地产***

[TTP://www . w3 . org/TR/REC-CSS2/visuren . html #流量控制](https://www.w3.org/TR/CSS2/visuren.html)

此属性用于控制出现在浮动框之后的框。在框上设置该属性将确保它出现在它之前的所有浮动框的下面。

```
*clear:left;*

This box will appear below any left-floating boxes that come before it.
```

*T2`clear:right;`*

这个框将出现在它之前的所有右浮动框的下面。

*T2`clear:both;`*

此框将出现在它之前的任何左浮动或右浮动框的下方。

*T2`clear:none;`*

此框不会清除它之前的任何浮动框。

***`z-index`地产***

[https://www . w3 . org/tr/rec-CSS 2/visren . html # q30](https://www.w3.org/TR/CSS2/visuren.html)

属性用于指定盒子如何堆叠在彼此的顶部，当你有两个占据相同屏幕空间的盒子时，这个属性是相关的。`z-index`接受 0 到 32767 之间的值。在两个盒子占据相同屏幕空间的情况下，具有最高`z-index`的盒子显示在‘顶部’。如果两者具有相同的值，则代码中最后一个值显示在顶部。

## 分享这篇文章