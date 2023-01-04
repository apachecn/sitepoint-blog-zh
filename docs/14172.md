# CSS 和圆角:用曲线构建方框文章

> 原文：<https://www.sitepoint.com/css-round-corners-boxes-curves/>

设计师在网页上使用图像的一个主要原因是为了创造曲线和圆角。目前不可能使用纯 HTML 创建任何类型的曲线形状，所以需要应用图像。

问题？将这些图像放入带有表格布局的 HTML 文档中会产生大量多余的代码。在本教程中，我们将使用 CSS 来创建[这个有圆角](https://www.sitepoint.com/examples/roundcorners/box-corners8.htm)的盒子，看不到`<img>`标签！

##### 它是如何工作的

上面基本上是一个普通的盒子，有一个橙色的背景，上面叠加了四个角的图像。所有这些图像都是通过 CSS 命令调用的。

所以，我们从下面的片段开始:

```
<div class="bl">Lorem ipsum dolor sit amet consectetur adipisicing elit</div>
```

我们使用了`class="bl"`,因为我们将通过 CSS 命令将右下角分配给这个`<div>`。作为一个规则，你只能用 CSS 给一个 HTML 标签分配一个背景图像，所以这是我们给这个`<div>`分配的唯一图像。

![1465_1bl](img/79cbb98874c956944e15989225f8085d.png)

我们将使用上面的图片，名为 bl.gif，我们用下面的 CSS 命令将它放在左下角:

```
.bl {background: url(bl.gif) 0 100% no-repeat; width: 20em}
```

CSS 背景命令分为三个部分:图像 URL、它的位置和重复命令。我们还插入了一个 width CSS 命令，这样这个框就不会覆盖整个屏幕。让我们依次检查三个后台命令:

1.  **图片 URL** —记住，图片是通过 CSS 调用的，所以图片的路径必须是来自 CSS 文档的路径，而不是 HTML 文档。
2.  **图像位置** —在这个例子中，我们在 CSS 规则中使用了命令`0 100%`。第一个数字表示距`<div> ;`左边缘的距离，第二个数字表示距顶边缘的距离。在这种情况下，使用了`%`，但也可以使用不同的距离值，如 em 或 px。如果省略该命令，将使用默认值`0 0`，图像将被放置在左上角。
3.  **重复命令** —显然，我们不希望这个图像重复，所以我们插入了`no-repeat` CSS 命令。如果我们愿意，我们可以使用`repeat-x`，水平重复图像，`repeat-y`，垂直重复图像，`repeat`，水平和垂直重复图像。如果省略这个命令，将使用默认值`repeat`。

这三个 CSS 后台命令的放置顺序并不重要。[我们带有曲线的盒子现在看起来像这样](https://www.sitepoint.com/examples/roundcorners/box-corners3.htm)。

##### 右下曲线

接下来，我们将坚持右下角的曲线。如前所述，我们只能给 CSS 中的每个`<div>`分配一个背景图像，所以我们需要插入一个新的`<div>`:

```
<div class="bl"><div class="br">
Lorem ipsum dolor sit amet consectetur adipisicing elit
</div></div>
```

下面是我们将使用的图像:

![1465_1br](img/0325ec9cdd59be6f10510d34b321e7f6.png)

将右下角的图像命名为 br.gif，我们将插入一个新的 CSS 规则:

```
.br {background: url(br.gif) 100% 100% no-repeat}
```

这个 CSS 规则本质上和上一个是一样的，尽管现在我们已经把左边的位置变成了 100%,而之前是 0%。[盒子现在看起来像这样](https://www.sitepoint.com/examples/roundcorners/box-corners4.htm)。

##### 顶部曲线

为了制作顶部曲线，我们需要两张新图像:

![1465_1tl](img/2eaeaa4d0fce49bca4aa1663b744ce15.png)

![1465_1tr](img/ec16cdb3f83e93dc87776a71e5209b2b.png)

我们将它们命名为 tl.gif 和 tr.gif。我们需要为它们创建两个新的`<div>`:

```
<div class="bl"><div class="br"><div class="tl"><div class="tr">
Lorem ipsum dolor sit amet consectetur adipisicing elit
</div></div></div></div>
```

新的 CSS 规则如下:

```
.tl {background: url(tl.gif) 0 0 no-repeat}
.tr {background: url(tr.gif) 100% 0 no-repeat}
```

这些给了我们[这个显示](https://www.sitepoint.com/examples/roundcorners/box-corners5.htm)。

##### 背景颜色

我们现在将我们的橙色背景颜色插入到盒子中，以实现整个圆角效果。

背景色必须总是分配给第一个 CSS 规则。这是因为每个 CSS 背景规则本质上都是在前一个规则之上的一个层。因此，在本例中，我们的分层顺序是 br.gif、bl.gif、tl.gif，然后是 tr.gif。但是，在本例中，图像没有重叠，因此我们并没有真正注意到这种分层效果。

默认情况下，背景色覆盖整个`<div>`,并将覆盖任何其他先前分配的背景图像和/或颜色。因此，如果我们将橙色放在第一张图片之外的任何一张`<div>`上，它将被放在前面的图片之上，并基本上使它们消失。因此，我们必须将橙色背景色(#e68200)放在第一个 CSS 规则中:

```
.bl {background: url(bl.gif) 0 100% no-repeat #e68200; width: 20em}
```

和以前一样，我们在 CSS 背景规则中把这个颜色命令放在哪里并不重要。我们的盒子现在看起来像这样。

##### 填料

需要填充以防止文本重叠到图像上，图像大小为 10px x 10px。因此，我们需要 10px 的文本填充。但是我们应该给哪个`<div>`分配填充 CSS 规则呢？有关系吗？嗯，是的。

无论我们将填充分配给哪个元素，其中的每个元素都将继承该填充。如果我们给第一个`<div>`、`<div class=""bl">`、[分配填充，我们会得到这个效果](https://www.sitepoint.com/examples/roundcorners/box-corners7.htm)。

为了让这个填充正常工作，我们需要将它分配给最后一个`<div>`，`<div class="bl">`:

```
.tr {background: url(tr.gif) 100% 0 no-repeat; padding:10px}
```

下面是它现在的样子。

##### Internet Explorer 问题

你可能已经注意到底角在顶角之前被调用。如果我们反过来做事情，也就是说，首先调用顶部的角落，橙色背景颜色的一些部分将会在底部曲线下偷偷溜走，造成相当难看的效果。调换一下`<div>`的顺序，你自己看看。

Internet Explorer 中的另一个问题是，框的背景颜色有时会与下面的元素重叠，再次导致不美观的效果。这可以简单地通过在圆角框下放置一个小占位符来解决。在第四次关闭`</div>`后，立即插入以下 HTML:

```
<div class="clear">&nbsp;</div>
```

现在，给它分配这个 CSS 规则:

```
.clear {font-size: 1px; height: 1px}
```

##### 最终代码

我们完成的 HTML 现在看起来像这样:

```
<div class="bl"><div class="br"><div class="tl"><div class="tr">
Lorem ipsum dolor sit amet consectetur adipisicing elit
</div></div></div></div>
<div class="clear">&nbsp;</div>
```

下面是实现这一切的 CSS:

```
.bl {background: url(bl.gif) 0 100% no-repeat #e68200; width: 20em}
.br {background: url(br.gif) 100% 100% no-repeat}
.tl {background: url(tl.gif) 0 0 no-repeat}
.tr {background: url(tr.gif) 100% 0 no-repeat; padding:10px}
.clear {font-size: 1px; height: 1px}
```

## 分享这篇文章