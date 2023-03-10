# 原型

> 原文：<https://www.sitepoint.com/prototypes/>

只是转储一些链接到我最近遇到的一些原型开源项目，这些项目值得注意，因为它们推动了前沿，也因为看代码给了乐观的理由。

**PHP 性能 SQL**

[http://phprestsql.sourceforge.net/](http://phprestsql.sourceforge.net/)——直接通过 PHP 以 XML 的形式发布你的 MySQL 数据库，使用友好的 URL(尽管我们可以去掉“？”).

我在这里想到的最初的问题，没有看得太深

–分页结果集？如果没有它们，当你有很多行时会发生什么…

–标签命名。我们需要名为“表格”和“行”的标签吗？ [Keith](http://keithdevens.com/weblog/) 最近提出了一个可能相关的问题[这里](http://keithdevens.com/weblog/archive/2004/Dec/24/SvP.tags)——将“表格”替换为复数(如“用户”)，将“行”替换为单数(如“用户”)。

**Qwad 框架**

[http://www.qwad.com.au/code/doku.php?id=qwad_framework](http://www.qwad.com.au/code/doku.php?id=qwad_framework)–“QWAD 框架旨在使富互联网应用的开发变得更加容易。可以创建跨浏览器的 web 应用程序，提供用户期望从桌面应用程序获得的丰富 GUI 功能。”我知道有一些 Javascript 窗口库，但是这里的代码让我很乐观，而且我有偏见，因为一些例子使用了 [JPSpan](http://jpspan.sourceforge.net) 。

**xulRecordSet**

http://xulrecordset.sourceforge.net/–“xulRecordset 是一个用记录集行为创建 XUL 部件库的项目。”。这很酷的一点是它使用 [XBL](http://www.mozilla.org/projects/xbl/xbl.html) 来扩展 XUL 标签集，并使记录集易于使用(此外，它还使用 [JPSpan](http://jpspan.sourceforge.net) 所以更多的偏见)。远程 API 目前可能粒度过细，无法提供良好的性能——在此补充我的想法[但我认为这个总的概念是朝着正确方向迈出的一大步。](http://sourceforge.net/forum/forum.php?thread_id=1201952&forum_id=432051)

撇开傲慢和令人恼火不谈(对不起):如果你想超越原型，别忘了你的 [PHP 单元测试](http://www.lastcraft.com/simple_test.php)(或者[JSUnit](http://www.edwardh.com/jsunit/)——尝试 JS 模拟[这里](http://jpspan.sourceforge.net/wiki/doku.php?id=javascript:mock))。

**更新**

在我忘记之前，看看[http://map.search.ch/](http://map.search.ch/)–*不是一个原型，而是一个全功能的服务，由 Javascript 和 XMLHttpRequest 支持(检查拖动/缩放)——没有 Flash /小程序等。参与其中。我们对谷歌建议感到兴奋…*

关于 map.search.ch 还有一点——“后端”是 mapdata . search . ch——这是来自 [LiveHttpHeaders](http://livehttpheaders.mozdev.org) 对 http://mapdata.search.ch/[的请求的回复中的关键一行；](http://mapdata.search.ch/)

 `X-powered-by: PHP/4.3.9`

不言自明。

## 分享这篇文章