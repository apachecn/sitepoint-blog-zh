# 使用 CSS 字体大小调整改进 Web 排版

> 原文：<https://www.sitepoint.com/improve-web-typography-css-font-size-adjust/>

![CSS font-size-adjust Property](img/6184ffb51f3713522a5abca4a3121c22.png)

*本文由 [Panayotis Matsinopoulos](https://www.sitepoint.com/author/pmatsinopoulos/) 进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

CSS 中的 [`font-size-adjust`属性](https://drafts.csswg.org/css-fonts-3/#propdef-font-size-adjust)允许开发者根据小写字母而不是大写字母的高度来指定`font-size`。这可以显著提高网页上文本的易读性。

在本文中，您将了解到`font-size-adjust`属性的重要性以及如何在您的项目中正确使用它。

## 字体大小调整的重要性

您访问的大多数网站主要由文本组成。因为文字是网站如此重要的一部分，所以特别注意你用来显示信息的字体是有意义的。选择合适的字体可以带来愉快的阅读体验。然而，使用错误的会使网站难以辨认。一旦你决定了你想要使用的字体，你通常会选择一个合适的大小。

属性设置你想在网站上使用的所有选项的大小。然而，大多数时候它通常是以这样一种方式选择的，你的第一个`font-family`选项看起来不错。当第一个选择由于某种原因不可用，并且浏览器使用 CSS 文档中列出的备用字体之一来呈现文本时，就会出现问题。

例如，给定这个 CSS 规则:

```
body {
  font-family: 'Lato', Verdana, sans-serif;
}
```

如果你的浏览器从[谷歌字体](https://fonts.google.com/?query=lato&selection.family=Lato)下载的“Lato”不可用，将使用下一个备用字体，在这种情况下是 Verdana。然而，选择`font-size`值时很可能考虑的是“Lato ”,而不是 Verdana。

### 网页字体的长宽值是多少？

对于恒定的`font-size`值，字体的外观大小及其易读性可能会有很大的不同。对于像拉丁文这样区分大小写字母的文字来说尤其如此。在这种情况下，小写字母与其大写字母的高度之比是决定给定字体易读性的一个重要因素。这个比率通常被称为字体的**纵横比**。

正如我前面提到的，一旦你设置了一个`font-size`值，它将对所有字体系列保持不变。但是，如果备用字体的外观值与首选字体的外观值相差太大，这可能会影响备用字体的易读性。

在这种情况下，`font-size-adjust`属性的作用变得非常重要，因为它允许你将所有字体的 x 高度设置为相同的值，从而提高它们的可读性。

## 为字体大小调整选择正确的值

现在你知道了使用`font-size-adjust`属性的重要性，是时候学习如何在你的网站上使用它了。该属性具有以下语法:

```
font-size-adjust: none | <number>
```

`font-size-adjust`的初始值为`none`。值`none`意味着不同`font-family`选项的`font-size`的值不会被调整。

您还可以将`font-size-adjust`属性的值设置为一个数字。这个数字用于将网页上所有字体的 x 高度计算为相同的值。x 高度等于给定数字乘以`font-size`。这可以大大提高小字体的可读性。下面是一个使用`font-size-adjust`属性的例子。

```
font-size: 20px;
font-size-adjust: 0.6;
```

所有字体的 x 高度现在将是 20px * 0.6 = 12px。现在可以更改给定字体的实际大小，以确保 x 高度始终保持在 12px。给定字体的新调整后的`font-size`可以使用以下公式计算:

`c = ( a / a' ) s`。

这里，`c`是要使用的调整后的`font-size`，`s`是指定的`font-size`值，`a`是由`font-size-adjust`属性指定的纵横比值，`a'`是需要调整的字体的纵横比值。

您不能将`font-size-adjust`设置为负值。值为 0 将导致文本没有高度。换句话说，文本将被有效地隐藏。在较老的浏览器中，如 Firefox 40，值 0 相当于将`font-size-adjust`设置为`none`。

在大多数情况下，开发人员通常会用几个`font-size`值进行实验，看看什么最适合给定的字体。这意味着，理想情况下，他们希望所有字体选项的 x 高度等于他们首选字体的 x 高度。换句话说，`font-size-adjust`属性最合适的值是您首选字体的纵横比。

## 如何知道字体的纵横比

要确定字体的正确纵横比，您可以依赖这样一个事实，即调整后的`font-size`应该与您指定的原始`font-size`相同。这意味着`a`应该等于前面等式中的`a'`。

计算纵横比的第一步是创建两个`<span>`元素。两个元素都将包含一个字母和每个字母周围的边框(两个`<span>`元素的字母必须相同，因为我们需要对它们进行比较)。同样，两个元素的`font-size`属性的值相同，但是只有一个元素使用`font-size-adjust`属性。当`font-size-adjust`的值等于给定字体的纵横比时，每个`<span>`元素中的两个字母将具有相同的大小。

在下面的演示中，我在字母“t”和“b”周围创建了一个边框，并为每一对应用了不同的`font-size-adjust`值。

以下是相关片段:

```
.adjusted-a {
  font-size-adjust: 0.4;
}

.adjusted-b {
  font-size-adjust: 0.495;
}

.adjusted-c {
  font-size-adjust: 0.6;
}
```

正如你在下面的现场演示中看到的，较高的`font-size-adjust`值使字母变大，较低的值使字母变小。当`font-size-adjust`等于 aspect 值时，这些对的大小相等。

在 [CodePen](https://codepen.io) 上看到由 SitePoint ( [@SitePoint](https://codepen.io/SitePoint) )决定长宽值的笔[。](https://codepen.io/SitePoint/pen/YxxbMp/)