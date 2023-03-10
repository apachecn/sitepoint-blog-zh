# @媒体 2007 伦敦-第一天总结

> 原文：<https://www.sitepoint.com/media-2007-london-day-1-wrapup/>

我现在在伦敦参加 [@media](http://www.vivabit.com/atmedia2007/europe/) 会议——我本希望在会议期间写博客，但是会场缺乏无线网络(我对会议唯一真正的抱怨)和劣质的酒店网络连接打消了这个念头！

第一天有几个演讲让我觉得特别有趣:

**[内特·科切利](http://nate.koechley.com/blog/)–高性能网站**
内特讲述了他们在雅虎所做的一些工作。为了提高网站的性能，最终列出一系列简单易行的规则，如果遵循这些规则，平均速度会提高 20-50%。

*   减少 HTTP 请求——这是最重要的规则，使用 CSS 精灵，结合脚本和样式表
*   使用 CDN(内容分发网络，例如 Akamai)。首先分发您的静态内容。
*   为所有文件添加过期文件头，而不仅仅是图像
*   Gzip 所有组件，不仅仅是 HTML。真正缩短用户下载时间。90%的浏览器支持压缩。Gzip 比 deflate 压缩得更多，也更受支持
*   把 CSS 放在文件头的顶部。使用 link，而不是@import，以获得更快的感知加载时间(不是实际加载时间)
*   将脚本移动到底部–在浏览器加载完脚本之前，脚本会阻止呈现文件中位于其下方的所有内容
*   避免 CSS 表达式
*   使 JS 和 CSS 成为外部的
*   减少 DNS 查找–阻止并行下载。Bets 的做法是使用 2-4 台主机并使用 keep-alive
*   缩小外部和内部的 JavaScript..使用缩小而不是模糊
*   避免重定向
*   关闭 ETags

在 Yahoo！搜索页面的性能提高了 40-50%。你可以跟随他们在 YUIblog 的工作。

**[马克·波尔顿](http://markboulton.co.uk/)——改进排版的五个简单步骤**
作为在演讲中使用武术的众多演讲者之一，马克将优秀的排版设计比作李小龙的武术风格。马克列出了五个简单的步骤来改善你的网页排版。

1.适当且适应性强

字体讲述故事——网络缺乏可用的字体，但是你仍然需要使用合适的字体。让你的排版与你想表达的内容一致。不要让字体(或设计)妨碍文字。
适应要求，适合故事。

2.使用节奏
使用垂直节奏(Rich Rutter 的 24ways 文章是一个很好的参考)
移除浏览器默认设置
在边注上使用递增的前导

3.平衡和对比
使用“光灰色”(眯着眼睛模糊文字或使用模糊滤镜可以帮助你确定你的平衡有多好——你应该以中等重量为目标，尽量减少暗光灰色。你可以通过使用不同的、更开放的字体以及增加行距和行高来使你的文本变得更亮
使用 sans 和 serif 字体来提供对比。

4.为正确的工作使用正确的工具。连字符不是破折号！使用长破折号或短破折号。

5.使用系统
使用网格。你的单位尺寸(网格的基本尺寸)以你的字体大小为基础。将所有的与网格对齐。

这一天以乔·克拉克题为“当无障碍不是你的问题”的闭幕演讲结束。Joe 解释说，网站作者不应该担心可访问性的具体方面——如果你坚持使用良好的语义 HTML，你就不必担心坏屏幕阅读器会如何阅读你的内容。您不需要总是为缩写和首字母缩略词提供扩展。如果不合适，你不需要总是写得很简单。

第一天到此为止！

## 分享这篇文章