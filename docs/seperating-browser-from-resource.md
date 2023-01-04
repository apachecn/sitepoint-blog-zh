# 将浏览器与资源分离

> 原文：<https://www.sitepoint.com/seperating-browser-from-resource/>

*免责声明:之前已经以不同的方式说过了——例如，搜索[将导航与结构](http://www.google.com/search?q=separating+navigation+from+structure)分开，或者[将行为与结构](http://www.google.com/search?q=separating+behaviour+from+structure)分开——只是补充一下我的观点。*

将内容和风格分开的概念，几乎成为了网页设计的公认准则，其好处众所周知

最近我一直在想把*浏览器*和*资源*分开的想法。在这个上下文中，我说的“浏览器”是指 web 应用程序中的用户界面元素，它允许用户四处走动(导航)、修改内容(表单)等。而“资源”是指在网站上发布的文档/内容。

这个思路有多个提示；

–个人抱怨:我讨厌*为 web 应用程序编写*管理界面。“前端”——网站访问者看到的东西——没问题，但管理界面通常更复杂，同时只对有限的使用群体可见，这让我抓狂——对于那些往往有非常进化的观点的人来说，有很多工作要做。

–另一个个人抱怨:我(通常)讨厌使用管理界面。例如，浏览/管理用户列表往往很痛苦。每个页面请求都要重新加载相同的管理工具(减慢速度)。在用户列表和单个用户视图之间切换往往会涉及过多的数据库查询(除非我聪明地使用了选项卡式浏览——即。违反了接口的预期用途)。与使用 grep 在命令行上工作或尝试搜索和替换相比，像搜索功能这样的东西永远不够强大(附注:年度工具名称:【http://fart-it.sourceforge.net/)。

罗伊·菲尔丁斯的论文[中提出的休息(表征状态转移)概念](http://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm)在这里[做了简洁的总结](http://www.xfront.com/REST-Web-Services.html)。不久前， [Jeff](http://www.procata.com/blog) 在 [Sitepointforums 上发起了一个关于 REST](https://www.sitepoint.com/forums/showthread.php?t=166693) 的讨论，提出了一个问题“有什么我们可以应用到面向用户界面的 web 应用程序中的吗？”。从 web 应用程序的角度来看，我对 REST 的解释是“REST 似乎表明你有用 URL 表示的*文档*和用于编辑(或者导航)它们的*工具*”。默文在讨论中发表了一些有见地的评论，如 [this](https://www.sitepoint.com/forums/showpost.php?p=1210095&postcount=35) 。底线；"基于 REST 的架构有什么好处？"似乎你的网站“资源”(内容/数据)变得不仅仅是网络浏览器可以使用——许多不同类型的应用程序可以“消费”它们(网络服务、专门的桌面内容管理工具、博客聚合器等)。).

–IE 和 Mozilla 的最新版本对 XSLT 有不错的支持(如 Sitepoints [RSS feed](https://www.sitepoint.com/add-semantic-richness-to-your-markup-with-rdf-ease/) 所示)。这提供了将“浏览器”与“资源”分开发布的可能性——对于 web 浏览器，他们可以加载导航/工具来“浏览”与正在浏览的文档分开的站点，而使用的其他应用程序可以简单地忽略样式表。给定的样式表可能应用于站点上的许多文档(甚至可能是整个站点)，所以只需要加载一次。XSLT 不是每个人都喜欢的，但是 think 说明了拥有“粘性”用户界面定义的重要性，它与浏览器一起，而文档/资源是单独加载的。

–最近的 IE / Mozilla 版本也有“可行的”Javascript 实现。我的意思是，它的标准符合率为 95%,而且现在编写在两种浏览器下都能正常运行的代码也更容易了。渐渐地，网络开发者开始意识到 JavaScript 远比看上去的要多。如果你看看[这个例子](http://w3future.com/html/orgcharts.html)，你就明白了。通过这种方式可以构建强大的用户界面。

–有了 Mozilla、IE 和 Safari 中可用的 [XMLHttpRequest](http://www.xulplanet.com/references/objref/XMLHttpRequest.html) ，使用 Javascript 就有可能构建“粘性页面”,提供网站所需的所有用户界面元素。换句话说，你首先加载一个页面，其中包含了[菜单](http://www.brainjar.com/dhtml/menubar/demo.html)、[标签](http://javascript.internet.com/navigation/tab-control-simulation.html)，甚至[窗口](http://www.brainjar.com/dhtml/windows/demo.html)所需的一切。从“粘性页面”可以通过 XMLHttpRequest 加载资源。

–以 XUL 为首的丰富的网络客户端即将出现。在[这里](http://www.phppatterns.com/xul/)使用 XUL 之前，我已经试验过“增强”(实际上在这种情况下是降低)用户的浏览体验。通过提供“粘性菜单”，定位单个文档不再需要多次重新加载页面来遍历通常以静态 HTML 形式“内嵌”呈现的导航。实际上，DHTML 仍然是比今天的 XUL 更安全的赌注，但我认为 XUL 正在成功地让人们重新思考我们所知道的网络。

在我看来，将浏览器与资源分离的主要好处是灵活性和可重用性。

你在网站上发布的文档/数据(可能)可供其他应用程序使用，而不是网络浏览器，随着技术的进步，有希望保留它们的相关性。通过将这种分离作为目标，开发/收集可应用于许多站点的标准工具/部件(DHTML 或其他)成为可能；也许这里的先驱是桌面博客聚合器。作为站点管理员，使用相同的命令行实用程序进行“grep”或搜索和替换的可能性更大。

作为一个副作用，从最终用户的角度来看，带宽也得到了保留，感知性能应该得到了提高，因为 UI 只加载一次。

无论如何——有人觉得这是真的吗？

## 分享这篇文章