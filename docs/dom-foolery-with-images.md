# 有图像的“DOM-foolery”

> 原文：<https://www.sitepoint.com/dom-foolery-with-images/>

冒着向那些阅读设计视图的人重复我自己的风险，我想我应该从昨天的问题张贴这个小想法。

本周，我们在一个优雅的小网站设计中遇到了一个小挑战——演示文稿要求所有的“内容图像”都有圆角。

在过去，没有太多的选择。你在 Photoshop 或 Fireworks 中制作了一个漂亮的取景模板，将你的图片放在它后面，然后导出你的新图像。

这种方法的问题是，虽然图像本身被认为是“内容”，但圆角是一种“设计奇想”，只与当前的外观和感觉相关。如果客户决定下一次重新设计应该更加光滑和锐利，我们就需要重新创作每张图片，或者只是苦笑着忍受——不是好的选择。

那么，有哪些替代方案呢？事实证明，即使是 CSS3 或 Mozilla 特有的风格也不能给我们提供太多帮助。Moz 可以在角落后面做曲线边框——看起来不错，但不是我们想要的。Safari 可以保存多种背景，但在其他地方用处不大。对我来说，唯一可行的解决办法似乎有点“愚蠢”。

首先，我们需要想出一个可重复的方法来获得效果，暂时先不考虑声音标记技术。

由于图像是前景元素，我们需要将我们的“角图形”悬浮在图像上。这意味着我们需要用每个图像自己的“相对定位”DIV 来包装它，然后用这个“包装器”来定位我们的“绝对定位”角。

正如您在图中看到的，包装 DIV 被称为。wrapper '并需要相对定位，向左或向右浮动，以迫使它紧紧包围我们的图像。

```
div.wrapper {
  position:relative;
  float:left;
}
```

然后，我们的四个内部 div 被设置为'*位置:绝对*'，这将自动将它们带到我们图像的前面。每个都有相同的宽度和高度(7x7px——角落 gif 的尺寸),但位置和背景图形不同。大概是这样的:

```
div.wrapper div{    /* set all the inner DIVs */
    position:absolute;
    width: 7px;
    height: 7px;
}
div.wrapper div.tl{
    background:transparent url(tl.gif) top left no-repeat;
    left:0;
    top:0
}
div.wrapper div.tr{
    background:transparent url(tr.gif) top right no-repeat;
    right:0;
    top:0
}

div.wrapper div.bl{
    background:transparent url(bl.gif) bottom left no-repeat;
    left:0;
    bottom:0
}
div.wrapper div.br{
    background:transparent url(br.gif) bottom right no-repeat;
    right:0;
    bottom:0
} 
```

将标记与 CSS 结合起来，它就很好地结合在一起了。

好吧，现在，这一切都很好，但显然我们不能到处精美地手工包装每一个新图像，就像小“虚拟寿司卷”一样。我们需要一个漂亮的小函数来替我们做脏活。

在我们的例子中，我们已经决定只针对指定的' *DIV#content* '内的图像 CMS 的可编辑内容区域。然而，修改脚本以针对所有图像并不难，或者如果需要的话，只针对那些具有特定类别的图像。

我将快速浏览一下脚本，但是如果你感兴趣的话，这个例子有很好的注释

首先，我们将声明一个新函数(roundedImages)，获取#content 中的所有元素，然后过滤掉除图像之外的所有内容。他们正等着我们在一个“imgs[]”数组中使用。

```
function roundedImages() {
var content = document.getElementById ('content');
var imgs = content.getElementsByTagName ('img');

```

接下来，我们开始一个循环来处理我们的图像列表。我们创建一个全新的 DIV，将“wrapper”类附加到它上面，选择第一个图像，并用我们的新 DIV 替换它(现在)。

```
for (var i = 0; i < imgs.length; i++) {
var wrapper =
document.createElement('div');
wrapper.className = 'wrapper';

var original = imgs[i];
original.parentNode.replaceChild
(wrapper, original); 
```

现在我们需要将我们的四个角 div 注入到文档中，并为每个添加适当的类。代码相当简单。

```
var tl = document.createElement('div');
   tl.className ='tl';
   var br = document.createElement('div');
   br.className ='br';
   var tr = document.createElement('div');
   tr.className ='tr';
   var bl = document.createElement('div');
   bl.className ='bl';

```

好吧，我们有我们的角 div，但他们实际上不在文件中。我们可以使用' *appendChild* '将它们粘贴到我们的包装器 DIV 中。为了完成循环，我们将原始图像粘回到包装器中；然后，我们准备捕捉下一个图像。

```
 wrapper.appendChild(tl);
  wrapper.appendChild(tr);
  wrapper.appendChild(bl);
  wrapper.appendChild(br);

   wrapper.appendChild(original);
  }
}
```

最后，我们在页面加载时运行该函数。

```
window.onload = roundedImages;

```

仅此而已。用你的 CSS 将脚本放入你的中，你就像一个 INDY500 驱动程序一样转弯了。[你可以在这里看到它的作用](https://www.sitepoint.com/examples/rounded_images/ "Example: rounding corners on images with the DOM")。

据我所知，它在 IE5+、Moz1+、Safari 和 Opera 8.0 中运行得非常好。Opera 7 好像有个 bug 我还没想出来。

当然，这是第一代实验，有一些局限性。需要浮动 DIV.wrapper' ,以紧密包围图像，这意味着您的图像也将始终显示为浮动的。然而，通过让脚本提取每个图像的尺寸，然后使用它们来手动调整包装器的大小，很有可能消除这个需求。

如果脚本能够将图像附带的任何样式信息(尤其是对齐数据)传递给包装器，允许用户选择向左或向右浮动图像，那就更好了。现在，我们要求客户端的内容中有右浮动的图像。

IE6 中似乎还有一个古怪的 bug(不！*绝不！*)会导致宽度和高度均为奇数的图像右侧和底部出现 1px 的错位。完全奇怪。这似乎是一个舍入误差(没有双关语)，目前，我能提供的最佳解决方案是使用偶数图像尺寸。如果你有更好的 IE 巫毒，我们当然很想听听。

不然看起来还挺扎实的。当然，它速度快，降级优雅，在这个最简单的主题上有无穷无尽的潜在变化。

我对一些变化有一些想法，我可能会在以后的帖子中采用，但如果你觉得有用，请告诉我。

<ins datetime="2005-12-15T01:24:57+00:00">PostScript</ins>

根据约书亚·潘恩的一些很好的建议，我们对这个剧本做了一些小的改进。

*   首先，IE 中的一个舍入错误使带有“`right:o`”的角根据浏览器宽度的不同，移动了 1 个像素)。修复方法是允许右上角和右下角伸展包装器的整个宽度，并将它们的背景图像改为与右侧对齐。为此，我们需要使用 DOM 将每个包装器的宽度“强制设置”为它所包含的图像的宽度——最初我们没有指定它的宽度
*   想要链接图像是很常见的。这导致了“树的重新排序”,东西不再在它预期会导致错误的地方。我们已经添加了一行代码，使脚本逐步跳过链接。

这两个建议[现在都被合并到示例](https://www.sitepoint.com/examples/rounded_images/)中。谢谢约书亚。

## 分享这篇文章