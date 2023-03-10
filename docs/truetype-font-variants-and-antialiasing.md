# Truetype、字体变体和抗锯齿

> 原文：<https://www.sitepoint.com/truetype-font-variants-and-antialiasing/>

在浏览 Panic.com 的一个网站时，我发现了一个有趣的 CSS 小怪癖。恐慌的家伙们最出名的可能是他们深受喜爱和尊敬的[尾段编辑](http://www.panic.com/coda/)和[传输 FTP 客户端](http://www.panic.com/transmit/)。

恐慌总是使用可爱的大而丰富的卡通图形来装饰他们的页面，但是在我的系统上字体看起来异常粗糙。当然，Mac 和 PC 采用不同的方式来呈现字体，但这是另外一回事。

那么，这里到底发生了什么？

深入研究他们的 CSS，我发现了一些有趣的东西。CSS 直接引用了 truetype 字体的 PostScript 名称——“HelveticaNeue-Light”，而不是通过字体族名称(例如，'*Helvetica·纽伊*')来调用字体，并分别用 font-weight 和 font-style 属性来设置字体的粗细和样式。

```
 body {
font-family: "HelveticaNeue-Light", Helvetica, Arial, sans-serif;
}

```

现在，我不知道这是允许的，但是做了更多的研究，我看到了大约 12 个月前吉列尔莫·埃斯特韦斯关于这个问题的帖子。具有讽刺意味的是，他注意到了不同之处，因为它在他的系统上看起来非常好。根据吉列尔莫的说法:

> Safari 允许您通过在 CSS 中引用字体的 PostScript2 名称(在本例中为“HelveticaNeue-Light”)来使用字体的附加权重；而您只需在样式表中声明字体的全名(“Helvetica·诺伊光”)，以便在 Firefox 2 和其他基于 Gecko 的浏览器(如 Camino)中使用。因此，下面的声明将在几乎所有的 Mac 浏览器中给你带来华丽的 Helvetica 新光

这在 Safari 的任何地方都是一个强大的 purdy 外观，我可以理解为什么你会想要实现这个外观。然而，结果是目前在 Firefox、Opera、Chrome 和 Internet Explorer 上对使用这种字体的 Windows 用户来说都很恶心(我显然也是这样)。

事实上，我用斜体变体做了一些测试(右)，这让我想挖出我的眼球。

它似乎也对 Linux 用户做了令人讨厌的事情。根据菲利普·巴恩哈特的评论..在 FireFox 3 和 Linux 上的 Konquorer 上几乎完全无法读取。

如果字体是常驻的，似乎大多数非 Mac 浏览器都不能忽略这种字体声明，但它们也不太擅长很好地呈现它。

当然，恐慌为 Mac 用户制作 Mac 软件，并清楚地知道他们的受众。Helvetica 纽也*不是一个常见的 Windows 字体，所以我假设大多数 Windows 用户在 Candybar 网站上看到 Arial(如果我错了，请告诉我)，所以没什么大不了的。*

然而，为了防止开发人员受到诱惑，为其 Mac 的可爱而欣喜，并开始将这种技术用于更常见的字体变体，我认为有必要指出它可能带来的不愉快。

## 分享这篇文章