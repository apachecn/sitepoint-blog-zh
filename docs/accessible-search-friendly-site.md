# 可访问性的秘密好处第 2 部分:更好的搜索排名

> 原文：<https://www.sitepoint.com/accessible-search-friendly-site/>

网页可访问性的主要好处之一是，人们更容易访问的网站通常也更容易被搜索引擎访问。你的网站越容易被搜索引擎访问，他们就越有信心猜测网站的内容，从而让你的网站更有机会在搜索引擎排名中名列前茅。

并不是所有的可访问性指南都有助于你的搜索引擎排名，但是肯定有很多重叠的地方。让我们依次看一下每一个。

##### 1.为图像指定 ALT 描述

许多视障网络用户使用的屏幕阅读器不能理解图像。为了确保可访问性，需要为每个图像分配一个替代描述；屏幕阅读器将读出此替代说明:

```
<img src="filename.gif" alt="image description goes here" />
```

像屏幕阅读器一样，搜索引擎通常被认为不能理解图像。但是一些搜索引擎索引 ALT 文本。通过给你的图片分配替代文本，这些引擎甚至能够理解你的图片内容。

当然，许多搜索引擎不会注意替代文本。ALT 标签垃圾邮件的泛滥破坏了 ALT 文本在这些引擎眼中的相关性，这些引擎不再在其相关性算法中考虑它。

那么，为什么要用图片替代文本呢？因为你的排名可能会在考虑到这一点的搜索引擎中提高，并使你的网站更容易访问。

##### 2.通过 HTML 显示文本，而不是图像

图像中嵌入的文本看起来像素化、模糊，使用屏幕放大镜的人通常无法阅读。从可访问性的角度来看，应该避免在图像中嵌入文本。

似乎目前搜索引擎也无法读取嵌入图像的文字。嗯，你可以只应用 ALT 文本到这些图像，对不对？不幸的是，有强有力的证据表明，搜索引擎对替代文本的重视程度低于普通文本。为什么？垃圾邮件发送者(再次！)，如上所述。

答案？为了确保您的网站是可访问的，并且您的内容尽可能多地计入您的搜索排名，请避免在图像中嵌入文本。

##### 3.使用描述性链接文本

视障网络用户可以通过在链接之间切换来浏览网页，一边浏览一边听链接文本的内容。因此，可访问网站中的链接文本必须始终描述其目的地。

搜索引擎通常也会重视链接文本。许多引擎假设链接文本将描述其目的地，所以它们检查指向给定页面的所有链接的文本。例如，如果指向一个关于小部件的页面的所有链接都是“点击这里”，搜索引擎不访问它就不能获得关于那个页面的任何信息。另一方面，如果所有的链接都说“widgets ”,那么搜索引擎可以很容易地猜出链接的页面是关于什么的。

这种技术在实践中的一个最好的例子就是搜索词“悲惨的失败”。如此多的人使用这个短语作为链接文本链接到乔治·布什的简历，现在当在谷歌上搜索“悲惨的失败”时，乔治·布什的简历出现在搜索排名的首位！

##### 4.禁用 JavaScript 的网站功能

大约 7%的网络用户不支持 JavaScript，要么是因为他们关闭了 JavaScript(可能是为了防止弹出广告)，要么是因为他们的浏览器不支持它。许多形式的 JavaScript 对于使用屏幕阅读器的人来说是不可理解的。

众所周知，很少有搜索引擎能够理解 JavaScript——这些服务可能无法索引任何 JavaScript 驱动的内容。也许更重要的是，他们也将无法跟踪 JavaScript 驱动的链接。你可能真的喜欢你的下拉菜单的外观，但是如果搜索引擎因为缺乏常规的`<a href>`链接而无法访问你网站上的某些页面，它们就不会喜欢。

##### 5.提供基于 Flash 的内容的替代方案

像 JavaScript 一样，Flash 对许多用户来说是不可访问的，包括那些使用屏幕阅读器的用户。同样，很少有搜索引擎能访问 Flash——一定要提供对等的搜索引擎。

##### 6.音频可用的抄本

听力受损的用户显然需要音频内容的书面等同物来访问该信息。通常，搜索引擎也不能访问音频内容；抄本为他们提供了相关的文本索引。

##### 7.提供网站地图

站点地图对视力有障碍的用户来说是一个有用的工具，因为它们提供了一个指向站点主页的链接列表。

站点地图对搜索引擎来说也是很棒的，因为大多数搜索引擎可以在到达站点地图后立即索引你的整个站点。在每个链接旁边，你还可以提供一个简短的关键词丰富的页面预览，以改善用户体验和潜在的搜索排名。当然，所有的链接都应该通过普通的 HTML，而不是 JavaScript(见上面的第 4 点)。

##### 8.有意义的页面标题

当我们到达网页时，首先出现的——视障用户首先听到的——是页面标题。视障网络用户没有特权快速浏览页面以查看其中是否包含他们想要的信息，所以[页面标题有效地描述页面内容](https://www.sitepoint.com/html-title/)是至关重要的。

如果你对搜索引擎优化有所了解，你就会知道页面标题是页面上最重要的属性之一。如果它充分描述了该页面的内容，更多的搜索引擎将能够更准确地解释该页面的内容。

##### 9.使用的标题和副标题

视力受损的网络用户可以通过在标题之间以及链接之间切换来浏览网页(见上面的第 3 点)。因此，从可访问性的角度来看，确保使用`<h1>`、`<h2>`等正确标记标题是很重要的。标签。

一般来说，大多数搜索引擎会认为包含在标题标签中的文本比文档中的其他文本更重要，因为标题描述了它们下面的内容。搜索引擎赋予`<h1>`最大的重要性，然后是< h2 >，以此类推。确保正确使用标题标签。不要滥用它们——例如，标题标签之间包含的文本越多，搜索引擎就越不重视它们。

##### 10.用于布局的 CSS

屏幕阅读器可以更有效地阅读基于 CSS 的网站的 HTML 代码，因为内容与代码的比率更高。使用 CSS 布局的网站也可以通过车载浏览器、网络电视和 PDA 访问。正如我在本文第一部分提到的，预计 2008 年全球将售出 5800 万台 PDA。

搜索引擎倾向于给基于 CSS 的网站更高的搜索排名，因为:

*   代码更干净，因此更容易被搜索引擎访问
*   重要的内容可以放在 HTML 文档的顶部
*   与编码相比，内容的密度更大

CSS 应用价值的一个例子是 [Juicy Studio 站点](http://www.juicystudio.com/)，它最近从一个常规的基于表格的站点变成了一个基于 CSS 的站点。自那以后，该网站的访问量增加了 6 倍。

##### 结论

随着网页可访问性和搜索引擎优化之间的重叠，没有理由不在你的网站上实现基本的可访问性技术。SEO 的关注从来不会为忽视无障碍网页设计方法提供一个可行的借口。

将易访问的编码方法应用到网站设计中，绝不会妨碍搜索引擎对你的网站的感知，并且在很多情况下会极大地提高对你的内容的访问。可访问性技术的实现可能会给你一个更好的机会获得更高的搜索引擎排名，从而吸引更多的访问者。

## 分享这篇文章