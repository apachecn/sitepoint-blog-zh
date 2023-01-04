# HTML 4 被认为有害

> 原文：<https://www.sitepoint.com/html-4-considered-harmful/>

![A cat in a washing machine](img/86de6e4ae24cf8f3ca683d0f10dcc40f.png)

有时候我觉得自己在用头撞砖墙。或者我应该说，有时候我觉得我敲的鼓像是被砖头砸的！

多年来，我一直提倡使用 XHTML ，虽然我对 <abbr title="eXtensible HyperText Markup Language Version 2">XHTML 2</abbr> 的死亡一点也不感到遗憾(因为它完全脱离了现实)，但我对如此多的开发人员退回到使用 <abbr title="HyperText Markup Language Version 4">HTML 4</abbr> 感到非常遗憾。我同样感到遗憾的是，那些已经开始用 <abbr title="HyperText Markup Language Version 5">HTML 5</abbr> 标记他们的内容的有远见的开发者中，有很大一部分正在使用 <abbr title="HyperText Markup Language Version 4">HTML 4</abbr> 语法。

XHTML 有很多好处，严格的 HTML 也是如此，举两个例子来说，比如去除了表示标记，以及对属性的一致引用。但是 <abbr title="eXtensible HyperText Markup Language">XHTML</abbr> 有一个独属于它的好处，那就是 <abbr title="eXtensible Markup Language">XML</abbr> 语法。对我来说，使用 <abbr title="eXtensible Markup Language">XML</abbr> 语法的好处如此显著，以至于我对任何质疑它的人都感到震惊。

不要误解我的意思，我不是说任何不同意我的人都是无知的。我要说的是，对我来说，像 <abbr title="eXtensible Markup Language">XML</abbr> 良构性这样明显有用的东西竟然会被任何人忽略，这是多么不可思议。 <abbr title="eXtensible HyperText Markup Language">XHTML</abbr> 担任`text/html` **难道**比 <abbr title="HyperText Markup Language Version 4">HTML 4</abbr> 有优势，仅仅是因为*长得像* <abbr title="eXtensible Markup Language">XML</abbr> 。

好吧，所以它不是真正的 <abbr title="eXtensible Markup Language">XML</abbr> 。所以自结束语法只在当前的浏览器中有效，因为它们有纠错的倾向。所以 <abbr title="eXtensible HyperText Markup Language">XHTML</abbr> 作为`text/html`是一个忽悠，技术上是不正确的。但这不重要。重要的是它*看起来像* <abbr title="eXtensible Markup Language">XML</abbr> ，对于任何能够解析字符串的 <abbr title="eXtensible Markup Language">XML</abbr> 解析器来说，根本没有**的区别**。

这里有一个恰当的例子——最近我需要找到一种从`responseText` <abbr title="HyperText Markup Language">HTML</abbr> 创建 <abbr title="Document Object Model">DOM</abbr> 的方法。我不能得到`responseXML`,因为标记超出了我的控制范围(无论如何，它不是 <abbr title="eXtensible Markup Language">XML</abbr> ),我也不能使用“document.write to an iframe”的技巧，因为实现不支持它。所以我用了 [DOMParser](https://developer.mozilla.org/en/DOMParser) (在 Firefox、Opera 和 Safari 上都可以用):

```
var dom = new DOMParser().parseFromString(request.responseText);
```

这很有效。但是它只适用于格式良好的 XHTML 文档。为什么？因为**他们长得像<abbr title="eXtensible Markup Language">XML</abbr>T5！它在 HTML 4 文档上失败了，因为它们没有。**

这里有一个简单的例子，说明使用格式良好的 <abbr title="eXtensible HyperText Markup Language">XHTML</abbr> 、 [SynBay](http://synbay.com/ "rapidshare megaupload torrent") 来开发网页是多么的有益，而不考虑交付它所使用的 mime 类型。看起来像 <abbr title="eXtensible Markup Language">XML</abbr> 的标记可以被解析为 <abbr title="eXtensible Markup Language">XML</abbr> ，不管它实际上是否如此。

我是说真的。还有什么比这更有说服力的呢？我就是不明白。

## 分享这篇文章