# 无媒体查询的响应 CSS 模式

> 原文：<https://www.sitepoint.com/responsive-css-patterns-without-media-queries/>

![A variety of screens with different responsive images](img/9f5eaaa9aead57d8838e05b088de7b61.png)

首先让我说，尽管标题如此，这篇文章并不是要以任何方式消除媒体质疑或抨击媒体质疑。媒体查询非常有用，我一直用它们来做各种事情。然而，它们不能解决我们所有的响应式设计问题。

通常希望根据元素容器的尺寸而不是视口来改变元素的排列。为了解决这个问题，诞生了[元素查询](https://www.sitepoint.com/beyond-media-queries-time-get-elemental/)的概念。然而，元素查询还不是真正的*事物*，Mat Marquis 展示了这个概念的一些问题，并将它们重新表述为[容器查询](http://alistapart.com/article/container-queries-once-more-unto-the-breach)。不幸的是，这些还不是一件事情。

希望有一天会实现，但同时，我在这里介绍了一些技巧和技术，你可以用它们来解决容器查询有一天会解决的一些问题。

## 带`flex-wrap`的柔性盒

`Flex-wrap`在响应容器尺寸方面，可以解决许多问题。例如，如果有足够的空间，通常希望两个元素并排出现，但是如果没有足够的空间，就将一个元素堆叠在另一个元素的顶部。在下面的演示中，我们可以看到这种行为在起作用:

通过 [CodePen](http://codepen.io) 上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看 Pen [响应模块——flexbox](http://codepen.io/SitePoint/pen/vgQXZG/)。

这里没有花哨的技巧，只有带`flex-wrap`的 flexbox，但它很好用。当然，Flexbox 可以用于更多的事情，而不仅仅是创建两列，但是我在这个演示中保持事情简单。这项技术的基本部分很简单:

```
<div class="container">
  <div class="img">...</div>
  <div class="content">...</div>
</div>

...
```

```
.container {
  display: flex;
  flex-wrap: wrap;
  flex-direction: row;
}

.container .content,
.container .img {
  flex: 1 0 12em;
  /* Change 12em to your breakpoint. */
}
```

理解`flex-grow`、`flex-shrink`和`flex-basis`是正确理解的重要部分；我发现来自佐伊·吉伦沃特的这个 [flexbox 提示对于理解这些属性之间的关系非常有用。](http://www.creativebloq.com/advice/3-pro-tips-for-using-flexbox)

## Fab Four 技术

使用`width`、`min-width`、`max-width`和`calc`来创建基于断点的宽度开关——被称为“Fab Four 技术”——是[雷米·帕门蒂尔](https://medium.freecodecamp.com/the-fab-four-technique-to-create-responsive-emails-without-media-queries-baf11fdfa848#.dtpllw1lf)的创意。最初是为了帮助回复电子邮件而创建的，它可以很容易地用于普通的网页，并在创建自适应模块方面开辟了一些新的可能性，正如 Thierry 所展示的。举个例子，

```
{
  min-width: 50%;
  width: calc((25em - 100%) * 1000);
  max-width: 100%;
  /* Change 25em to your breakpoint. */
}
```

这是因为当`width`是一个百分比时，它是元素容器宽度的百分比。然后，`calc`函数将这个值与期望的断点进行比较，然后生成一个非常大的正数(如果宽度小于断点)，或者一个非常大的负数(如果宽度大于断点)，如果完全匹配，则生成零。大的正宽度由`max-width`限定，大的负宽度或零宽度设置为`min-width`的值。

所以，在上面的例子中，我们设置了一个断点`25em`。如果字体大小为`16px`，则解析为`400px`。如果容器是`400px`或以上(换句话说*等于或大于*断点)，宽度解析为 0 或大负数:
`(400 - 400 = 0) * 1000 = 0`或`(400 - 401 = -1) * 1000 = -1000`

有了这样的值，`min-width`开始生效，所以上例中元素的合成宽度将是`50%`。但是，如果容器小于`399px`(换句话说，小于断点的*)，则宽度解析为一个大的正数:
`(400 - 399 = 1) * 1000 = 1000`*

在这种情况下，`max-width`开始生效，最终宽度为`100%`。下图应该有助于形象化这一过程:

![Diagram visualizing the Fab Four Technique](img/a9cb96b7cf4a59bb366121003fabb765.png)

接下来的四个演示都以不同的方式使用这种技术来切换元素的宽度，以响应其容器的宽度。

## 浮动图像–全角/半角

在这个演示中，我使用了“Fab Four 技术”结合`float`根据容器的宽度在全角和半角之间切换图像:

见笔[响应模块——通过](http://codepen.io/SitePoint/pen/OWaRjg/) [CodePen](http://codepen.io) 上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )浮动。

类似于上面的`flexbox`例子，这种技术允许元素在小宽度的堆叠排列和浮动/包装排列之间切换，如果有足够的空间的话。

## 浮动图像-可见/隐藏

根据前面的技术，我反转了计算的结果并删除了`min-width`声明来创建一个开/关开关。这有助于将装饰元素隐藏在较小的容器中，因为它们可能会占用宝贵的空间:

通过 [CodePen](http://codepen.io) 上的 [@SitePoint](http://codepen.io/SitePoint) )查看笔[响应模块——浮动/隐藏](http://codepen.io/SitePoint/pen/VPVKMe/)。

为了清楚起见:

```
{
  /* Removed min-width since we want the width to be zero at this point and negative widths are treated as zero */
  /* Inverted the multiplier: */
  width: calc((25em - 100%) * -1000);
  max-width: 100%;
  /* Change 25em to your breakpoint. */
}
```

## 文本和图像–叠加/堆叠

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )叠加/堆叠的笔[响应模块。](http://codepen.io/SitePoint/pen/egQdGb/)

与前面的技术类似，我使用了一个额外的`div`来将文本拉至图像上方，但是如果图像太小，会被文本遮挡，那么文本会被拉至下方。这部分技术有点复杂，所以我将尝试阐明它。

```
.pull {
  /* Pull the text up over the image by this much: */
  margin-bottom: -10em;
}
```

这里，负边距将后续内容向上拉，使其覆盖图像。然而，当容器宽度越过断点时，我们需要关闭它，但是由于没有`min/max-margin`属性，我们不能在这里使用“Fab Four 技术”来实现这一点。

幸运的是，如果给定填充百分比，它就是容器宽度的百分比，而`padding-top`和`-bottom`将影响元素的*高度*。有了这些知识，我们可以使用`calc`来创建一个底部填充值，该值基于容器的宽度在零或“非常大”之间切换:

`padding-bottom: calc((30em - 100%) * 1000);`

我们不能将其直接应用于`.pull` div，因为没有`min/max-padding`属性来限制这些值；解决方案是在一个伪元素上放置填充开关来强制改变高度，并在`.pull`元素上使用`max-height`来将高度限制为与负边距相同的值，这样我们就可以有效地取消该边距。

```
.pull {
  /* Pull the text up over the image by this much: */
  margin-bottom: -10em;
  /* Don't allow this container to be larger than the same amount: */
  max-height: 10em;
  /* and hide any overflow, just to be on the safe side: */
  overflow: hidden;
}

.pull::before {
  content: "";
  display: block;
  padding-bottom: calc((30em - 100%) * 1000);
  /* Change 30em to your breakpoint */
}
```

通过将如前所述的“开/关”开关应用于已经应用了背景梯度的伪元素来实现覆盖梯度效果:

```
.image::after {
  content: "";
  display: block;
  position: absolute;
  left: 0;
  top: 0;

  /* Gradient to make the text more legible: */
  background-image: linear-gradient(to bottom, rgba(0,20,30,0) 0%,rgba(0,20,30,0) 50%,rgba(0,20,30,1) 100%);

  /* Extra .5% to prevent bleed due to rounding issues: */
  height: 100.5%;
  /* Toggle gradient overlay at the same breakpoint as the 'pull': */
  width: calc((30em - 100%) * -1000);
  /* Change 30em to your breakpoint */
  max-width: 100%;
}
```

## 截断列表

我开发的最后一项技术受到了 CSS 技巧上的 [Priority Plus 模式的启发。虽然没有那么复杂，但这个不需要任何 JavaScript:](https://css-tricks.com/diy-priority-plus-nav/)

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看笔[截断列表](http://codepen.io/SitePoint/pen/GrwjOr/)。

同样，这使用了“Fab Four 技术”，只是这次是基于容器的高度，而不是宽度。

```
<div class="outer">
  <div class="inner">
    <div class="item">...</div>
    ...
    <div class="control">...</div>
  </div>
</div>
...
```

```
.outer {
  height: 2.25em;
  overflow: hidden;
}

.outer:target {
  height: auto;
}
```

外部容器有一个固定的高度，除非元素被`:target`编辑，否则隐藏任何溢出。

```
.inner {
  display: flex;
  flex-wrap: wrap;
}
```

内部容器是一个打开了`flex-wrap`的 flex 容器，所以当元素换行时它的高度会增加，但是第一行以下的元素会被`.outer`容器的`overflow:hidden`隐藏，从而产生截断效果。

```
.control {
  height: calc((2.25em - 100%) * -1000);
  max-height: 2.25em;
}

:target .control--open {
  display: none;
}

:target .control--close {
  display: block;
}
```

只有当容器的高度超过断点(与主链接的高度相同)时，‘*多/少*’控件才可见，`:target`状态决定了哪个控件可见。

## 在 CSS 中智能对齐文本

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看笔[响应文本 Align](http://codepen.io/SitePoint/pen/EZOgbB/) 。

根据容器中相对于文本长度的可用空间，将文本居中对齐或靠左对齐是一件非常有用的事情。这项技术是由 Vijay Sharma 创造的，它让实现这一点变得非常容易。

## 奖励:Flex-grow 9999 Hack

Joren Van Hee 的一个非常适合这个系列的技巧是 flex-grow 9999 hack 。

## 赞:看，没有媒体提问

Vasilis van Gemert 的演讲[看，没有媒体询问](https://vimeo.com/160593680)为我提供了研究无媒体询问响应设计的动力，这反过来又促使我写了这篇文章。他的演讲很值得一看，包括一些其他的想法，虽然很有用，但不太适合我在这里展示的主题。

## 结论

没有元素/容器查询，很多事情都无法完成。比如颜色值、字体大小和行高、边框和框阴影、边距和填充等等——清单很长。使用元素/容器查询来调整所有这些以响应父容器的状态应该是可能的，但是，唉，没有任何迹象表明这将很快成为现实。然而，我希望与此同时，我在这里介绍的一些内容会对你有用。

如果你想知道用元素查询*设计*会是什么样子，[今天使用 EQCSS](https://www.sitepoint.com/eqcss-a-javascript-plugin-to-write-element-queries/) 编写元素查询将帮助你开始。

如果您想了解更多关于元素/容器查询的信息，这里有一些有用的资源:

*   用于元素查询的 CSS 扩展&更多
*   [元素查询为何重要](http://hugogiraudel.com/2014/04/22/why-element-queries-matter)
*   [集装箱查询:再次突破](http://alistapart.com/article/container-queries-once-more-unto-the-breach)
*   [寻找圣杯:我如何结束了元素查询，以及你今天如何使用它们](https://www.smashingmagazine.com/2016/07/how-i-ended-up-with-element-queries-and-how-you-can-use-them-today)
*   [元素查询用例及需求编辑稿](https://responsiveimagescg.github.io/cq-usecases)
*   [容器查询用例](https://justmarkup.com/log/2016/02/use-cases-for-container-queries)
*   [样式范围如何处理元素查询](http://codepen.io/tomhodgins/post/how-style-scoping-works-with-element-queries)