# phpGroupware 和 XSLT

> 原文：<https://www.sitepoint.com/phpgroupware-and-xslt/>

根据 Sourceforge 的说法，如果你接触 PHP 有一段时间了，你可能听说过 [phpGroupWare](http://www.phpgroupware.org) ，现在已经是第四年了(对于 PHP 来说，这已经是老的了)。

正如[丹·库肯德尔](http://kuykendall.org/)最近评论[这里](https://www.sitepoint.com/blog/)，phpGroupware，[下一代](http://wiki.phpgroupware.homelinux.org/index.php?page=nextgen)正在进行中。过去，在与永无止境的洪水搏斗时，我设法通过电子邮件与 Dan 进行了至少一次有意义的聊天，他对 XSLT 的使用有一些非常有趣的想法。

比如下一代 phpGroupWare 的[演示，登录(user: admin，pass: temppass)。四处冲浪，享受一些美好的视觉效果。现在让我们惊喜一下——从你的浏览器“查看>来源”…。HTML 在哪里？假设您使用的是 Mozilla 或 IE 的最新版本，您应该会看到一个 XML 文档。在顶部，你会看到这样的内容:](http://nextgen.phpgroupware.org/demo/index.php)

它实际上是你的浏览器将页面转换成好看的东西。对于没有内置 XSLT 处理器的浏览器，Dan 在服务器端用 PHP 将页面转换成 HTML。提供了一种有趣的替代方式，替代了通常在 PHP 中使用的模板化方式，并且*可能*意味着新浏览器的用户可以更快地加载页面，并且为基于 REST 的 web 服务开辟了道路(旁注 Sitepoint 最近也在他们的 [RSS 提要](https://www.sitepoint.com/add-semantic-richness-to-your-markup-with-rdf-ease/)中使用了类似的技巧。)

值得注意的是，下一代 phpGroupWare 打包了 SOAP 和 XML-RPC 接口，为访问 phpGroupWare 提供了进一步的选择，也许为 XUL 客户端开辟了道路。

## 分享这篇文章