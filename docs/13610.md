# OSCON 2006: Cosmo 和 Scooby:基于标准的开源日历

> 原文：<https://www.sitepoint.com/oscon-2006-cosmo-and-scooby-standards-based-open-source-calendaring/>

本周，凯文·杨克从俄勒冈州波特兰市的 [OSCON 2006](http://conferences.oreillynet.com/os2006/) 发回报道。

由[开源应用基金会](http://www.osafoundation.org/)(OSAF)[开发的【Cosmo】是一项服务，它使用户能够将他们的个人信息放到网上，并通过像 WebDAV 这样的开放协议与他人分享。](http://cosmo.osafoundation.org/) [Scooby](http://scooby.osafoundation.org/) 是一个基于网络的前端，用于访问你在 Cosmo 上存储的信息，目前它提供了一个灵活的日历视图。在这次演讲中，三位 OSAF 开发商概述了这些项目、它们的现状以及它们未来的发展方向。

Cosmo/Scooby 首席工程师 [Brian Moseley](http://bcm.maz.org/) 介绍了 Cosmo 及其架构。特别是，他专注于对 [WebDAV](http://www.webdav.org/) (它本身是对基本 HTTP 的扩展)的一些扩展，这些扩展是为提供特定于内容类型的特性而开发的: [CalDAV](http://www.caldav.org/) 用于日历数据， [CardDAV](http://wiki.osafoundation.org/bin/view/Projects/CosmoCardDav) 用于联系信息。在互操作性的名义下，Cosmo 还搜索了 [Atom Syndication Format](http://www.atomenabled.org/developers/syndication/) 和 [Atom Publishing Protocol](http://bitworking.org/projects/atom/) ，甚至是由 [GData](http://code.google.com/apis/gdata/) 和 [Google Calendar](http://code.google.com/apis/gdata/calendar.html) 引入的 API，更不用说[Java Content Repository](http://jcp.org/en/jsr/detail?id=170)(JCR)标准 API 了，尽管这些 API 被开发成了崇高的理想，但尚未兑现承诺，这迫使 Cosmo 研究更实用和可扩展的替代方案。

SitePoint 作者 [Matthew Eernisse](http://fleegix.org) ，他也是 Scooby 的主要开发者，然后接手演示基于网络的日历界面。开发这个大量使用 AJAX 的 web 应用程序的关键是保持界面简洁，Eernisse 演示了为任何用户操作提供即时反馈的原则，然后等待服务器确认操作成功(并在出错时回滚操作)。

Cosmo 开发人员鲍比·鲁洛接着解释了 Cosmo 的公共 API，这使得任何前端(包括第三方网络应用程序)都可以使用 JavaScript 友好的 JSON 数据格式将数据输入和输出 Cosmo 存储库。他指出，其他 RPC 格式(如 XML-RPC)可以根据需要轻松地添加到系统中。

鲁洛还展示了一个单独的项目， [CalDAV4j](http://wiki.osafoundation.org/bin/view/Projects/CalDAV4jHome) ，这是一个用于处理 CalDAV 协议的 Java 库(我上面提到的日历特定的 WebDAV 扩展)。这个库曾在 Cosmo 内部用于处理与 Scooby 基于 web 的日历前端的通信，但不再需要用于该目的。然而，这个库将有助于在多个 Cosmo 服务器之间同步日历信息，OSAF 正在寻找这个项目的贡献者。

Moseley 最后呼吁有兴趣的开发者加入这些项目。Cosmo 及其各种客户端和 API 在很大程度上仍然是一项正在进行的工作；Moseley 声称大部分内容都处于 alpha 测试之前，目标是在 2007 年 Q2 发布 1.0 版本。但是这些项目对于相关的开发者来说有着潜在的令人兴奋的未来。

## 分享这篇文章