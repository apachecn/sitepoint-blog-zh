# IE6 死后我们可以做的 10 件很酷的事情

> 原文：<https://www.sitepoint.com/10-cool-things-well-be-able-to-do-once-ie6-is-dead/>

有些人认为 IE6 已经死了。但是只有那些拥有专业观众的开发者，或者那些在他们的工作中没有任何商业利益的人，才能这样想。对于我们其他人来说，我们要考虑的是现实世界中普通的非技术用户， <abbr title="Internet Explorer 6">IE6</abbr> 仍然是一个持续的关注点。

但也许不会太久了。既然 IE8 已经推出，我们将会看到更多的用户最终升级；毫无疑问，微软将会为此努力，而且会非常努力。除此之外，我们看到一个缓慢但稳定的进程，用户完全离开了 Internet Explorer，转向 Firefox 和其他浏览器；Arstechnica 最近报道说 [Firefox 的使用率首次超过了欧洲的 Internet Explorer](http://arstechnica.com/open-source/news/2009/03/firefox-3-marketshare-exceeds-internet-explorer-7-in-europe.ars)。不可避免地，总有一天 <abbr title="Internet Explorer 6">IE6</abbr> 会拥有足够少的受众，以至于我们可以像对待几年前的网景 4 一样对待它——将其视为我们不再需要支持的遗留技术，而其剩余用户则是我们不再需要迎合的任性固执的个人！

![IE6 Wanted DEAD](img/6012a35c702bac46e5da43a765353caa.png)

(真的很搞笑。你不会走进当地的手机店，抱怨你在 2001 年买的手机不能在 3G 网络上工作，也不能用它发送图片信息。然而，出于某种原因，少数网络用户的行为确实如此。)

无论如何，据我估计，大约还有 12 个月，我们就可以永远和支持者吻别了。当那一天到来时，我们将能够做一些以前在主流网站上做不到的有用的事情:

1.  **使用子选择器**

    不再需要定义无穷无尽的派生规则来抵消其他派生规则。使用 <abbr title="Cascading Style Sheets Level 2">CSS2</abbr> [子选择器](https://www.w3.org/TR/CSS21/selector.html#child-selectors)我们可以指定 <abbr title="Cascading Style Sheets">CSS</abbr> 规则，这些规则只适用于直接子代，而不适用于一般的后代。

2.  **充分利用 24 位 png**

    不再混合不同颜色背景下的图像，使边缘具有良好的抗锯齿效果。有了 <abbr title="Portable Network Graphics">PNG</abbr> alpha 通道支持，我们可以使用带有阴影、发光和其他不透明效果的图像，所有图形浏览器用户都可以安全地看到它们。

3.  **使用属性选择器**

    当我们可以用<abbr title="Cascading Style Sheets Level 2">CSS2</abbr>属性选择器比如`input[type="text"]`来处理输入时，就不必再为输入定义类型类了——比如`<input class="text" ... />`。我们甚至可以使用 <abbr title="Cascading Style Sheets Level 3">CSS3</abbr> [子串匹配属性选择器](https://www.w3.org/TR/css3-selectors/#attribute-substrings)(包括 <abbr title="Internet Explorer 7">IE7</abbr> 在内的所有现代浏览器都支持)，这对于定义适用于一系列不同属性值的选择器非常有用，从而显著减少了寻址具有类似`class`名称的元素组所需的代码量(例如)。

4.  **使用更广泛的显示属性**

    能够使用类似于`display:inline-block`的东西来代替`float:left`意味着不再有无休止的 float 中 float 中 float，或者稍微有点可疑的使用`overflow:hidden`，只是为了正确地清除块。(虽然我应该指出，我们不得不等到火狐 3 的这一个！)

5.  **使用最小宽度和最大宽度**

    虽然 <abbr title="Internet Explorer 6">IE6</abbr> 对 width 的实现和 [min-width](https://www.w3.org/TR/CSS21/visudet.html#min-max-widths) 的正确实现很像，但又不尽相同，也不是什么都管用；它对最大宽度没有任何影响。随着<abbr title="Internet Explorer 6">的结束，IE6</abbr> 将迎来区块布局设计的新一轮复兴，带来设计者多年来一直渴望的更大灵活性。

6.  扔掉 90%的 <abbr title="Cascading Style Sheets">CSS</abbr> 黑客(以及 90%需要它们的理由！)

    再也不用担心[离奇重复的人物](http://www.positioniseverything.net/explorer/dup-characters.html)、[神秘隐形的封号](http://www.positioniseverything.net/explorer/peekaboo.html)，或者[令人沮丧的双倍边距](http://www.positioniseverything.net/explorer/doubled-margin.html)；随着 <abbr title="Internet Explorer 6">IE6</abbr> 的结束，我们也不再需要诊断和修复如此大量的渲染错误。

7.  **添加每个人都能看到的缩写**

    虽然就我个人而言，我只使用`<abbr>`元素而从不使用`<acronym>`，但偶尔想起 <abbr title="Internet Explorer 6">IE6</abbr> 用户看不到扩展还是会让我感到厌烦，在我需要为他们编写脚本(但不能)的奇怪情况下更让我烦恼。

8.  **再次信任 z-index】**

    当我们思考为什么 X 层在 Y 层之上而它应该在 Y 层之下时，我们不再挠头，想知道我们可能做错了什么，只记得——哦——我们没有做错任何事情，这是[在 <abbr title="Internet Explorer 6">IE6</abbr> 中的一个堆栈上下文错误](http://therealcrisp.xs4all.nl/meuk/IE-zindexbug.html)。

9.  **节省时间和金钱**

    花费在黑客上的时间显著减少意味着开发时间更短，开发成本更低。

10.  再次尽情享受吧！

    写 <abbr title="Cascading Style Sheets">CSS</abbr> 会变成以前的乐趣。也就是说，直到我们的期望再次上升，并且 <abbr title="Internet Explorer 7">IE7</abbr> 成为我们的克星…

## 但是有三件事我们仍然需要等待

可悲的是，在 <abbr title="Internet Explorer 7">IE7</abbr> 也成为记忆之前，仍有许多事情我们无法不受惩罚地去做！

1.  **使用 <abbr title="Cascading Style Sheets">CSS</abbr> 计数器**

    所以我们仍然不能放弃不幸被否决的`start`属性，或者用[生成的内容](https://www.w3.org/TR/CSS21/generate.html)做任何我们想做的有趣的事情。

2.  **使用`box-sizing`属性**

    在 <abbr title="Internet Explorer 8">IE8</abbr> 和其他现代浏览器中实现的 <abbr title="Cascading Style Sheets Level 3">CSS3</abbr> [盒子大小](https://www.w3.org/TR/css3-ui/#box-sizing)属性，意味着我们可以*最终*为自己选择在特定情况下我们想要的盒子模型，并且一劳永逸地结束关于哪个是“正确”的乏味争论。

3.  **JavaScript 有什么改进吗**

    对于日常的 JavaScript 编程，我希望看到很多东西——结束事件目标分叉、单一事件监听机制、可靠和一致的计算风格属性检索——但所有这些都要等到 <abbr title="Internet Explorer 7">IE7</abbr> 也走上精灵之路，因为只有在 <abbr title="Internet Explorer 8">IE8</abbr> 中，脚本引擎才有所改进(尽管这些改进实际上意味着什么还有待观察！).

所以在你的日历上标记这一天，因为在 2010 年初，当 <abbr title="Cascading Style Sheets">CSS</abbr> 的更充分的潜力将最终开始显现！

*图片鸣谢:[迈克·罗德](http://www.flickr.com/photos/rohdesign/3315062242/)*

## 分享这篇文章