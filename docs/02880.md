# 快速提示:修正悬停状态下的字体粗细问题

> 原文：<https://www.sitepoint.com/quick-tip-fixing-font-weight-problem-hover-states/>

你曾经尝试过改变链接状态的`font-weight`属性的值吗？如果是，您可能已经注意到文本发生了变化。

在这个快速技巧中，我们将首先了解是什么导致了这种不希望的行为，然后我们将研究两种可能的解决方案。

## 识别问题

作为第一步，让我们通过看一个简单的例子来确定问题。考虑以下可视化:

![Menu example](img/d0452f532f48443e209e63936cdc77d3.png)

这些项目是链接，所以在你的 CSS 中你可以尝试这样的东西:

```
a:hover {
  font-weight: bold;
}
```

因为我们通常不会为这样的项目定义固定的宽度，所以当用户悬停在链接上时，文本会移动。当然，这是因为悬停时每个链接的宽度变大了。为了测试它，我们将`active`类分配给第一个链接，并添加这个 CSS 规则:

```
.active {
  font-weight: bold;
}
```

最后，我们将第二个链接的文本改为与第一个链接相同，如下所示:

![Font-weight on hover](img/8665b34ec1d29ee41e35084752e29e86.png)

现在，如果我们打开浏览器控制台并定位前两个链接，我们会注意到它们的计算宽度是不同的。

您可以在下面的 CodePen 演示中看到我们在上面讨论的内容:

通过 [CodePen](http://codepen.io) 上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )在悬停状态上使用 font-weight 属性查看笔[。](http://codepen.io/SitePoint/pen/JXdOVm/)