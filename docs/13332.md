# OSCON 2007:本地化冒险

> 原文：<https://www.sitepoint.com/oscon-2007-adventures-in-localization/>

Wil Clouser 是 Mozilla 公司的一名网络开发人员，在 T2 的 Micropipes::Blog 上写博客。迈克·摩根是 Mozilla 公司 T4 分部的网络开发经理，在 morgamic.com[有一个博客。](http://morgamic.com/)

这个演讲描述了 Mozilla 公司的网络资产国际化的过程，特别是 mozilla.com 的 T1 和 addons.mozilla.org 的 T3，这两个网站每天的点击量几乎达到了 2 亿次。网站的本地化进程仍然落后于 Firefox 客户端，它提供了 40 多种不同的语言环境。

他们首先讨论了本地化的一些陷阱，如复数和名词性，然后继续谈论他们选定的本地化 Mozilla Web 属性的系统，这些属性是模板中静态文本和从数据库中提取的动态内容的组合。

对于静态文本，他们最终使用了 GNU 的 [GetText](http://www.gnu.org/software/gettext/) ，因为这是一个已经被广泛使用的稳定、成熟的解决方案。它有很好的文档、多元化支持和翻译人员可能已经熟悉的各种工具。

对于动态内容，他们使用了一个简单的数据库驱动的解决方案，基于字符串 ID 和地区键进行查找。(他们最初看到了 [PEAR::Translation2](http://pear.php.net/package/Translation2/) ，但结果证明对于他们的目的来说不够灵活。)这个系统的优点是它的简单性，以及它很容易应用于数据库中任何地方的动态内容查找的事实。缺点是以一种允许语言回退的方式获取数据需要很大的表和复杂的连接。

他们还谈到了一些正在开发中的改进的本地化工具，例如 Canonical 的 [Launchpad](https://launchpad.net/) ，以及各种让社区参与进来并帮助解决本地化问题的方法。

他们演示的幻灯片可在此在线获取[。](http://people.mozilla.com/~morgamic/presentation.pdf)

Wil 还有一篇后续博文叫做[网站本地化的十个技巧](http://micropipes.com/blog/2007/07/26/ten-tips-for-website-localization/)。

## 分享这篇文章