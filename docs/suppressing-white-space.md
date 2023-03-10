# 抑制空白

> 原文：<https://www.sitepoint.com/suppressing-white-space/>

*…发帖人* [大卫·梅德洛克](https://www.sitepoint.com/forums/member.php?u=24385) *:*

周末，我碰巧和另一个开发人员一起做一个项目，他给我发了一封电子邮件，内容是关于他正在使用的自定义标签的问题。他说他的文件中有过多的空白。事实上，包含所有空白的文档大约为 40KB，而没有空白的文档大约为 16KB。呀！

这样做的原因是因为在页面的执行中，服务器必须将 CFML 翻译成应用服务器可执行的 p 代码。在转换中，CFML 变成空白。

当您在显示文档中有 ColdFusion 代码，然后查看源代码时，您会看到 ColdFusion 代码所在的所有地方现在都是空白。这可能会导致你的文件臃肿，它可以吃掉你的带宽很快。

我发现抑制空白的最好方法是进入 ColdFusion Administrator，转到设置页面，然后单击“启用空白抑制”。这将极大地减小页面的大小，特别是当你在做一些数据库密集型的事情，并且有大量的查询时。这也将使你的代码更具可读性。

我知道在代码中有一些方法可以做到这一点，使用 cfprocessingdirective、cfsetting 和 cfsilent，但是有了在服务器本身中设置它的能力，我真的看不出有任何理由必须在代码中做到这一点。即使你在共享主机上，你也应该给他们发邮件，让他们为你做这个小小的改变。这将减少所使用的带宽，他们应该能够为您做到这一点没有问题。(如果他们问你 ColdFusion 管理员是什么，那就另找主机！)

OReilly 的《ColdFusion 编程》一书中有更多关于显示输出的信息。第 11 章是你想看的地方。

顺便提一下，我四处查看了一下，看看启用空白抑制是否对服务器性能有任何明显的影响，我还没有看到任何关于它的东西。我可以看到它可能会稍微减慢速度，因为它现在必须自己清理，但它也可能会加快一点点，因为它在内存方面处理的数据更少…如果有人有这方面的参考资料，我很有兴趣看看。

## 分享这篇文章