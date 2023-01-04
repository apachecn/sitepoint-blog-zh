# 我讨厌 HTML5 的 5 件事

> 原文：<https://www.sitepoint.com/5-things-i-hate-about-html5/>

我爱 HTML5。Viki Hoo 也是如此，她最近的文章“我喜欢 HTML5 的 5 件事”陈述了原因。但 HTML5 花园中并非一切都是美好的。所以，为了恢复 SitePoint 世界的平衡，我有五个讨厌的地方:

## 1.多余的标签

HTML5(这项技术)提供了 30 个新标签，尽管你永远不会使用其中的一半。这很好，但是考虑一下我们如何在 HTML4/XHTML1 中编码导航列表:

```
 <ul id="nav">
	<li><a href="link1.html">link 1</a></li>
	<li><a href="link2.html">link 2</a></li>
	<li><a href="link3.html">link 3</a></li>
</ul> 
```

以下是 HTML5 的等效内容:

```
 <nav>
	<ul>
		<li><a href="link1.html">link 1</a></li>
		<li><a href="link2.html">link 2</a></li>
		<li><a href="link3.html">link 3</a></li>
	</ul>
</nav> 
```

它有一个小小的语义优势，但它在现实世界中真的有影响吗？号码

它也有增加页面权重和添加不必要的 DOM 元素的缺点。我正在使用新的语法，但是，在多年赞美干净代码的好处和避免分裂之后，它让我觉得有点脏。

## 2.糟糕的开发工具

你的 IDE 或编辑器完全支持 HTML5、CSS3 和新的 JavaScript APIs 吗？我打赌不会。

在过去，我喜欢使用 Firefox 扩展，如 [HTML 验证器](https://addons.mozilla.org/en-US/firefox/addon/html-validator/)，当你查看一个页面时，它会突出显示你的标记中的问题。今天，我们有一些 HTML5 验证工具，但它们通常是错误的测试版，你需要向在线服务提交代码。它又慢又麻烦——我很少费心。

## 3.多媒体疯狂

新的`video`和`audio`标签非常棒。不幸的是，正在进行的供应商之争意味着你的视频必须用 WebM、H.264 和 Ogg Theora 编码才能在 HTML5 浏览器中工作。哦，是的，你将需要一个针对 IE6/7/8 的闪存回退——如果你这么做了，为什么还要麻烦这个`video`标签呢？

## 4.浏览器支持

我可以大声抱怨 IE6/7/8 和 IE9/10 没有在 Windows XP 上发布。然而，在我看来，最让我恼火的是实现级别的多样性。如果您想使用 HTML5 特性，您必须不断查阅文档并彻底测试代码。

考虑像日期输入字段这样基本的东西。Opera 展示了一个可爱的日历选择器。Firefox、Chrome 和 Safari 知道日期是必需的，但提供基本的控制和验证。IE9 退回到一个哑文本框。具有讽刺意味的是，您可能想要禁用 Opera 的实现，并恢复到 JavaScript 解决方案。

## 5.炒作和混乱

媒体猛烈抨击“HTML5”这个术语，它适用于任何酷的网络效果。哪怕是动画 GIF。虽然当您与客户交谈时，这不一定是一个问题，但它会引起开发人员的困惑:

*   有些人认为 HTML5 与 HTML4 有着本质的不同，但它确实是一种进化。
*   有些人认为使用 HTML 可以达到效果，但实际上它是 CSS3 或 JavaScript APIs。
*   有些不会碰 HTML5，因为 W3C 规范还在草案阶段。
*   有些人不会考虑 HTML5，因为他们需要支持传统浏览器。
*   让我们不要忘记【CSS3 厂商前缀给和[带来的麻烦，为什么我们需要修复它](https://www.sitepoint.com/css3-vendor-prefix-crisis-solutions/)。

如果你已经在这个行业工作了几年，这些概念可能是显而易见的，但是谁会想成为一个新手呢？

让我们明确一点:HTML5 的好处大于问题。我用它，建议你尽早切换。但是请记住，使用尖端技术是要付出代价的；不要假设代码会在每个支持 HTML5 的浏览器上如你所愿地工作！

## 分享这篇文章