# AtoZ CSS 快速提示:浮动、清除和居中元素

> 原文：<https://www.sitepoint.com/atoz-css-quick-tip-float-and-clear/>

购买 SitePoint Premium 会员资格将使您能够访问完整的 [AtZ: CSS 系列](https://www.sitepoint.com/premium/courses/atoz-css-2944)。

欢迎来到我们的 AtoZ CSS 系列！在这个系列中，我将探索不同的 CSS 值(和属性),每个值都以字母表中不同的字母开始。在本文中，我添加了一个新的快速技巧/课程，关于 Float 和 Clear 属性，以及如何以各种方式使元素居中。

![F2b-01](img/ff84e53caa4582f55697e51cd2f344f4.png)

## f 代表浮动和清除

如果你想把一个元素移动到页面的左边或右边，浮动是很好的，但是不幸的是，你不能做`float: center`来使一个元素居中。多痛苦啊，对吧？

好吧，不要害怕，这是集中各种元素的诀窍。

### 提示 1

如果元素是块元素，可以组合`width`和`margin: auto`。

`margin: auto`将自动计算左边和右边的边距，使它们相等，将块在其包含的元素内居中。

通过 [CodePen](http://codepen.io) 上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看笔 [tip-margin-auto](http://codepen.io/SitePoint/pen/gwaErx/) 。

### 提示 2

如果元素是内联的(或者内联块)，在父容器上使用`text-align: center`。

通过 [CodePen](http://codepen.io) 上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看 Pen [tip-flexbox](http://codepen.io/SitePoint/pen/RGWdGp/) 。

### 提示 3

如果元件处于绝对位置，组合`left`和`transform`使元件水平居中。

通过 [CodePen](http://codepen.io) 上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看 Pen [tip-flexbox](http://codepen.io/SitePoint/pen/vXNPyx/) 。

类似的技术也可以用来垂直居中元素，但是在以后的技巧中会有更多的介绍！

### 提示 4

使用 flexbox(另一个 F 属性，耶！)

要使用 flexbox 将单个项目(或一组项目)居中，您需要在包含元素上设置两个属性:`display: flex`和`justify-content: center`。

通过 [CodePen](http://codepen.io) 上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看 Pen [tip-flexbox](http://codepen.io/SitePoint/pen/bwVkBr/) 。

你可以用 flexbox 做很多其他很酷的事情，包括扩大或缩小一个包含元素，以最好地利用可用空间。您甚至可以垂直和水平对齐一个元素，而不是像 blocking 和 inline 那样要求垂直或水平对齐。

使用 flexbox 的另一个好处是不再有容器折叠的问题，也不再需要使用 clearfix 解决方案。