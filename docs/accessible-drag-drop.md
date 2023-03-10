# 多个项目的可访问拖放

> 原文：<https://www.sitepoint.com/accessible-drag-drop/>

在本文中，我将向您展示如何扩展 <abbr title="HyperText Markup Language Version 5">HTML5</abbr> 拖放的功能——以便它可以处理多个元素，并支持键盘交互，适合视力正常的用户和屏幕阅读器用户。

*我假设你已经对拖放 <abbr title="Application Programming Interface">API</abbr> 非常熟悉，但是如果没有，看看这篇[介绍文章](http://www.html5rocks.com/en/tutorials/dnd/basics/ "Native HTML5 Drag and Drop (html5rocks.com)")(忽略对 Modernizr 的引用，你不需要它)。*

拖动多个元素的基本方法实际上非常简单——我们只需要记住多个元素的拖动数据。我们可以使用`dataTransfer`对象来实现，或者我们可以只使用一个单独的数组(这就是我们将要做的)。那么为什么要写一整篇文章呢？

为什么——因为尽管数据很简单，但界面要复杂得多。

我们需要做一些额外的工作来**实现一个预选机制**，我们需要**让它从键盘**工作(因为本地拖放不支持这个)。

注意:本文不会涉及触摸事件，也不会为老版本的浏览器提供多种填充。此外，我们采用的方法只能在单个页面中工作，它不支持在窗口之间拖动。尽管拖放 API 确实支持这一点，但是没有直接的方法使它可以通过键盘访问。

## 基本拖放

让我们从一个功能示例开始，它定义了基本的拖放事件，允许用鼠标在两个容器之间拖动单个元素:

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看到笔[基本拖拽](http://codepen.io/SitePoint/pen/gbdwjj/)。