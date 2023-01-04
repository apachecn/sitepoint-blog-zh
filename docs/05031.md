# 如何用 HTML，CSS 和 jQuery 制作一个简单的图片滑块

> 原文：<https://www.sitepoint.com/making-simple-image-slider-html-css-jquery/>

幻灯片、图像滑块、神奇的图片变换框:不管你怎么称呼它们，这种模式在互联网上随处可见，以至于几乎每个网站都有。如果你是一个网站开发者，总有一天你会不得不自己建立一个网站。记住这一点，让我们看看如何使用 HTML、CSS 和 jQuery 构建一个简单的图像滑块。

## HTML

我们将从创建一个容器元素开始，它有一个类`container`。在那里面是我们的图像。图像用 div 标签包装，所以幻灯片可以变成链接，或者图像以外的内容可以用作幻灯片。第一个容器 div 有一些内联样式，确保滑块中的第一个图像在页面加载时可见。还有两个按钮元素，用于根据用户的请求手动切换幻灯片。

## CSS

这是我们设置图像滑块、容器 div 和其中图像的宽度的地方。值得注意的是，我们所有的容器 div 都被设置为`display: none;`。如果不是这样，我们所有的图像将同时可见。我们将使用 JavaScript 将容器 div 设置为`display: inline-block;`，而其余部分保持隐藏。

## JavaScript

首先，我们需要定义几个重要的变量。第一个变量是`currentIndex`，设置为 0。第二个变量是`items`，设置为`$('.container div')`。这将返回一个包含图片的容器 div 列表。第三个变量是`itemAmt`，它被设置为我们的`items`变量的长度。最后一个变量给出了图像滑块中幻灯片的总数。

接下来，我们创建一个名为'`cycleItems`'的函数。该函数将用于显示正确的图像，同时确保其他图像保持隐藏。在这个函数中，我们创建了一个名为`item`的变量。该变量被设置为`$('.container div').eq(currentIndex)`。jQuery 提供的`eq`方法接受一个整数，并将目标指向`$('.container div')`返回的第一个匹配该整数的条目。因此，如果`currentIndex`是 0，`$('.container div').eq(currentIndex)`将目标锁定在我们的图像滑块中的第一个图像容器。在 cycleItems 函数中要做的下一件事是隐藏所有的图像容器 div，并将`item`设置为`display: inline-block;`。

为了确保我们的图像自动旋转，我们需要提供一种方法，在经过一定时间后继续调用我们的 cycleItems 函数。我们通过创建另一个名为`autoSlide`的变量来做到这一点。这个变量存储一个 setInterval 函数，该函数需要 3000 毫秒的延迟，即三秒钟。在该函数中，我们将变量`currentIndex`加 1，这样`$('.container div').eq(currentIndex)`将总是引用下一个容器 div。然后我们定义一个非常重要的‘如果’语句。这条语句说，如果我们的`currentIndex`变量大于我们的滑块中的图像总量，将变量重置回零。如果没有这个 If 语句，我们就无法循环遍历图像列表。在 if 语句之后，我们调用 cycleItems 函数。

接下来，我们定义我们的上一步和下一步行动。这些定义了当我们点击“上一步”和“下一步”按钮时会发生什么。它们的工作方式类似于`autoSlide`功能，除了在点击时取消自动循环。要手动循环浏览幻灯片，单击下一个按钮会将`currentIndex`变量加 1，而单击上一个按钮会将`currentIndex`变量减 1。

## 演示

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看钢笔 [JS pen #1](https://codepen.io/SitePoint/pen/YqmjRj/) 。