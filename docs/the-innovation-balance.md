# 创新平衡

> 原文：<https://www.sitepoint.com/the-innovation-balance/>

有很多人真的在挑战 DOM 和 CSS 的极限。你们都知道这个。但是，在某些时候，你会碰到一堵墙，那堵墙被命名为 Internet Explorer。

情况并不总是这样:要到达 Internet Explorer 墙，你现在要跨过以前那堵墙的废墟。如果你从那面墙上捡起一块饱经风霜的旧砖，你几乎可以看到上面写着“网景 4”的字样。但是那堵墙只是一堆旧石头，现在每个人都忽略了。Internet Explorer 是新的 Netscape 4。

这里的问题是:你会因此而退缩吗？我是说，l00k，00d！Firef0x haz 这些 k-rAd 新 t00ls！IE 是 teh sux0r！杀了 M$！

呃，也许不是。不过，这是一个严肃的问题；为了给 Firefox 用户提供更好的界面，我们应该在什么时候放弃 IE 支持？

有一个非常合理的说法是，这个问题的答案是:永远不会。不要做只属于 FF 的事情。打着“符合标准”的旗号说“嗯，是 IE 开发团队没有费心实现所有 CSS 的错”纯粹是诡辩，你也知道。Web 开发人员因使用特定于 Internet Explorer 的技术而受到谴责，这是理所当然的。有人记得[HTML+时间](http://msdn.microsoft.com/workshop/author/behaviors/time.asp)吗？CSS 中的 Javascript 表达式？你们中的一些人现在在嘲笑:innerHTML 怎么样？Contenteditable？XMLHTTPRequest？并不是所有不标准的东西都一定要扔掉。

有一个小的但正在增长的 DOM 操作类正在被发布，可以在 Firefox(可能还有 Safari 和 Opera)中工作，但不能在 IE 中工作。以我最近看到的两个非常好的黑客为例:Brad Fitzpatrick 的基于 Ajax 的共享白板和 Tim Taylor 的拖放式可排序列表。它们都是非常简洁的代码，我希望它们才华横溢的作者不会反对我在这里命名它们；他们也都不在 IE 工作。现在，既然 DOM 操作应该是在一个已经可用的网站上增加额外可用性的东西，一个完全不需要打开 JavaScript 就能工作的网站(谷歌，我又看着你了)，这应该没什么关系…但它确实有关系。值得称赞的是，两位作者都承认他们的代码不起作用，并暗示他们正在努力解决这个问题，但我们这些标准遵从者和鼓吹者需要小心，这些鼓吹者真的是标准的拥护者，而不是反对西雅图巨头的人。

## 分享这篇文章