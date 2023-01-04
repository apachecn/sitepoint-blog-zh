# 谷歌应用引擎和对 Phalcon 的请求

> 原文：<https://www.sitepoint.com/google-app-engine-and-a-plea-for-phalcon/>

让我们看看 Google 最近在 Google App Engine 中拥抱 PHP，这对 PHP 社区意味着什么，以及如果他们添加对高性能 PHP 框架 Phalcon 的支持，它将如何进一步改进。

## Google App Engine 是什么？

[谷歌应用引擎](https://developers.google.com/appengine/ "Google App Engine - Google Developers") (GAE)是谷歌的云计算平台即服务产品，用户可以在该平台上部署位于谷歌数据中心的网络应用。这意味着我们在环境中部署的任何应用程序都会自动继承 Gmail、Google+等应用程序的稳定性、互操作性和可扩展性。

虽然对 GAE 的全面讨论超出了本文的范围，但可以说 GAE 既不是私有服务器也不是虚拟私有服务器——它是一个平台。因此，它有一些局限性。你不能简单地在里面安装扩展，也不能随意写磁盘。您不能自行改变资源限制，也不能安装自定义系统工具。它不是普通的 Linux 机器；对于企业级应用程序来说，这是一个完全托管的庞大环境。

在过去的几年里，GAE 的受欢迎程度一直在稳步上升，特别是在 Python 2 和 JVM 语言(Java、Scala、Clojure)应用程序的部署方面，但是直到最近在 Google I/O 上[才宣布由于](https://www.youtube.com/watch?v=Qt1_atU_Qsg "Google I/O 2013 - Introducing Google App Engine for PHP - Youtube")[社区的兴趣](https://code.google.com/p/googleappengine/issues/detail?id=13 "Issue 13 - googleappengine - PHP language support - Google App Engine - Google Project Hosting")增加了 PHP 支持。

GAE 部署在一定程度上是自由的。如果你的应用超过免费使用配额，你可以请求付费升级。这是一种按使用付费的模式，最适合大规模高性能应用。因此，优化您的应用程序并减少请求数量是绝对必要的。它不仅有助于提高性能，还能帮您省钱。

## 过渡到谷歌应用引擎

GAE 的文件系统是只读的。向磁盘写入内容的唯一方法(如文件上传、缓存、自定义日志等。)是要么使用读写 CDN 之类的外部服务，要么使用 gae-filestore 之类的虚拟文件系统(更多信息请参见上面链接的谷歌 I/O 视频和[这里的](https://developers.google.com/appengine/docs/python/datastore/#Python_Datastore_API "Storing Data - Python Datastore API - Google Developers"))或[谷歌云存储](https://developers.google.com/appengine/docs/php/googlestorage/overview "Google Cloud Storage PHP API Overview - Google App Engine - Google Developers")。此外，不存在 MySQL/MariaDB 或 PostgreSQL 的本地安装。相反，你可以选择使用[谷歌云 SQL](https://developers.google.com/cloud-sql/ "Google Cloud SQL - Google Developers") (它类似于 MySQL，但有[限制](https://developers.google.com/cloud-sql/docs/introduction#features "About Google Cloud SQL - Google Cloud SQL - Google Developers")和差异，我们不会在这里讨论)。

不用说，过渡到 GAE 环境的 PHP 开发人员有自己的工作要做。令人欣慰的是，Google PHP 团队[运营着一个博客](http://gaeforphp-blog.appspot.com/ "Tips and Tricks from the Google App Engine PHP team")(实际上是 GAE 上的一个 WordPress 安装),提供了有用的提示和教程，而针对仍在试验阶段的 PHP 实现的[文档](https://developers.google.com/appengine/docs/php/ "PHP Runtime Environment - Google App Engine - Google Developers")也做得很好。对于那些对简单概述感兴趣的人来说，还有非常清晰的[入门指南](https://developers.google.com/appengine/docs/php/gettingstarted/introduction "Introduction - Google App Engine - Google Developers")。

我鼓励你查看所有的链接资源，尽可能多地熟悉这个新环境。GAE 对 PHP 的支持是 PHP 社区的一个巨大转变，从长远来看，肯定有影响 PHP 开发的潜力。

## 基本 PHP 扩展和 Phalcon

人们已经注意到不能安装扩展的巨大缺点，并要求增加两个必要的扩展: [mbstring](https://code.google.com/p/googleappengine/issues/detail?id=9342 "Issue 9342 - googleappengine - PHP mbstring extension - Google App Engine - Google Project Hosting") 和 [iconv](https://code.google.com/p/googleappengine/issues/detail?id=9340 "Issue 9340 - googleappengine - ICONV PHP extension support - Google App Engine - Google Project Hosting") (它们已经被安装了)，使得 GAE 的 PHP 运行时更加可用。鉴于 PHP 支持仍处于试验阶段，还没有准备好投入生产，GAE 团队正在接受并积极考虑新安装和升级的建议。

这就是费尔康发挥作用的地方。Phalcon 是一个非常高级的 PHP 框架，用 C 编写，作为 PHP 扩展安装。它提供了您所熟悉的所有框架特性，甚至更多，但是具有更高的性能(更快的速度，更少的资源)和健壮性。它用 C 语言编写，在本地执行，并在服务器启动时加载到内存中，本质上用一些急需的缺失特性增强了 PHP 本身。

在某些方面，Phalcon 比 PHP6 本身更 PHP6，并且使您能够用以前构建应用程序的一小部分时间来编写快速和安全的应用程序。如果你还没有尝试过 Phalcon，我鼓励你这样做——他们的[文档](http://docs.phalconphp.com/en/latest/index.html "Welcome! - Phalcon 1.1.0 documentation")非常完整和全面，包含每个 API 调用的示例。

问题是前面提到的无法在 GAE 的 PHP 中安装自定义扩展，这意味着这种环境永远无法使用 Phalcon。由于 Phalcon 的[出色的](http://systemsarchitect.net/performance-benchmark-of-popular-php-frameworks/#post-642 "Performance Benchmarks of Popular PHP Frameworks | Systems Architect") [性能](http://docs.phalconphp.com/en/latest/reference/benchmark.html "Framework Benchmarks | Phalcon 1.1.0 documentation") f，如果它没有实现将是一个遗憾，因为它无疑将减少谷歌的服务器负载和应用程序所有者将面临的成本。这就是为什么我[在 GAE 的问题跟踪者里开了一个新的功能请求](https://code.google.com/p/googleappengine/issues/detail?id=9336 "Issue 9336 - googleappengine - Install the PhalconFramework PHP extension - Google App Engine - Google Project Hosting")，你可以投票支持。到目前为止，它是 30 个最受欢迎的功能之一。

不过不要相信我的话——试试 Phalcon，看看你有多喜欢它。很有可能你稍后会为这个问题投票。但是，请根据主演来投票，而不是评论。注释纯粹是为了在安装过程中帮助解决信息和潜在问题。当在谷歌代码上发表评论时，一封电子邮件会发送给所有关注该问题的人，这可能会导致一些人取消关注，如果他们觉得自己被垃圾邮件最终伤害了努力。

## 摘要

GAE 对 PHP 来说是一个全新的机会。它有可能彻底改变 PHP 开发世界和开发人员可用的托管选项。要想从一开始就成为这个新时代的一部分，请关注 SitePoint 上的 PHP-GAE 教程和 Phalcon 指南，并通过本文中的链接，特别是[gaeforphp-blog.appspot.com](http://gaeforphp-blog.appspot.com/ "Tips and Tricks from the Google App Engine PHP team")，关注事态的发展。别忘了去看看 Phalcon，为这个问题投票，以进一步推动这项事业，并随时通过 [Google+](http://gplus.to/Swader "Bruno Škvorc - Google+") 或在下面的评论中向我提问。

## 分享这篇文章