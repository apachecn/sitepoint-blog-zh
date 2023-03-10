# 如何停止通过跳出框框来设计方形布局

> 原文：<https://www.sitepoint.com/stop-designing-square-layouts-thinking-outside-box/>

![Websites are built from rectangles](img/d1d71c3d98d02fb8d85b573751684ee9.png)

网站基本上是长方形的东西。

当然，这是有充分理由的。就像之前的泥板、纸莎草纸和书籍一样，网站大多是由一排排堆积的字母、单词和句子组成的。同样，我们的相机拍摄矩形照片。为什么矩形“砖块”倾向于建造矩形“墙壁”并不神秘。

默认情况下，组成网站的许多“块”是矩形的——对于开发者来说，你会知道我说的是`<div>`、`<article>`、`<sections>`元素和其他类似的代码标签。

在 CSS3 之前，如果我们想制作不同的形状，我们必须在网页设计中插入位图图像。然而，现代 web 标准(例如 SVG 支持)在这方面允许更多的灵活性。

让我们来看看一些使用现代网络标准设计不规则形状布局的网站，更重要的是，这是如何影响用户体验的。

## Easee:简单的 SVG 示例

SVG 代表 *[可缩放矢量图形](https://en.wikipedia.org/wiki/Scalable_Vector_Graphics)* 。这是一种现代的、技术上成熟的文件格式，可以在不损失质量的情况下进行缩放。SVG 不是标准的“像素网格”(即 PNG、JPG 等)，它更像是一个“图像食谱”——通过简单地将配料翻倍，使蛋糕变大一倍。

最近，我们看到现代浏览器改进了它们的 SVG 功能，这是一个好消息，因为 SVG 允许我们在不增加网站加载时间的情况下创建独特的形状。

让我们直接进入我的第一个例子。

![Simple SVG shapes in a layout](img/b1beaf26a0d8b5bbb9a9163273bdd1ba.png)

如果你看一下 [Easee](http://easee.design/) 的页脚，你会发现他们在背景中使用了波浪曲线。它可以立即适应任何屏幕大小，因为它是一个内嵌的 SVG，并且图像的文件大小非常小，不到 1kb。位图图像的大小应该是它的 4 到 5 倍。

事实上，这是整个 SVG 的形状——包括一个你在网站上可能没有注意到的微妙的阴影。

```
<svg width="1440px" height="204px" viewBox="0 0 1440 204" version="1.1" class="homepage__features__wave">
 <defs>
 <filter x="-50%" y="-50%" width="200%" height="200%" filterUnits="objectBoundingBox" id="filter-1">
 <feoffset dx="0" dy="-4" in="SourceAlpha" result="shadowOffsetOuter1"></feoffset>
 <fegaussianblur stdDeviation="0" in="shadowOffsetOuter1" result="shadowBlurOuter1"></fegaussianblur>
 <fecolormatrix values="0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0.056 0" in="shadowBlurOuter1" type="matrix" result="shadowMatrixOuter1"></fecolormatrix>
 <femerge>
 <femergenode in="shadowMatrixOuter1"></femergenode>
 <femergenode in="SourceGraphic"></femergenode>
 </femerge>
 </filter>
 </defs>
 <path d="M6.05022984e-08,204 C0.00535896441,203.997351 356.00274,28.000572 720,104 C1084,180 1440,4 1440,4 L0,4 L0,204 L0,4 L1440,4 L1440,204 L0,204 Z" fill="#F9F9F9" filter="url(#filter-1)"></path>
 </svg>
```

它也不仅仅是提供了一些视觉上的“不同”——它给设计增加了友好、弯曲、有弹性、有趣的个性。一个普通的正方形可能不会有同样的效果。

顺便说一下，我提到过 SVG 图像可以很容易地制作成动画吗？刷新网页，再仔细看一点！

## 土豚旅:传统位图示例

让我们看看另一个例子，它使用位图图像完成了同样的事情。我用谷歌 Chrome 开发工具检查了最上面的背景图片(在菜单栏下),它有 197kb 那么大。

由于 SVG 文件由几何形状组成，因此使用 SVG 创建纹理更加困难，并且由于位图不可缩放，因此本例中的位图非常宽(2300px ),以适应所有的屏幕尺寸，这是导致文件过大的另一个因素。

值得吗？

![Heavy bitmap shapes - The Aardvark brigade](img/10f8c70df907b5c702ff21f5a28ebbde.png)

我的直觉告诉我不是，但我做的测试却不是这样。

我把 tools.pingdom.com 指向了那个地方，发现它在 1.26 秒内就被载入了 T2。由于该设计使用了大量纹理位图，我原本期待更高的东西，但测试表明该网站已经进行了大量优化。所以，这证实了如果你考虑整体性能和负责任的优化(并在这个问题上投入足够多的服务器)，位图仍然是一个可行的选择。

*注意:你* ***可以*** 将位图嵌入到 SVG 文件中，创建一个 SVG/位图混合文件，这有时是一个合适的折衷方案。

## Buffalo 构建:CSS 转换示例

到目前为止，我们的例子有些复杂。对于更简单的形状(可以命名的形状，如六边形、三角形或菱形)，可以使用简单的 HTML 和 CSS 来创建。

[Buffalo 建造](http://builtbybuffalo.com)使用一系列旋转的矩形创建一个六边形。现代 web 标准支持旋转、倾斜和缩放等变换，有时甚至完全不需要图像，甚至允许用本地 web 代码设计形状的样式**和动画**。

![Built By Buffalo: Transforming the default square div box](img/442c72795b733e6425163e07bd45081e.png)

Joyce Vherck 的网站是目前我最喜欢的例子。她对形状的运用不仅仅是一种视觉审美，而是她品牌不可或缺的一部分。

更好的是，Joyce 利用现代网络标准以各种方式扭曲传统的盒子元素。如果你正在寻找一种在网页设计中创建形状的轻量级方法，并且在现代网页浏览器中具有合理的兼容性，这将是实现它的方法——唯一的缺点是形状必须相对简单。

倾斜部分已经成为网页设计的一个巨大趋势，所以如果你想知道如何实现这一点，现在你知道了。

![Slanted, skewed shapes](img/2d572ffb66088900eb933508b0921ec3.png)

如果你向下滚动到 [Joyce 的](http://www.joycevherck.com)网页，你会看到形状不仅以不同的方式使用，而且使用我在本文中提到的各种方法创建，这重申了创建形状没有正确或错误的方法这一事实。看情况吧。

## CSS 屏蔽

网页设计中的形状遮罩可以用`clip-path`属性来完成，尽管[浏览器对](http://caniuse.com/#feat=css-clip-path)的支持仍然很不稳定。出于这个原因，网上没有太多的用例，但 CodePen 上有一些例子可以向我们展示屏蔽是如何工作的。

参见 [CodePen](http://codepen.io) 上卡伦·梅内塞斯([@伊莫凯](http://codepen.io/imohkay))的笔[可视化剪辑路径](https://codepen.io/SitePoint/pen/BLjpdY/)。