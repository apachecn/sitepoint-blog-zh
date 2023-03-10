# 下划线链接不刺痛眼睛的秘密？

> 原文：<https://www.sitepoint.com/secret-underlined-links-dont-sting-eyes/>

[![Hypertext Editing System - Brown University](img/dc7764f6fcad14b3d4bffac73017ca95.png)](http://en.wikipedia.org/wiki/Hypertext_Editing_System)

这是超级链接之父 T2 在 1969 年在布朗大学发明的超级文本编辑系统。

该系统最酷的地方是内置的光笔，它允许操作者手动给他们想要链接的文本加下划线。

天啊，皮博迪先生！

你几乎可以直接找到我们今天仍在网上使用的带下划线的链接。下划线可能是网络上最广为人知的约定，而且不会很快消失。

另一方面，在排版圈子里，下划线一直是一个严重的禁忌。

> 绝对不行。在打印文档中，不要换行。永远不会。它很难看，使文本更难阅读。

[*来源](http://practicaltypography.com/underlining.html)

Butterick 将下划线的做法定义为一种打字机技巧——一种在不提供任何粗体或斜体功能的技术中强调文本的变通方法。

所以，..易访问性和 UX 人告诉我们应该使用它们，但是我们被告知它们是印刷毒药。

洛克先生。我能把你介绍给硬地先生吗？

### 问题是

那么，如果我们不能扔掉下划线链接约定，我们能把它变得更好吗？它最大的缺点是什么？

除了填充行与行之间的空白所带来的一般痉挛之外，也许最大的一个问题是当下划线接触到字体时会发生什么。

![Problems with lowercase 'q' and 'a'.](img/2ef090ec3255e719bd14bc121e56a927.png)

一个常见的令人讨厌的例子是小写字母 p，g，y，q，有时 z 的下行字母碰到了下划线。受轻包豪斯影响的短下行字体似乎比大多数字体更容易受到影响。

正如你在下面看到的谷歌字体，如 **[Rajdhani](http://www.google.com/fonts/specimen/Rajdhani "Google fonts: Rajdhani")** 和 **[Poiret One](http://www.google.com/fonts/specimen/Poiret+One "Google fonts: Poiret One")** ，当涉及下划线时，很难在视觉上辨别小写字母“q”和“a”之间的区别。

[![Quattrocentro's capital Q disappears into the underline](img/e1651ed58d6cfc6a262887961f67d504.png)](https://www.google.com/fonts/specimen/Quattrocento)

同样，大写字母“Q”的尾部加下划线通常是没问题的，但是对于某些字体来说可能会出现严重的问题。例如， **[Quattrocentro](https://www.google.com/fonts/specimen/Quattrocento "Google font: Quattrocento")** 有一个非常低的水平尾部，几乎消失在下划线中。大写字母“J”上的小尾巴也变得不那么清晰了。

乱七八糟，对吧？

### 有解决办法吗？

[![photo credit: Ghostsigns](img/d9f0e6a8a620c52f3d7665028736a6a0.png)](https://www.flickr.com/photos/ghostsigns/14427477195/)

图片来源:Ghostsigns

信号作者经常在他们的作品中使用下划线，但他们通常会清楚地将线和字母分开。

虽然我们不能将下划线完全断开，但我们可以制造出下划线断开的假象。

亚当·施瓦茨最近认真考虑了这个问题。[他的“智能下划线”方法](https://eager.io/blog/smarter-link-underlines/)使用 CSS 阴影在文本周围生成背景色的“光晕”——同时去掉默认的下划线(文本装饰)。

然后，他使用背景渐变重新创建下划线，将下划线推回到文本后面。这个动画展示了零件是如何堆叠的。

[![Animation: Stacking order of the underline.](img/89a9e78f1db4752cbaaddf0b27f4d762.png)](https://eager.io/blog/smarter-link-underlines/)

我认为这是一个非常好的方法。它不仅解决了“下行崩溃问题”，还让设计者完全控制你的下划线的粗细、颜色和垂直对齐。

我知道在某些情况下，正是*这种缺乏风格控制的情况促使设计师们一起移除链接下划线。也许这会改变他们的想法。*

 *我能看到的唯一潜在的缺点是光环颜色和背景不同步的情况——也许一个灰色的文本框最终会有白色的发光链接。

即使这个问题也可以通过良好的 CSS 结构和样式指南文档轻松避免。

这是聪明的东西。有阅读。

##### 从 [SitePoint 设计时事通讯](https://www.sitepoint.com/newsletter/) ( [档案馆](http://sitepointnewsletters.cmail2.com/t/ViewEmail/y/968259CA6A47DD1A))重新发布* 

## *分享这篇文章*