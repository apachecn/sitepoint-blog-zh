# 5 个非常有用但很少使用的 CSS3 属性

> 原文：<https://www.sitepoint.com/5-extremely-useful-but-rarely-used-css3-properties/>

勤奋的设计师紧跟最新的网页设计和开发。站在网络的前沿可以给你的网页设计业务带来优势，甚至可以让你的设计工作变得更容易、更好。大多数网站设计者知道并使用 CSS 来创建漂亮的网站，这些网站对浏览器友好，并且在所有平台上都是一致的。话虽如此，有一些 CSS3 属性并不像你预期的那样经常使用。尽管它们很稀有，但非常有用。

### 多重背景

许多设计师没有意识到你可以在你的网站设计中实现多种背景。这可能有许多创造性和实际用途。想象一下，使用绝对定位将某些元素对齐到不同的边或角。如果使用得当，这可能会变成一种有趣的设计技术。

要实现多个背景，需要在 body 标签中定义它们，并用逗号分隔它们。指定背景选择器，然后指向 URL(通常在图像文件夹中)。我发现如果你在每张图片之后直接指定图片的方向，会更容易，也更容易理解。一旦指定了 *repeat* 样式，放置一个逗号，然后列出下一个 URL。您可以对其他图像遵循相同的公式，完成后，用分号结束语句，就像对任何其他 CSS 样式声明一样。

```
body {
background:url(images/saver.png) left top no-repeat, url(images/anchor.png) right top no-repeat, url(images/dock.jpg) center top no-repeat;
background-size: auto, auto, cover;
}
```

[![css3 Multiple Backgrounds](img/c395407660992b3bf5e94c762ad87ebf.png)](https://www.sitepoint.com/wp-content/uploads/2013/03/css3-multiple-backgrounds.jpg)

结果是这两个图像(救生圈和锚)被放置在浏览器的右上角和左上角。序列中的最后一幅图像放在底部。我使用了 *background-size* 属性向您展示了如何将多个背景与其他属性结合起来。你只要记得申报每件物品的尺寸就行了。锚和救生圈设置为*自动*。第三张图片(dock)设置为*封面*。由于它是最后一张图片，它将位于底部并充满浏览器窗口。其他两个图像将保持它们预期的大小，并保持在我们指定的位置。

### 过渡

随着 Flash 的衰落，一个最大的问题是 CSS 中缺少动画或过渡属性。好吧，这最终在 CSS3 中改变了。现在，您可以在元素的不同状态之间进行平滑过渡。您可以制作诸如文本框和文本阴影、位置、颜色变化等内容的动画。

```
#main {
width: 300px;
padding: 5px ;
background-color: #F00;
border-radius: 7px;
-webkit-transition: background-color 5s;
-moz-transition: background-color 5s;
-o-transition: background-color 5s;
-ms-transition: background-color 5s;
transition: background-color 5s;
}

#main:hover {
background-color: #06C;
}
```

[![css3-transition-before](img/633fdb8b1d52b8ff88f672cc1a56a8c4.png) ](https://www.sitepoint.com/wp-content/uploads/2013/03/Screen-shot-2013-03-20-at-10.28.05-AM.png) [ ![css3-transition-after](img/0832d7ea2f2c90dcb72a04575e2a1b2c.png)](https://www.sitepoint.com/wp-content/uploads/2013/03/Screen-shot-2013-03-20-at-10.28.24-AM.png)

结果是背景颜色从红色开始，这是我们指定的主 div 的正常状态。当我们将悬停状态设置为蓝色时，我们告诉背景颜色在 5 秒内从红色过渡到蓝色。这就产生了一个淡入淡出效果，在过渡的中间，这个框看起来是紫色的。CSS 创造了一个令人惊讶的从红色到蓝色的平滑过渡，不依赖任何插件或 JavaScript。您可以将过渡效果应用于许多不同的属性，如旋转、边距等等。这本质上是跨浏览器动画的基础。唯一要记住的是，你要在原始状态下设置转换，而不是悬停状态，这可能会让一些人感到困惑

### RGBa 吗

RGBa 是一种定义对象颜色的方法。有些人更喜欢十六进制值，但是使用 RGBa 方法的优点是能够利用等式的最后一个元素，即代表 alpha 透明度的 *a* 。这使您可以定义透明颜色，从而向设计中添加额外的元素，创建部分透明的部分。

RGBa 很好控制。r、G 和 B 是红色、绿色和蓝色值，就像 Photoshop 中的任何标准 RGB 颜色一样。要控制颜色的 alpha 或透明度，可以使用从 0.1 到 1 之间的任何值，1 表示 10%，1 表示 100%。在 CSS 中使用 RGBa 的格式如下所示:

```
background: rgba( 250,100,100, .5);
```

下面的方框是所有三种颜色的混合，这使得结果为红色/粉红色，最后的. 5 值将透明度设置为 50%。你可以看到这个盒子是透明的，因为你可以看到图像渗出。这会产生一些有趣的效果。

[![css3-rgba](img/e28441e30633a3c3ba9c67789fff3042.png)](https://www.sitepoint.com/wp-content/uploads/2013/03/css3-rgba.png)

### 内心阴影

内部阴影并不是 CSS 样式的真正名称，但是如果你熟悉 Photoshop 中的术语，它也有同样的效果。您使用 box-shadow 来定义阴影的参数，但是如果您指定要插入的阴影，则阴影会弹出到元素内部。我喜欢用它作为下拉菜单或导航菜单。这是一种方式，我可以增加深度和维度的菜单选择，而不必使用那些讨厌的，复杂的梯度。CSS3 渐变可能很难，因为你必须声明所有的后退和前缀。在下面的例子中，我给上一个例子中的透明 div 添加了一个嵌入框阴影。

```
-webkit-box-shadow: inset 0px 0px 8px 4px rgba(0, 0, 200, .7);
box-shadow: inset 0px 0px 8px 4px rgba(0, 0, 200, .7);
```

[![css3-box-shadow-inset](img/ae894f3209de9da67491dacdb5d0fdcc.png)](https://www.sitepoint.com/wp-content/uploads/2013/03/css3-box-shadow-inset.jpg)

结果是在透明的 div 上有一个蓝色的嵌入阴影。如果你想在你的 div 上得到真实的阴影，使用一个较低的透明度，比如 30%的透明度为 0.3，将颜色设置为灰色，这是所有三种颜色的一个相等值，或者黑色，这将创建一个更明确的阴影。

### 改变

变换可能是一个非常有用的属性，因为您可以创建一些非常规的形状和效果，如镜像。你可以打破盒子的模式，无论它们是圆形的还是方形的。这也允许你给你的作品添加维度，因为你可以使用 CSS3 中正确的参数在透视中变换对象。在下面的例子中，我将 div 旋转了 45 度。如果你想把一个 div 转向一边或者稍微旋转它，这是非常好的。请记住，您必须为每种浏览器类型指定前缀。

```
transform: rotate(45deg);
-ms-transform: rotate(45deg); /* IE 9 */
-webkit-transform: rotate(45deg); /* Safari and Chrome */
-o-transform: rotate(45deg); /* Opera */
-moz-transform: rotate(45deg); /* Firefox */
}
```

[![css3-transform](img/c4471d4afe94c5ced7fcb68f981d43e4.png)](https://www.sitepoint.com/wp-content/uploads/2013/03/css3-transform.jpg)

我将旋转添加到透明的 div 中(在移除了方框阴影之后)。结果是矩形旋转了 45 度。这是一个很好的效果，但是请注意“哇，看这个！”已经被旋转出屏幕。当在 CSS3 中转换元素时，您需要记住这一点。您可能会旋转或倾斜某些内容，使其中的文本无法阅读，或者超出了查看区域。

### 结论

向你的武库中添加更多的功能将允许你创建更有活力和更有趣的网站。他们将打破模式，让游客愿意来参观并与他人分享。增加一点互动大有帮助。动画不同的属性将增加一个惊喜和喜悦的元素。请记住不要过度使用过渡，因为过多的过渡可能会让人不知所措。

*你对这些 CSS3 属性有什么看法？你已经在你的网站设计中使用它们了吗？有没有另一个你认为应该上榜的未被充分利用的 CSS3 属性？在下面的评论区分享你的想法。*

## 分享这篇文章