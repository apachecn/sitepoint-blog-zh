# PHP / Web 应用程序集成

> 原文：<https://www.sitepoint.com/php-web-application-integration/>

我现在忘记了我是如何看到这篇文档的，甚至不能告诉你作者是谁(希望他们能站出来)，但不管怎样，这是一篇有趣的文章:[两个或更多 web 应用程序集成的方方面面](http://dev.nei.ch/integration_aspects.pdf) ( `application/pdf`)。不久前通过[画廊](http://gallery.menalto.com/)论坛/邮件列表中的一个找到了这个链接，这要感谢在谷歌上搜索完全不同的东西的偶然结果。

不管怎样——除了记忆丧失——这基本上是 Gallery2 开发团队的一个成员写的一篇简短的“研究论文”,内容是关于如何进行 web 应用集成。如果你想知道 Gallery 是一个(也许是最流行的)PHP 图像库应用程序。画廊 2 是一个完整的重写，并于去年秋天发布。/公告[此处](http://developers.slashdot.org/developers/05/09/13/2129233.shtml)。

回到集成，这是一个在我看来还没有很好解决的领域，无论是在 PHP 还是其他网络平台。基本要求通常源于“我如何为我的网站访问者提供一个登录我的论坛/维基/博客/画廊等的单点登录。?"。

[gallery2 论文](http://dev.nei.ch/integration_aspects.pdf)确定了集成 web 应用程序的以下关键“方面”(项目的选择有特定于 PHP 的倾向，但相信它仍然广泛适用于任何 web 平台)；

*   主从关系和通信
*   用户和组管理
*   会话管理
*   证明
*   URL 生成
*   视觉整合
*   (权限管理)
*   控制访问
*   搜索和其他 API 接口
*   命名空间冲突
*   环境

会让它自己说话。实际上，这个文档现在需要扩展更多的细节和建议，以一种通用的方式(如果可能的话)解决这个问题，这种方式可以应用于任何(PHP)应用程序。

实际上，另一种看待 web 应用程序集成的方式是*在哪里*做，比如在哪个层？这可以归结为。

*   在数据方面:例如，破解 App Y 的登录逻辑以使用 App X 的“用户”表。这通常看起来是最简单的方法，但在实践中，可能会导致不得不重写(或复制/粘贴)应用 Y 的重要部分，并意味着您需要[跟踪您的黑客](https://www.sitepoint.com/track-your-hacks-with-cvs/)。每次我去这里，都达到了“从头开始写我自己的作品会更少”的地步。
*   在代码方面:API、智能包含等。我想这通常是可行的方法，但是需要应用程序开发团队跳出框框思考，考虑其他应用程序如何以及在哪里可以插入他们的应用程序。我在 PHP 中见过许多不同的方式，从[fud forums](http://fudforum.org/forum/)[fudapi.inc.php](http://cvs.prohost.org/c/index.cgi/FUDforum/getfile/install/forum_data/scripts/fudapi.inc.php?v=1.18)(一个包含类似`fud_fetch_full_topic`的函数的可包含的 PHP 脚本)和 [Horde 框架的](http://www.horde.org/) ["COM"](https://www.sitepoint.com/horde-first-signs-of-a-com-for-php/) 到 [Serendipity 的](http://www.s9y.org) [embed "mode"](http://www.s9y.org/55.html) 和 [dwBliki](http://www.ezpdo.net/blog/cats/dwbliki/) 。这个话题真的需要很长很长的讨论，这是我要避免的。
    但是当我在这里的时候，有一个半相关的提示——[Scarlet](http://www.hawklab.jp/scarlet/)([en](http://babelfish.altavista.com/babelfish/trurl_pagecontent?lp=ja_en&url=http%3A%2F%2Fwww.hawklab.jp%2Fscarlet%2F))一个用于 PHP 的[依赖注入容器](http://en.wikipedia.org/wiki/Dependency_injection)。顺便说一下，这不是唯一的 PHP DI 实现——我们将不得不看看 Pawel 在[周五](http://www.phpconference.co.uk/)会说些什么。同时[杰夫](http://www.procata.com/blog/)有事情[在酝酿](http://cvs.sourceforge.net/viewcvs.py/wact/wact/framework/di/di.inc.php?rev=1.8&view=auto)。
*   在 HTML:如 [Sitemesh](http://www.opensymphony.com/sitemesh/) 方式(或[PHP Mesh](http://trypticon.org/software/phpmesh/)——捕捉并合并你的 HTML 服务器端。XSLT 可能也适用于这里。也相对容易，但我想也容易打破。
*   …我想还有一个——[grease monkey](http://greasemonkey.mozdev.org/)——把问题推到浏览器上。

无论如何——从[结果](http://codex.gallery2.org/index.php/Gallery2:Integration:Available_Integrations)来看，画廊团队当然有一些经验可以分享。

以一个愿景结束:让 2006 年成为我们真正实现 PHP 应用程序集成的一年怎么样？

## 分享这篇文章