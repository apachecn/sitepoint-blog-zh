# Blockquote:过去和现在

> 原文：<https://www.sitepoint.com/blockquote-then-and-now/>

**正如《终极 HTML 参考》的作者伊恩·劳埃德所说，“`blockquote`已经存在了很长时间，但它一天也没有变老。”虽然`<blockquote>`标签在今天的浏览器中的作用与它在最早的浏览器中首次出现时几乎没有变化，但我们使用它的方式却有了显著的进步。**

当然，`blockquote`是为了标记一个“块引用”——除了周围的内容之外，在别处最初发布或呈现的内容的逐字复制，作为一个块呈现。与这一重要目的背道而驰的是，元素的默认视觉呈现非常简单:浏览器只需在元素的两边留出大约 36 像素的左右边距。

在网络历史的早期，`blockquote`很大程度上被忽视了。在一个 HTML 元素经常被滥用来实现视觉效果的时代，`blockquote`被视为 [`dd`](https://reference.sitepoint.com/html/dd) 的孪生兄弟，因为它们具有相同的视觉效果，并且因为`<dd>`比`<blockquote>`更短，这就是设计师们所使用的。

以代码质量为傲的设计师们很快发现 HTML 验证器(像 W3C 的[)不喜欢他们为此使用`dd`。`dd`元素毕竟属于一个定义列表(](http://validator.w3.org/) [`dl`](https://reference.sitepoint.com/html/dl) )。另一方面，`blockquote`可以出现在任何一个 block 元素受欢迎的地方，所以它逐渐成为生成 indentend 内容的首选元素。

设计师希望内容出现的缩进越多，他就会包装越多的元素。这导致了一些非常可怕的代码，特别是当由 WYSIWYG 创作工具生成时:

```
<blockquote>
  <blockquote>
    <blockquote>
      <blockquote>
        <blockquote>
          <p>A block with a lot of breathing room!</p>
        </blockquote>
      </blockquote>
    </blockquote>
  </blockquote>
</blockquote>
```

当然，今天大多数网页设计者理解语义有意义的代码的价值。这一点，以及 CSS 可以很容易地应用与默认情况下`blockquote`元素完全相同的格式这一事实，已经见证了近年来正确使用`blockquote`成为主流。

同时，`blockquote`提供的语义比以往任何时候都更加相关。博客不断引用其他博客和网站，而`blockquote`元素非常适合标记这样的引用。

继续读下去，发现一些`blockquote`元素隐藏的微妙之处，以及如何用它来揭示网络中的网络:一个归属的网络。

## 归因网络

网络的许多方面都是基于链接的概念。当我在一篇博文中链接到你时，谷歌认为这是对你内容的信任票。现代博客软件甚至“pings”一个链接的目标，让它知道它已被链接(这被称为 trackback)。所以在很多方面，今天的网络是一个链接的网络。

`blockquote`元素有可能创造第二个“网中之网”——一个归属网。这种可能性是由鲜为人知的 [`cite`属性](https://reference.sitepoint.com/html/blockquote/cite)产生的，该属性应该指向引用的 URL。

```
<blockquote cite="http://example.com/source.html">
  <p>A scintillating quote…</p>
</blockquote>
```

如果每个人都这样标记他们的`blockquote`,这将为我们现在拥有的用于链接的同类强大分析工具打开大门。谷歌不仅能告诉你谁链接到你，还能告诉你谁在引用 T2 的话。

当然，`cite`属性的问题是几乎没有人知道它——甚至更少的*人使用它【*】!原因是什么？在大多数浏览器中，它不做任何事情。

这是微格式社区真正得到的东西——不管 HTML 的一个特性对 Google 有多有用，如果这个特性对网站的访问者来说是不可见的，开发者也不会费心去使用它。

微格式是将 HTML 语言的现有特性结合在一起，使该语言做新事情的方法，微格式的规则之一是它们“首先为人定义，其次才是机器”微格式必须对网站用户可见且有用，这一事实给了设计师使用它的真正理由。

考虑到这一点，微格式社区提出了一种使用`blockquote`以用户可见的方式构建属性网络的方法。这个微格式草案叫做 [cite-rel](http://microformats.org/wiki/cite-rel) ，看起来是这样的:

```
<p>
  A scintillating quote from <cite id="goodquote"
  class="relationship"><a href="http://example.com/source.html"
  >someone I respect</a></cite>:
</p>
<blockquote cite="#goodquote">
  <p>A scintillating quote…</p>
</blockquote>
```

`blockquote`的`cite`属性不是直接指向引用的来源，而是指向一个 [`cite`元素](https://reference.sitepoint.com/html/cite)的 ID，它包含到来源的实际链接。

这不仅产生了一种结构，搜索引擎可以阅读这种结构来发现引用及其在网络上的来源，而且它使这些引用对用户可见，因为引用最终以链接的形式呈现。

下次您拿出一个`blockquote`时，考虑包含一个`cite`属性来指向源代码，甚至尝试 cite-rel 微格式来让您的用户看到源代码，而不仅仅是 Google！

## 分享这篇文章