# 更多针对超文本的犯罪

> 原文：<https://www.sitepoint.com/more-crimes-against-hypertext/>

**回到 *[针对超文本的犯罪](https://www.sitepoint.com/crimes-against-hypertext/)* ，我列举了几个糟糕的超链接的常见例子。这引发了一场有趣的讨论，所以我想我应该写一篇后续文章，增加一些例子和一些解决方案。**

## 最新流氓

我们流氓画廊的第一个新人来自我们的语言编辑，凯利。她对将多个相邻单词分别链接到同一个链接目标，而不是将它们连接成一个链接感到困惑。她称之为重身幽灵号。

下一条来自*贝尔*，一位评论者说:

> 伪装成伏击，这个链接看起来像要打开另一个网页，但实际上是要打开一个 PDF，文件，电子邮件地址等。总是当你很匆忙，快速浏览你要找的东西时，你点击其中的一个，然后 BAMM——当它启动你的客户端软件时，你被绑着双手等待。哦，当我匆忙踩上一颗超级链接地雷时，从我的角落里传来懊恼的嚎叫声。“不，不！嗷嗷呜！!"

我把那个叫做**破坏者**。SitePoint 的另一位技术编辑 Raena 认为这款游戏应该叫做**哭泣游戏**。我会让你去思考这个问题。

最后，来自另一个评论者，*韦恩菲尔德*，是被称为**哑剧艺术家**的链接。这是让你想要“更多> >”的链接:

```
<p>It's often taken for granted but the lack of good
hypertext makes reading a web page a generally 
unpleasant experience. There are several essential 
hyperlinking techniques you should know.</p>
<p><a href="/">More >></a></p>
```

哑剧艺术家之所以如此命名，是因为它模糊地指向一个毫无意义的方向，没有给你任何线索，让你希望你能得到退款。

## 良好的链接实践

关于好的链接实践的共识是，一个链接应该指出当用户点击它时会发现什么。链接文本应该帮助用户决定是否跟随一个链接。

一些发帖者建议我重写这个好例子，将链接的意图*而不是*目标站点的名称*。所以，不要这样:*

```
For excellent examples of finely crafted hypertext
look no further than <a href="http://kottke.org/">
kottke.org, the online home of Jason Kottke</a>.
```

…我会这样重写链接:

```
For <a href="http://kottke.org/">excellent examples
of finely crafted hypertext</a> look no further than
kottke.org, the online home of Jason Kottke.
```

这对我来说很有意义。

这得到了 [Web 内容可访问性指南 2.0](https://www.w3.org/TR/UNDERSTANDING-WCAG20/navigation-mechanisms-refs.html) 的支持，该指南指出，链接的目的应该能够仅从其链接文本或其所处的上下文来确定。事实上，[该指南描述了在以下情况下可访问性的失败:](https://www.w3.org/TR/2008/NOTE-WCAG20-TECHS-20081211/F84)

> …“单击此处”或“更多”等链接用作锚定元素，您需要有周围的文本来理解它们的目的。

那么我们如何避免这样的可访问性问题呢？

## 解决方案

有许多支持者使用短语“点击这里”——同义反复链接——作为行动的号召，以便更多的读者会点击这个链接。我理解呼吁行动的必要性，因此在诉诸“点击此处”之前，我可能会调查替代方案例如，您可能会看到:

```
To see how you can improve your hypertext
today, <a href="/">click here</a>.
```

我首先会考虑这样改变语言:

```
<a href="/">See how you can improve your
hypertext today!</a>
```

但是，如果客户要求“点击这里”的路标，我会建议:

```
<a href="/">Click here to see how you can 
improve your hypertext today!</a>
```

关于 Mime Artist 链接，如果你必须实现一个使用单词“more”作为链接文本的链接，[指南还提供了一个简单的解决方案](https://www.w3.org/TR/2008/NOTE-WCAG20-TECHS-20081211/C7)(这在 [Max Design](http://www.maxdesign.com.au/presentation/more-links/) 网站和许多其他网站上有所反映):使用 CSS 来隐藏你想要隐藏的链接文本部分。

让我们重用一些以前的标记，但是添加一个包含一些额外文本的`span`:

```
<p>It's often taken for granted but the lack of good
hypertext makes reading a web page a generally 
unpleasant experience. There are several essential 
hyperlinking techniques you should know.</p>
<p><a href="/">More<span> about creating quality
hypertext</span></a></p>
```

然后使用 CSS 将`span`元素中的文本移出屏幕。这两种解决方案(如上所述)使用绝对定位来移动文本，但彼此略有不同。它们值得一看。

这两种解决方案的结果都是只让所需的链接文本可见，同时让扩展的链接文本对屏幕阅读器和搜索引擎可用。

## 分享这篇文章