# 两种 AJAX: HTML++ vs Client / SOA

> 原文：<https://www.sitepoint.com/two-kinds-of-ajax-html-vs-client-soa/>

我认为这需要清楚地说明，根据来自 [technicalpursuit](http://www.technicalpursuit.com/) (西藏)的 Bill Edney 的[评论](https://www.sitepoint.com/ajaxlocalhost/#comment-13956)，因为我认为它反映了人们对 AJAX 的体验。

简而言之，我认为将会有两种 AJAX 应用程序，一种我称之为“HTML++ ”,另一种是比尔提到的[客户端/ SOA](http://en.wikipedia.org/wiki/Client/SOA) 。粗略地描述一下…

**HTML++**

AJAX 用于增强现有的 HTML 表单/用户交互，但基本范式仍然与“普通”web 应用程序相同。这种风格的一些关键气味；

1.  页面重新加载仍然经常发生
2.  *有可能*(如果你努力的话)优雅地降级到不支持 JS 的浏览器/关闭 JS 的浏览器。
3.  会话状态仍然驻留在服务器上。

实际上，这是每个人现在都在做的事情，并取得了不同程度的成功。对许多人来说，我认为“转向 HTML++的努力和成本将远远超过收益。实际上，我认为最好在服务器端使用类似于 [HTML_AJAX](http://blog.joshuaeichorn.com/archives/2006/02/13/ajax-and-latency/) 这样的工具来为你做“思考”(旁注:在给[这个演讲](https://www.sitepoint.com/ajaxlocalhost/)时，提到 JPSpan 实际上已经死了——[HTML _ AJAX](http://pear.php.net/package/HTML_AJAX)在某种程度上是它的继承者——有点像 [PEAR::SOAP](http://pear.php.net/package/SOAP) 和 [NuSOAP](http://dietrich.ganx4.com/nusoap/) )。

与此同时，一个有趣的“介于两者之间”的例子是 [script.aculo.us](http://script.aculo.us) [购物车](http://demo.script.aculo.us/shop)，我认为这个例子突出了这种差异。每当您将一个商品放入购物车时，它都会告诉服务器，然后检索购物车内容的新列表并更新客户端。换句话说，服务员是你购物车里东西的评判者。这很有趣，因为你可能会说购物车可以完全停留在客户端，只在你决定去“结账”时才向服务器发送任何东西。然而在实践中，购物车很可能被用在需要跨多个“页面”(或标签)工作的站点上，这些页面共享一个服务器端会话。

**客户端/ SOA**

正如 Bill 所说,[客户端/ SOA](http://en.wikipedia.org/wiki/Client/SOA) 将使用相同的。SOA 可能还不是一个容易交流思想的概念。我理解这个的角度是从问 [AJAX:什么是会话开始的？](http://ajaxblog.com/archives/2005/05/27/ajax-whats-a-session)。另一个角度来自于 [Cédric Savarese](http://www.4213miles.com/contact.php) 在 [Ajax 中的](http://formassembly.com/blog/ajax-not-all-about-xmlhttprequest/)[表单组装](http://www.formassembly.com/):这不完全是关于 XMLHTTPRequest (以及[第 2 部分](http://formassembly.com/blog/ajax-its-not-all-about-xmlhttprequest-part-ii/))，这一思路导致了他对[freja 框架](http://formassembly.com/blog/the-freja-framework/)所做的事情。

客户端/ SOA 的一些关键气味；

1.  页面重新加载很少发生，如果有的话。应用程序倾向于在单个浏览器窗口中运行。
2.  如果不维护独立的代码库，优雅地降级几乎是不可能的。
3.  会话状态主要由客户端处理。
4.  Javascript 和浏览器就像 Java 或。NET 运行时。
5.  这需要专业的开发人员

实际上，在这里使用 AJAX 这个词可能是错误的。为了实现这种风格的应用程序，你需要大量的 Javascript 胶水，而我们称之为 AJAX 的东西可能被隐藏得如此之深，以至于它几乎没有反映在你作为应用程序开发人员所称的 API 中。

据我所知， [Tibet](http://www.technicalpursuit.com/) 是目前唯一能够实现这一点的开源项目，而且目前还没有可用的代码——你需要等待下一个版本(即将发布)。同时，通过谷歌，你会发现[人](http://ajaxian.com/archives/tibet-pushing-javascript-to-its-limits) [一直到 2001 年 4 月](http://weblogs.asp.net/dfindley/archive/2005/08/06/421737.aspx)都对西藏赞不绝口。

以这种方式构建什么样的应用程序还有待观察。从表面上看，这些将等同于今天的桌面应用程序，但我认为这里的赢家*不会是那些仅仅试图重新发明已经有效的应用程序的人。例如，建立基于网络的办公套件的竞赛已经开始，但我认为这在很大程度上是一个愚蠢的游戏。具体来说，从 ms Word 的角度来看，如果你曾经看到过公司通常是如何处理 Word 模板的，并考虑过为什么 MS 开发了 [InfoPath](http://en.wikipedia.org/wiki/InfoPath) ，你就会意识到文字处理器作为一个“应用程序单元”已经不再有趣了。一些更像“活文档”的东西，与一群人试图组织的特定类型的信息相关，并且整合像协作、版本和工作流这样的东西要有趣得多。*

此外，这种应用程序可能会被另一种技术所掩盖，比如微软打包进 Vista 的东西或基于 XUL 的东西(也许只是 Firefox 扩展)。我认为这很大程度上取决于未来 12 个月会发生什么。

无论如何——这只是我的想法，危险地接近于市场架构，但是为了确定 AJAX 的相关性。

## 分享这篇文章