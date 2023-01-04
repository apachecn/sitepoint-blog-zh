# 面向初学者的 CSS 多列布局教程

> 原文：<https://www.sitepoint.com/css-multi-column-layout-tutorial-for-beginners/>

对于一些人来说，阅读很长的文本可能会有问题。他们将不得不更加关注不要漏掉一行，而不是文本本身。通过使用多列来布局内容，这个问题很容易解决。多栏在印刷媒体中无处不在。CSS 多列布局模块的特性使我们能够在网站上重新创建同样的多列效果。

设计网页时很难使用多列的一个原因是无法控制文档的大小。在本教程中，我将教你如何创建在各种屏幕尺寸下都好看的响应式多栏布局。我们将从基础开始，然后转向更复杂的概念。

## 浏览器支持

如果您愿意使用前缀，浏览器对多栏布局的支持是非常好的。根据我可以使用的[统计，全球 95.32%的浏览器支持该功能。IE10+、Edge、Opera Mini 等少数浏览器完全支持多栏布局。其他如 Firefox 和 Chrome 需要前缀。](http://caniuse.com/#feat=multicolumn)

如果您需要支持旧版本的浏览器(通常指 IE9 及以下版本)，可以尝试使用旧版本的 polyfill。当然，如果浏览器不支持多栏功能，布局会优雅地降级为单列布局。因此，在这种情况下，聚合填充可能不是最佳选择。

CSS 多列布局模块有许多不同的属性。在接下来的部分中，我将一个接一个地介绍它们。

## 列数和列宽

属性指定了你想要为一个元素设置的列数。可以设置为`auto`或者正数。当设置为`auto`时，列数将由`column-width`属性决定。如果设置为正数，则所有列的宽度都相等。

属性指定了一个元素的各个列的宽度。这是不需要严格遵守的。例如，如果没有足够的可用空间，列可以更窄。这个属性也可以设置为一个`auto`值或者一个正数。如果设置为自动，宽度将由`column-count`属性决定。可用空间将在所有列中平均分配。

或者，可以使用简写的`columns`属性同时设置这两个值。`columns`属性的语法是:

```
.example {
  columns: <'column-width'> || <'column-count'>
}
```

下面显示了该属性的几个使用示例，并在每个示例旁边的注释中给出了解释:

```
.example {
  columns: 10em;      /* column-width: 10em / column-count: auto */
  columns: 4;         /* column-width: auto / column-count: 4 */
  columns: 4 auto;    /* column-width: auto / column-count: 4 */
  columns: auto 10em; /* column-count: auto / column-width: 4 */
  columns: auto;      /* column-count: auto / column-width: auto */
  columns: auto auto; /* column-count: auto / column-width: auto */
}
```

如你所见，第一个`columns`定义是你在第四个中看到的内容的简写，第二个是第三个的简写。基本上，如果整数没有分配任何单位，它将被解析为`column-count`。

[**这是一个 CodePen 演示程序，演示到目前为止讨论过的特性**](http://codepen.io/SitePoint/pen/WwvMQK)

如果调整窗口大小，您会注意到一些事情:

*   属性总是保持列数等于您指定的值。唯一改变的是列的宽度。
*   属性根据可用空间自动改变列数。调整列数的方式是使列宽大于指定值。如果没有足够的可用空间，它还可以将所有列的宽度调整为较小的值。
*   `columns`属性使用`column-count`值作为允许的最大列数的限制。它不断调整宽度，使`column-count`永远不会超过计数限制，并且`column-width`也非常接近指定的宽度。

## 列间距和列规则

属性允许我们指定列之间的间距。您可以将其设置为`normal`或一个长度值。可以为零，但不能为负。当您将其设置为`normal`时，它会使用浏览器定义的默认列间距。

属性是一种速记法，它使我们能够在列之间添加一条线。这类似于使用`border-left`或`border-right`属性。该属性遵循以下语法:

```
.example {
  column-rule: <'column-rule-width'> || <'column-rule-style'> || <'column-rule-color'>
}
```

对于`column-rule-width`，您可以像`3px`一样将宽度指定为长度，或者使用像`thin`、`medium`或`thick`这样的关键字。`column-rule-style`接受`dashed`、`dotted`、`solid`等值。您可以使用`border-style`属性的所有有效值，而`column-rule-style`和`column-rule-color`可以是任何有效的颜色值。

[**这里是一个与这些属性一起使用的 CodePen 演示**](http://codepen.io/SitePoint/pen/PNqQxd)

## 列填充和列跨度

`column-fill`属性决定如何对内容进行分区以填充各个列。该属性可以设置为`auto`或`balance`。当设置为`auto`时，列被顺序填充。使用`balance`可以确保内容在所有列之间平均分配。

值得记住的一点是，如果你在列元素上设置了固定的高度，Firefox 会自动平衡内容。然而，其他浏览器开始顺序填充列。

属性控制一个元素如何跨越多列。它有两个可能的值:`all`或`none`。当设置为`all`时，元素跨越所有列。火狐不支持此属性[。](https://bugzilla.mozilla.org/show_bug.cgi?id=616436)

[**这是一个 CodePen 演示程序，它将一个 blockquote 元素“跨越”所有列**](http://codepen.io/SitePoint/pen/yONvry)

在 Firefox 中，blockquote 出现在中间一列，这可能是一个可以接受的后备选项。

## 创建具有多列的响应式布局

现在您已经熟悉了不同的属性和可能的值，让我们把注意力放在保持布局的响应性和用户友好性上。

`column-count`和`column-width`都有自己的问题。虽然`column-count`在较大的设备上控制列数，但在较小的设备上布局会中断。类似地，`column-width`将确保在较小的设备上列不会太窄，但在较大的设备上会有很多列。

为了确保你的布局在所有的屏幕尺寸上都好看，你应该同时指定`column-count`和`column-width`。这样，您可以控制列的宽度和数量。但是，您可能仍然需要修复一些问题，我们将在接下来讨论这些问题。

### 固定水平滚动

如果为列指定固定的高度，缩小视区会导致水平滚动条。由于内容不能垂直扩展，它将水平增长。要解决这个问题，您可以调整浏览器的大小，以了解水平滚动条第一次出现时的宽度。然后，使用媒体查询将列的高度设置为低于该宽度的`auto`。下面是执行此操作的代码:

```
.responsive-height {
  height: 250px;
}

@media (max-width: 1040px) {
  .responsive-height {
    height: auto;
  }
}
```

[**这个 CodePen 演示展示了问题和可能的解决方案**](http://codepen.io/SitePoint/pen/vGOdwj)

调整窗口大小以查看两个示例如何响应。

### 固定非常长的柱

如果你的栏目太长，用户要不停的上下滚动才能看完所有内容，会很烦。当列的高度大于视口高度时，最好去掉多个列。这也可以通过媒体查询来实现:

```
@media (min-width: 800px) {
  .long-columns {
    columns: 3 200px;
  }
}
```

在这种情况下，我只在视窗宽度允许用户不必上下滚动时使用多列。

[**查看多栏目演示和媒体查询**](http://codepen.io/SitePoint/pen/reVJEL)

## 结论

希望这篇关于 CSS 的多栏布局模块的介绍已经让你熟悉了这个特性。这些属性可以很好地添加到您的响应式设计工具箱中，如果您仍然需要支持旧的浏览器，多列通常会降级为单列。

## 分享这篇文章