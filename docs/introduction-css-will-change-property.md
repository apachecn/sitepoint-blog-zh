# CSS will-change 属性介绍

> 原文：<https://www.sitepoint.com/introduction-css-will-change-property/>

在本文中，我们将了解一下 [CSS `will-change`属性](http://dev.w3.org/csswg/css-will-change/)，它是什么，何时以及如何使用它，以及*如何不*使用它。关于`will-change`有相当多的技术文档可用，所以我将在本文中引用大量这些资源，并在最后将它们汇编成一个列表。本文将通过一些快速示例来概述重要的部分。

## 一些背景

今天，许多前端开发人员正在利用 CSS3 过渡、转换和动画来添加一个新的应用程序交互层，以前这是由 JavaScript 库或 Flash 完成的。现在，我们能够用最少的 CSS 实现流畅而美丽的动画。如果您一直在试验和利用这些 CSS3 属性，您可能会遇到术语 CPU、GPU 和硬件加速。让我们快速分解这些术语:

1.  CPU，或称中央处理器，是处理几乎所有计算机操作的硬件。它也被称为主板。
2.  GPU 或图形处理单元是与处理和渲染图形相关的硬件。GPU 旨在执行复杂的图形计算，并减轻 CPU 的一些严重的处理负担。
3.  硬件加速是一个通用术语，用于将 CPU 进程卸载到另一个专用硬件上。在 CSS 过渡、变换和动画的世界中，这意味着我们将这个过程卸载到 GPU 上，因此加快了它的速度。这是通过将元素推到它自己的层来实现的，在该层中，它可以在经历动画的同时被独立地渲染。

这如何提高动画的性能和质量？首先，在基于 WebKit 的浏览器上，我们可能经常会在执行一些 CSS 操作时看到闪烁，即 2D 变换和动画。过去，我们通过欺骗浏览器来解决这个问题。我们会让浏览器执行 3D 转换，从而将操作卸载到 GPU 上。这是因为 3D 变换会自动移到那里。这让我们想到了这样的诡计:

```
.accelerate {
  -webkit-transform: translate3d(0, 0, 0);
}
```

有一些类似的伎俩，但在大多数情况下，这涵盖了我们。然而，这是一个漏洞，如果使用正确的话,`will-change`属性将会给我们很大的帮助。我们来调查一下。

## 什么是`will-change`？

根据 [W3C 编辑的草稿](http://dev.w3.org/csswg/css-will-change/):

> CSS 属性……允许作者提前通知 UA 他们可能对某个元素做什么样的修改。这允许 UA 提前优化它们处理元素的方式，在动画实际开始之前执行准备动画的潜在昂贵的工作。

这意味着，我们现在可以使用一个专用属性来通知浏览器关注未来的变化，并相应地优化和分配内存，而不是通过 3D transform hack 将我们的变换强加到 GPU 上。听起来很不错，对吧？在我们兴奋之前，我们需要了解如何使用`will-change`。

## `will-change`是怎么用的？

`will-change`属性仅在特定时间使用时有效。我们不能将类似于`will-change: transform`的东西应用到已经转换的元素上——这没有意义。 [Sara Soueidan 在 Opera 博客](https://dev.opera.com/articles/css-will-change-property/)上详细讨论了这个问题，所以一定要去看看，但是要注意的要点是:

1.  我们感兴趣的是使用这个属性让浏览器提前**知道**元素可能会发生什么变化。
2.  这允许浏览器提前进行适当的优化，最终导致更快更快的渲染。
3.  如前所述，浏览器需要提前知道会发生什么变化。这意味着我们必须提前考虑何时为浏览器的变化做准备。

考虑到以上几点，让我们来看一个没有效果的例子:

```
.will-change:active {
  will-change: transform;
  transition: transform 0.3s;
  transform: scale(1.5);
}
```

在这里，当我们通知浏览器时，已经发生了变化，完全抵消了`will-change`属性的全部意义。如果我们想让浏览器提前知道什么时候会有变化，我们必须在合适的时候通知它。

一个元素要达到一个`active`状态，它必须首先被悬停。我们可以这样做:

```
.will-change {
  transition: transform 0.3s;
}

.will-change:hover {
  will-change: transform;
}

.will-change:active {
  transform: scale(1.5);
}
```

这个小例子让我们对正确使用`will-change`属性所需的思考过程有了一些了解。但是在我们继续更多的例子之前，我们需要注意一些重要的考虑事项。

从本质上来说，浏览器会尽最大努力进行优化，并为变化做好准备。为了释放内存，浏览器会尽快删除某些优化。但是，直接在元素上使用`will-change`属性会让浏览器坚持认为所讨论的元素总是非常接近于变化。这迫使浏览器保持优化，从而增加了内存消耗。考虑到这一点，我们需要在适当的时候添加和删除`will-change`属性。第一个例子为我们做到了这一点，因为属性只有在悬停时才会被添加。但是，如果我们希望转换实际上发生在悬停时呢？您可能会尝试这样做:

```
.will-change {
  will-change: transform;
  transition: transform 0.3s;
}

.will-change:hover {
  transform: scale(1.5);
}
```

这将导致内存消耗的增加，因为我们迫使浏览器认为元素随时都会被改变。我们可以通过在父容器上查找悬停事件来解决这个问题:

```
.will-change-parent:hover .will-change {
  will-change: transform;
}

.will-change {
  transition: transform 0.3s;
}

.will-change:hover {
  transform: scale(1.5);
}
```

这将在鼠标进入父容器时添加优化，并在鼠标离开时删除它。然而，这也意味着每次鼠标进入父容器时，浏览器都可以预期元素的变化。事实并非如此，这是一个非常清楚的例子，说明滥用这种属性是多么容易。

构建全屏幻灯片或某种 CSS 活页本是直接应用程序可以工作的一个例子。幻灯片总是需要更改，所以做如下事情可能比较合适:

```
.slide {
  will-change: transform;
}
```

## 始终移除`will-change`

当你使用完`will-change`属性时，一定要记得移除它。正如我上面提到的，浏览器优化是一个昂贵的过程，所以如果使用不当，可能会产生负面影响。我们可以用 JavaScript 来处理这个问题，我们将参考来自 MDN 的脚本[来构建一个粗略的例子。假设我们有一个类为`element`的元素，当我们点击它时，它被转换。我们的 CSS 可能看起来像这样:](https://developer.mozilla.org/en-US/docs/Web/CSS/will-change#Examples)

```
.element {
  transition: transform 0.5s;
}

.element.clicked {
  transform: scale(1.5);
}
```

如果我们想通知浏览器为将要发生的改变准备优化，以响应对元素的点击，我们可以这样做:

```
var el = document.querySelector('.element');

el.addEventListener('mouseenter', hintBrowser);
el.addEventListener('animationEnd', removeHint);

function hintBrowser() {
  this.style.willChange = 'transform';
}

function removeHint() {
  this.style.willChange = 'auto';
}
```

当然，当元素被实际点击时，您需要包含必要的 JavaScript 来添加正确的`clicked`类，但是上面的脚本应该让您了解如何准备浏览器进行更改，以及如何在更改完成后释放内存。当鼠标进入元素时，会添加`will-change`属性。当它被点击并且转换发生时(通过你相应的脚本)，动画事件将被发送出去。在`animationEnd`上，我们将删除`will-change`属性，再次释放内存。

## 浏览器支持

`will-change`属性是一个相当新的属性，根据[我可以使用](http://caniuse.com/#search=will-change)吗？

*   Chrome 36 及以上版本
*   Firefox 36 及更高版本
*   Opera 24 及以上
*   安卓浏览器 37
*   Android 40 的 Chrome 浏览器
*   歌剧移动 24

在撰写本文时，Internet Explorer 是唯一缺失的主要浏览器，而`will-change`被[列为考虑中的](https://status.modern.ie/csswillchange)。

## 最终注释和来源

CSS `will-change`属性的好处和坏处一样多。这个声明并不意味着阻止你使用它，而是作为一个建议。没有理由不在您的现代应用程序构建中考虑它，但是要注意我已经指出的所有潜在问题。我希望您对浏览器优化的广阔世界有所了解，并能将这些知识应用到您未来的项目中。

有几篇文章详细介绍了硬件加速和`will-change`属性。我建议你也仔细阅读它们，以巩固你对 CSS 的理解。

*   CSS 将改变 W3C 上的模块级别 1
*   Sara Soueidan 的文章
*   [CSS 将在 MDN 上更改](https://developer.mozilla.org/en-US/docs/Web/CSS/will-change)
*   [html 5 岩石上的高性能动画](http://www.html5rocks.com/en/tutorials/speed/high-performance-animations/)

## 分享这篇文章