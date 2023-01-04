# Novell NetDrive:用于 Windows 的 Webdav 客户端

> 原文：<https://www.sitepoint.com/novell-netdrive-webdav-client-for-windows/>

趁我还没忘记，赶紧把这个消息从 OSCOM 上卸下来。NetDrive 是一个免费的客户端，由 Novell 提供，通过映射一个驱动器，允许从 Windows 访问 [Webdav](http://en.wikipedia.org/wiki/Webdav) 服务器。

相当多以 OSCOM 身份出现的 CMS 利用 Webdav 来轻松更新内容，包括 PHP 的 [eZ publish](http://ez.no) 和 [Typo](http://typo3.com/) (加上 [PEAR::HTTP_WebDavServer](http://pear.php.net/package/HTTP_WebDAV_Server) )。

我个人对 Webdav 没有太多的第一手经验，但与那些在 OSCOM 上得到关于 Windows 上不完整的客户端实现的报告的人交谈，这些实现除了微软自己的服务器技术之外，不能很好地与任何东西配合。显然，对于像 [Life CMS](http://www.oscom.org/events/oscom4/proposals/lifecms) 这样的项目来说，通过覆盖 Linux 文件系统调用来实现他们自己的虚拟文件系统已经足够糟糕了(如果你试图构建一个 CMS，这是一条很长的路要走！).

NetDrive 的提示要感谢 Ivan Ristic T1，PHP 程序员可能从 T2 的 PHP Traveller T3 那里知道他。稍后还有更多要说的，关于伊万在 [mod_security](http://www.modsecurity.org/) 上做的事情，这是一个伟大的项目。

不管怎样，我个人以前从未听说过 NetDrive，而且也不是唯一一个对此感到惊讶的人。我觉得 Novell 很难把他们的信息传播出去。 [Danese Cooper](http://blogs.sun.com/roller/page/DaneseCooper/) 就[企业博客](http://www.oscom.org/events/oscom4/proposals/corporateblogs)对 Sun 的影响发表了颇有见地的演讲——或许 Novell 可以从中学习到一些东西？Novell / Suse / Ximian 的事情似乎正在悄悄地发生，这是令人惊讶的，因为它有潜力…

## 分享这篇文章