# 你认为回复短信只是一种时尚？

> 原文：<https://www.sitepoint.com/thought-responsive-text-just-fad/>

是的，我们在这里讨论的是文本。网页设计中不那么迷人也经常被忽视的部分。当每个人都在谈论响应式设计、图标、转换等等的最新时尚时，不起眼的文本经常被忽略。我不怪他们。真正关注它的只有排版极客，或者内容作者。但也许是时候改变这种状况了。不管你喜不喜欢，普通的旧文本可以成就或者毁掉你的网站。

> “读者=收入”
> –[D·布农·滕南特](http://www.smashingmagazine.com/2011/10/07/16-pixels-body-copy-anything-less-costly-mistake/)

很多网站的目的都是赚钱，不管是直接的还是间接的。网站是一种商业资产。它需要产生良好的投资回报。每一个元素都应该以此为目标。为了实现销售，网站需要告诉人们该做什么，为什么他们应该这样做，并让他们很容易实际去做。

怎样才能让人们理解这个信息呢？除了罕见的设计天才之外，这都是通过文本完成的。除非你是谷歌，否则你真的离不开纯白的屏幕和搜索框。

## 不管怎样，这真的都是关于文本的

网站访问者中有很大一部分来自移动设备(根据 StatCounter 的数据，截至 2014 年 9 月，约 36%的访问者来自移动设备)，难怪每个人都在创建响应式设计。然而，大多数响应式设计仍然侧重于流体网格、灵活的图像和正确的媒体查询。最多，一些设计有一些媒体查询，以减少小屏幕的默认文本大小，使文本适合屏幕。小字体，在小屏幕上。你可以想象阅读是多么有趣。

> “网页设计 95%是字体设计”
> –[奥利佛·雷琴斯坦](http://ia.net/blog/the-web-is-all-about-typography-period)

在移动设备上，你真的没有奢侈的屏幕空间。你不能让花哨的设计元素和按钮塞满你的屏幕。折叠上方的空间有限。你想让它产生影响。唯一的方法就是通过文本。

如何确保文本不仅可读，而且令人愉快，尤其是在小屏幕上？仅仅调整文本容器的大小并使文本重排是不够的。真正的响应式设计需要的不仅仅是这些。毕竟，目标不仅仅是在屏幕上显示一些东西，而是确保内容在可读性、可用性、可访问性和整体图形平衡方面得到优化。

## 良好可读性指南–在移动设备上

易于阅读的标准是过去良好可读性的标准。这是我的指南的更新版本，为了更好的可读性，特别是对于移动设备。

1.  **默认字体大小需要易读**。不变焦。无需点击任何按钮。你必须调整字体以适应屏幕尺寸。请记住，从移动设备上阅读时，与屏幕的平均距离仅为 8-10 英寸，就像一本书一样，而台式机的平均距离为 23-28 英寸。

2.  **文字需要呼吸**。虽然移动设备的屏幕空间非常宝贵，但是用文字填满屏幕对你的目标没有任何帮助。所以要避免诱惑。在文本周围留出足够的空白。它可以减轻读者的压力，让他们专注于重要的部分——文本。

3.  **保持列比例**。如果您仍然在移动布局上运行多列，请记住列宽需要与字体大小成比例。选择正确的列宽不仅仅是设计或格式的问题。这是为了确保易读性和流畅。就像在桌面上一样，如果栏太窄，会使眼睛频繁地变换线条，破坏阅读的连贯性。电话布局最好只有一个单独的栏。一般来说，每行 10-15 个单词可以让阅读流畅。

4.  **调整行高**。关于字里行间的东西，这是一种痛苦。没人想做。间隔太近的线条会让眼睛同时看到上面和下面的线条，让读者看不清它们。所以你的部分设计需要确保行与行之间有足够的空间，即使是在手机屏幕上。

5.  **避免图像中的文本**。一旦您的图像缩放到与屏幕尺寸匹配，就不能保证图像中的文本仍然可读。如果那条短信是你的笑点，太糟糕了，你已经失去了机会。

> "..读你正在设计的东西，想象第一次读的时候，像刚发现的人一样。”–[迪安·艾伦](http://alistapart.com/article/readingdesign)

## 工具

既然我们已经确定了在移动设备上正确处理文本的需求和准则，我们该如何着手呢？有许多方法可以做这件事，有些方法比其他方法更有用。让我们快速浏览一下。

实现字体缩放最简单的方法是设置一个灵活的基线。不要以像素为单位指定正文字体大小，而是将其设置为容器的 100%，然后使用`em`设置相对于此的每隔一个的字体大小:

```
body {
    font-size:100%; 
}

p {
    font-size: 0.875em; /* If the baseline is 16px, this will scale the font to 14px */
    line-height: 1em;  /* This keeps the line height proportional to the font size */
}
```

## 使用媒体查询

如果您想专门针对较小的屏幕进行缩放，媒体查询会有所帮助。就像网络上的其他事情一样，没有唯一正确的方法来做这件事。根据您的要求，您可以使用`px`、`em`、`rem`或[视口单位](https://www.sitepoint.com/new-css3-relative-font-size/)来指定测量。只有当你需要高精度时，像素才是好的，但它们并不能真正提供缩放所需的多功能性。

使用`em` [通过相对于正文尺寸进行所有测量来实现灵活性](http://www.impressivewebs.com/understanding-em-units-css/)。虽然这涉及到你头脑中的一些额外的数学运算，但这是一个确保字体缩放的好方法。然而，由于它是相对于容器的上下文的，一旦你的层次结构中有了几个层次，或者以后需要移动模块时，它就会变得非常笨拙。

救援的`rem`来了。`rem`单元与`em`非常相似，除了它们与根元素的字体大小相关，并且独立于当前容器的上下文。问题是，`rem`在旧的浏览器中不被支持。[伊森·马科特给出了一些关于何时使用这些装置的好建议。另一个有用的方法是基于](http://blog.typekit.com/2011/11/09/type-study-sizing-the-legible-letter/)[视口单位](https://www.w3.org/TR/css3-values/#viewport-relative-lengths)设置尺寸。

以`em`为例，我们将快速了解一下您可以做些什么。要使移动屏幕的字体稍微小一点，您可以这样缩放:

```
@media screen and (max-width: 30em) {
    body {
        font-size: 90%;
    }
}

@media screen and (max-width: 20em) {
    body {
        font-size: 80%;
    }
}
```

因为我们之前已经根据`em`指定了段落字体大小和行高，只需调整正文字体的大小就能确保它们也被调整。当然，这是一个非常基本的例子，只是为了展示它是如何做到的。你可能想跳到[萨拉·苏伊丹的文章](http://tympanus.net/codrops/2013/11/19/techniques-for-responsive-typography/)，在那里她得到了细节。

## 使用 jQuery 插件

虽然媒体查询是使文本具有响应性的更流行的方法，但它们不是真正灵活的解决方案，因为它们依赖于断点。有几个 jQuery 插件可以填补这个空白，帮助您以完全流畅的方式轻松缩放文本。jQuery 响应文本插件和[流类型插件](http://simplefocus.com/flowtype/)根据特定元素的宽度很好地缩放字体大小。FlowType 还允许您指定最小和最大字体大小，以便文本不会超出您想要的范围。

割台有时需要不同的处理。由于标题是一个加粗的语句，您可能不希望它被均匀缩放并分散到多行中。令人敬畏的 [slabText 插件](http://freqdec.github.io/slabText/)不只是盲目地缩放文本，而是计算每行的理想字符数，并相应地将标题拆分为新行，同时缩放文本。如果需要，您也可以控制分割点。萨拉·苏伊丹在[的 CodePen 账户](http://codepen.io/SaraSoueidan)中用一些简洁的例子展示了这种差异。这是一个使用 slabText 插件的响应标题[和使用简单的视口缩放](http://codepen.io/SaraSoueidan/pen/1a659742c6899c70a62059267b7eeedd)的响应标题[。](http://codepen.io/SaraSoueidan/pen/ae3d1f2ba1a4b821212477396b5d4096)

还有其他一些对回应性文本的理解也很有趣，尽管还处于萌芽状态。由[马尔科](http://www.maratz.com/blog/)开发的[head tracker](https://github.com/auduno/headtrackr/)使用用户网络摄像头的面部检测来确定他们离屏幕有多近或多远，然后相应地调整文本的大小。[伊恩·斯托姆·泰勒](http://ianstormtaylor.com/media-queries-are-a-hack/)和[其他人](http://www.smashingmagazine.com/2013/06/25/media-queries-are-not-the-answer-element-query-polyfill/)一直倡导*元素查询*而不是媒体查询——基于元素的属性而不是屏幕做出决定。它更多的是一种讨论权，元素查询还不是 CSS 的一部分。

## 结论

随着手机取代了我们消费数字媒体的方式，重点将从复杂的布局和花哨的设计转向更简单、更大、以版式为中心、移动优先的设计。

忽略响应文本真的不再是一个选项。

## 分享这篇文章