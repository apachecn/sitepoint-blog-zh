# 十大 IIS 技巧

> 原文：<https://www.sitepoint.com/top-10-iis-tips/>

作为一名 IIS 管理员，有时不得不抵挡来自声称天生服务器优势的 Apache 管理员的所有侮辱，这简直令人恼火。

一般来说，关于 Web 管理的讨论是从困扰 IIS 的各种安全漏洞以及该平台在过去一年中受到的负面报道开始的。然后它总是转移到一个讨论，关于 Netcraft 和其他统计网站如何显示 Apache 是网络上的主导服务器，某个大网站如何使用 Apache，或者如何有这么多的[酷模块添加到 Apache](http://httpd.apache.org/docs/mod/) 。

指出大量未经确认的公司内部服务器运行 IIS 它也是一个免费的服务器(因为它是操作系统自带的)；或者说，事实上有很多 IIS 的[酷插件(包括许多](http://www.iisfaq.com/default.asp?View=P11)[提供的源代码](http://www.genusa.com/isapi/isapisrc.html))并不能阻止这些服务器沙文主义者的观点。

然而，与其抱怨粗鲁的 Apache 管理员，我认为更有用的回应可能是简单地写下我发现的一些改进 IIS 的方法。所以，不要再拖延了，这里是我的十大技巧，让你充分利用你的 IIS。

##### 技巧 10:定制你的错误页面

尽管这很容易做到，但似乎很少有人利用错误页面定制。只需选择 MMC 中的“自定义错误”选项卡，并将每个错误(如 404)映射到适当的 HTML 或 ASP 模板。[详情请点击此处](http://www.15seconds.com/issue/980210.htm)。如果你想要一个更简单的解决方案——或者如果你想让开发人员处理映射而不允许他们访问 MMC——使用像 [CustomError](http://www.customerror.com/) 这样的产品。

##### 技巧 9:深入元数据库

如果您认为 Apache 很强大，因为它有一个配置文件，那么看看元数据库。通过编辑元数据库，您可以对 IIS 做任何您想做的事情。例如，您可以创建虚拟目录和服务器；停止、启动和暂停网站；以及创建、删除、启用和禁用应用程序。

Microsoft 提供了一个名为 MetaEdit 的 GUI 实用程序，它有点类似于 RegEdit，可以帮助您读写元数据库。在此下载[最新版本](http://support.microsoft.com/default.aspx?scid=KB;EN-US;q232068&)。但是，要真正打动这些 UNIX 管理员，并通过学习如何以编程方式操作元数据库来充分利用它，您需要尝试命令行界面，正式名称为 IIS 管理脚本实用程序。它的简称是 adsutil.vbs，您可以在 C:inetpubadminscripts 或% SystemRoot % system32 netsrvadminsamples 中找到它，还有许多其他有用的管理脚本。

不过，需要注意的是:就像 Apache conf 文件一样，元数据库对于 Web 服务器的运行至关重要，所以不要破坏它。[先备份一下。](http://support.microsoft.com/default.aspx?scid=KB;EN-US;Q300672& )

##### 技巧 8:给你的 URL 添加拼写检查

Apache 的人总是吹嘘 Apache 能够做的很酷的小把戏——特别是因为有大量的模块可以扩展服务器的基本功能。其中最酷的是使用 mod_speling 模块修复 URL 输入错误的能力。

嗯，多亏了 Port80 软件，现在看来 IIS 管理员也可以做到这一点，使用一个名为 [URLSpellCheck](http://www.urlspellcheck.com/) 的 ISAPI 过滤器。你可以在他们的网站上查看，通过尝试像 www.urlspellcheck.com/fak.htm,·www.urlspellcheck.com/faq1.htm 这样的网址——或者任何其他你想犯的简单错误。

##### 技巧 7:重写你的 URL

清理你的 URL 有各种各样的好处——它可以提高你的站点的安全性，减轻迁移的痛苦，并且为你的 Web 应用提供一个额外的抽象层。例如，如果您可以重新映射 URL，从 ColdFusion 迁移到基于 ASP 的网站并没有什么大不了的。

Apache 用户长期以来一直吹嘘 mod_rewrite 的巨大威力 URL 重写的标准 Apache 模块。现在，这种类型的 IIS 产品有十几个版本——其中许多比 mod_rewrite 更容易使用，mod _ rewrite 倾向于假设熟悉正则表达式的奥秘。例如，检查出 [IIS 重写](http://www.qwerksoft.com/products/iisrewrite/)或 [ISAPI 重写](http://www.isapirewrite.com/)。所以不要再吹牛了，阿帕奇游击队！

##### 技巧 6:添加浏览器检测

有很多方法可以建立网站，但是假设每个人都有一个特定的浏览器或屏幕大小是非常愚蠢的。Simple _JavaScript sniff 脚本用于客户端浏览器检测，但如果你是 IIS 用户，你可以使用 CyScape 的一款名为 [BrowserHawk](http://www.browserhawk.com/ ) 的产品做得更好。Apache 世界真的没有什么东西可以与这个流行的、成熟的和得到良好支持的产品相比。

说到 CyScape，他们最近增加了一个有趣的相关产品，名为 [CountryHawk](http://www.browserhawk.com/products/country/intro.asp) ，有助于位置检测，但到目前为止，我还没有语言或位置敏感的内容来保证试用它。

##### 技巧 5: Gzip 站点内容

浏览器可以处理 Gzipped 和 deflated 内容，并即时解压缩。虽然 IIS 5 有一个内置的 gzip 特性，但它已经坏了。进入像 [Pipeboost](http://www.pipeboost.com/) 这样的产品，给我们更好的功能——类似于 Apache 用户已经享受到的 [mod_gzip](http://www.remotecommunications.com/apache/mod_gzip/) 的功能。不要浪费你的带宽——即使谷歌对它的内容进行编码，它们的页面也很小。

##### 技巧 4:缓存你的内容

当我谈到提高性能的话题时，记得让你的站点缓存友好。您可以直接从 MMC 为不同的文件或目录设置过期标题。只需通过 IIS MMC 右键单击一个项目，翻到“HTTP Headers”选项卡，就可以了。如果您想以编程方式设置缓存控件标题，或者更好的方式，让您的站点开发人员来做，那么使用类似于 [CacheRight](http://www.cacheright.com/) 的东西。如果你想更进一步，添加反向代理缓存，特别是对于生成的内容，使用像 [XCache](http://www.xcache.com/home/default.asp?c=45&p=352) 这样的产品——它也支持压缩。

充分利用缓存可能会花费更多的时间和费用，但是当您看到您的日志因为不包含大量无意义的 304 响应而缩小，并且您的带宽消耗像石头一样下降(即使您的总页面浏览量在同一时期有所增加)时，您将开始理解为什么这个特别的技巧如此重要。缓存友好型网站非常少见，但是网上有大量关于正确使用的巨大好处的信息。查看 Brian Davidson 的页面，这份来自 Mark Nottingham 的[俏皮教程，以及](http://www.mnot.net/cache_docs/)[AOL 对这个主题的看法](http://webmaster.info.aol.com/index.cfm?sitenum=2&article=12)。

##### 技巧 3:优化您的服务器

IIS 调优不是一个小话题——整本书和课程都致力于此。但是一些好的、基本的帮助可以在网上找到，比如这篇来自 IIS 专家 Brett Hill 的文章，或者这篇来自微软自己的知识库文章。然而，如果你不想弄脏自己的手——或者负担不起将自己变成专家的时间和费用——看看 XCache 制造商的 [XTune](http://www.xcache.com/home/default.asp?c=51&p=334) 。它的性能调整向导引导您完成调整 IIS 环境的过程，并在此过程中提供专家建议。

##### 技巧 2:通过简单的修复来保护您的服务器

当然，人们会攻击网站，但如果你愿意付出哪怕一点点努力，你也不必坐以待毙。

首先，不要通过显示 HTTP 服务器头来宣传您运行 IIS 的事实。用类似于[服务器屏蔽](http://www.servermask.com/)的东西移除或替换它——这可能是你所花的最划算的 25 美元。除此之外，您还可以删除不必要的文件扩展名以更有效地伪装您的服务器环境，并扫描请求 URL 以寻找漏洞利用的迹象。

有许多商业产品可以执行用户输入扫描，微软提供了一个名为 [URLScan](http://www.microsoft.com/technet/treeview/default.asp?url=/technet/security/tools/tools/urlscan.asp?frame=true) 的免费工具来完成这项工作。URLScan 与 [IISLockDown](http://www.microsoft.com/technet/treeview/default.asp?url=/technet/security/tools/tools/locktool.asp) 一起运行，IISLockDown 是一个标准的安全包，可能应该安装在地球上的每一台 IIS 服务器上。这些都是简单的修复方法，可以带来巨大的回报，所以现在就开始实施吧！

##### 技巧一:补丁，补丁，补丁！

好吧，我们在 IIS 的世界里确实需要修补我们的系统和修补程序。然而，作为一名前 Solaris 管理员，我不得不在那里做同样的事情，所以我不知道为什么这是一个大惊喜。你真的需要跟上补丁。微软当然是最权威的来源，但是如果你也可以使用备受推崇的 www.cert.org 的 T2。只需在“IIS”上搜索。

好了，现在你有了:IIS 管理员改进服务器的 10 个技巧。一旦 IIS 6 成为黄金版，一些提示可能会变得过时，但是，至少现在，W2K 和 NT IIS 管理员应该应用其中的一些，晚上睡个好觉。

## 分享这篇文章