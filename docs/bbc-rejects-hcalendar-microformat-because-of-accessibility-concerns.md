# BBC 拒绝 hCalendar 微格式，因为考虑到可访问性

> 原文：<https://www.sitepoint.com/bbc-rejects-hcalendar-microformat-because-of-accessibility-concerns/>

<abbr title="British Broadcasting Corporation">BBC</abbr> 宣布他们将[从他们的在线节目列表](http://www.bbc.co.uk/blogs/radiolabs/2008/06/removing_microformats_from_bbc.shtml)中移除 hCalendar 微格式，因为[与](http://www.webstandards.org/2007/04/27/haccessibility/) [ABBR 设计模式](http://microformats.org/wiki/abbr-design-pattern)的可访问性问题。

总结一下这个问题:一些微格式使用`<abbr>`元素的`title`属性来存储 <abbr title="International Standards Organisation">ISO</abbr> 格式的日期，作为用自然语言编写的日期的机器可读扩展，例如:

```
Meeting to be held on 
<abbr class="dtstart" title="1998-03-12T08:30:00-05:00">
	12 March 1998 from 8:30am EST
</abbr>
```

然而，这可能会对盲人用户产生严重影响，他们将屏幕阅读器设置为扩展缩写，因为屏幕阅读器所说的对用户来说基本上是胡言乱语。例如，Window Eyes 在其最大详细度设置时会这样说:

> 会议将于 19980312t 83000005000 举行

这同样会影响有认知障碍的用户，他们在工具提示中查看数据，面对同样无法理解的信息。日期不是缩写， <abbr title="International Standards Organisation">ISO</abbr> 格式的日期也不是展开；ABBR 设计模式在概念上和明显上都被打破了。

不幸的是，微格式社区并没有认真对待这个问题，尽管有相当多的争论和提供的替代方案。

我个人避免过多地卷入这场争论，但是我通过布鲁斯·劳森和詹姆斯·克莱格在 T2 网络标准项目中所做的工作来了解这场争论，试图找到并提出其他的设计模式；或者至少让微格式社区中的早期采用者足够认真地对待这个问题，寻找真正的替代方案，而不是将整个辩论斥之为<q>小鸡崽</q>，或者<q>屏幕阅读器的问题</q>。人们只能希望这样一个备受瞩目的组织做出的这一决定会给这场辩论注入新的活力。

就个人而言，我认为微格式是其简单性的受害者。我完全赞成让事情尽可能简单，但不能以牺牲可访问性为代价——以牺牲用户体验为代价让开发人员的生活更轻松是不可接受的。

我认为引入这样一个系统是愚蠢的，它明显地需要名称空间，而不使用名称空间。一个有明确命名空间的微格式不可能与现有的实现冲突，并且很容易被机器读取，没有任何歧义。因此，从这个意义上来说，我将支持 RDFa T1，认为它是通向更加语义化的网络的更好途径，在这个网络中，人类可读和机器可读的数据可以和平共存。

回到一个更普遍的问题——也许，如果不是因为 [Hixie 的标志性反 XHTML 信件](http://www.hixie.ch/advocacy/xhtml)(在我看来，它在每个重要方面都是错误的)我们现在可能都在使用 <abbr title="eXtensible HyperText Markup Language">XHTML，而命名空间信息的介绍将是微不足道的。只是因为这么多开发者坚持用</abbr> <abbr title="HyperText Markup Language Version 4">HTML 4</abbr> 命名空间就不能用了，为什么我们一开始就有这个问题。

## 分享这篇文章