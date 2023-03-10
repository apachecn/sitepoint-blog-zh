# 5 个最好的免费 HTML5 演示系统

> 原文：<https://www.sitepoint.com/5-free-html5-presentation-systems/>

我非常尊重微软的 PowerPoint。它可能被过度使用，并鼓励人们创建令人震惊的幻灯片，但它是强大和有趣的。我只有一个批评:所有的 PowerPoint 演示看起来都一样。无论你如何改变颜色、背景、字体或过渡，每个人都可以在一英里外看到 PPT。

幸运的是，我们现在有了另一个选择:HTML5。或者，更具体地说，HTML5 模板由带有 CSS3 2D/3D 过渡和动画的 JavaScript 支持。好处包括:

*   添加一些 HTML 标签比使用所见即所得界面要快
*   您可以在任何设备上使用基本文本编辑器更新演示文稿
*   文件可以在 web 上托管；你再也不需要丢失 PPT 了
*   您可以轻松分发演示文稿，而无需查看软件
*   这不是 PowerPoint，你的观众会对你的技术实力感到惊讶。

不可否认，HTML5 演示并不那么强大:

*   你需要网络编码技能
*   定位、效果和过渡更加有限
*   很少有系统提供幻灯片备注(单独显示有点尴尬)
*   打印讲义更加困难

但这些不一定是致命的缺点——你被迫专注于演示内容，而不是琐碎的调整和糟糕的动画。

以下是我发现的最好的 HTML5 演示系统。点按标题观看演示——大多数在 Chrome、Safari 或 Firefox 中运行效果最佳…

## [Reveal.js](http://lab.hakim.se/reveal-js/#/)

这是我目前的最爱。幻灯片看起来很棒，可以水平或垂直放置。更改 CSS 来添加你自己的效果也很容易——我设法添加了一个基本的 notes 工具来实现这一点。

幻灯片在单独的`section`标签中定义，Reveal.js 提供了几种 3D 幻灯片过渡的选择……尽管它们看起来非常相似。

[下载 reveal.js 文件……](https://github.com/hakimel/reveal.js)

## [Impress.js](http://bartaz.github.com/impress.js)

Impress.js 的确令人印象深刻。它受到了商业[prezi.com](https://prezi.com/)的影响；幻灯片排列在三维空间中，您的视图会相应地旋转和平移。我不想进一步解释它——[试一下](http://bartaz.github.com/impress.js)，你就会明白我的意思。

幻灯片在`div`元素中定义，数据属性控制 x、y、z 位置和旋转。这是唯一的缺点；形象化和定义这些价值有点困难。但是，坚持下去，你将能够创作出一些令人惊叹的原创幻灯片。

[下载 Impress.js 文件……](https://github.com/bartaz/impress.js)

## Google 幻灯片模板

正如你所料，谷歌有自己的 HTML5 演示模板(以及在[谷歌文档](http://docs.google.com/)中提供的那个)。与 Reveal.js 或 Impress.js 相比，它是相当基本的；它仅限于从右到左的幻灯片过渡和淡入效果，但看起来仍然很棒。

幻灯片在`article`标签中定义，很容易添加额外的样式表来覆盖默认的 CSS。

[下载谷歌幻灯片模板文件……](http://code.google.com/p/html5slides/source/browse/trunk/template/index.html)

## Deck.js

Deck.js 是最早的 HTML5 表示系统之一。它看起来类似于谷歌的产品——也许更漂亮一点，但没有幻灯片过渡效果。

幻灯片被定义在外部`article`中语义正确的`section`块内。还有一个文档 API 下载 Deck.js 文件…

## 淋浴

Shower 是 Opera 软件的 Vadim Makeev 开发的演示系统。虽然它是最简单的系统之一，但它速度很快，在所有浏览器中都可以很好地工作。幻灯片缩略图视图特别酷(按 ESC)。

Shower 的主要缺点是复杂的 HTML。幻灯片被定义在一个`section`内的一个`div`内的另一个`div`内，并包含它们自己的`header`和内容。再多做一点开发就能让它更容易使用。

下载淋浴文件…

其他值得一提的是:

*   [S5——一个简单的基于标准的幻灯片放映系统](http://meyerweb.com/eric/tools/s5/s5-intro.html) ( [下载](http://meyerweb.com/eric/tools/s5/))
*   [CSSS——基于 CSS 的幻灯片放映系统](http://lea.verou.me/csss/sample-slideshow.html) ( [下载](https://github.com/LeaVerou/CSSS))
*   [幻灯片](http://briancavalier.com/code/slides/#0) ( [下载](https://github.com/briancavalier/slides))
*   HTML5Rocks(不直接下载，但是可以复制源码)

我有没有忘记提到你最喜欢的 HTML5 演示系统…

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如真实世界的 [HTML5 & CSS3。](https://learnable.com/courses/html5-css3-for-the-real-world-1484?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)

对本文的评论已经关闭。有关于 HTML5 的疑问？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?52-(X)HTML?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章