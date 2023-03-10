# 微软将发布 IIS Express

> 原文：<https://www.sitepoint.com/microsoft-to-release-iis-express/>

如果有一件事微软做得很好，那就是提供软件选项。它有时会有点乱——例如，Vista 的 57 个关闭选择——但它通常对我们有利。

如果你使用微软的 IIS 网络服务器，你目前有两个选择:

**1。完整的 IIS web 服务器**
IIS 随 Windows XP、Vista、7 一起提供。它是一个完整的 web 服务器，与生产 Windows 服务器环境中部署的系统几乎完全相同。在某些情况下，您将不能运行多个域名，或者它将被限制在几个并发用户，但是这些问题不应该影响您的日常开发需求。

不幸的是，每个版本的 Windows 都提供了不同版本的 IIS。在公司环境中运行它的人也需要一个管理员帐户，据微软称，一些公司不允许在开发人员的个人电脑上安装 web 服务器。

**2。ASP.NET 开发服务器**
Visual Studio 为测试 ASP.NET 应用程序提供了一个内置的开发服务器。它与 IDE 一起安装，是轻量级的，立即启动，自动使用安全的本地 TCP/IP 端口，并在非管理员帐户中工作。

不幸的是，它不是一个完整的开发服务器。您不能更改配置、更改安全设置或安装 URL 重写等模块。

## 第三个人

如果你对这两种服务器都感到沮丧，你会很高兴听到微软发布了第三种选择:IIS Express。实际上，这是一个折中的解决方案，提供了其他版本的最佳之处，而没有这些问题:

*   这是一个 10MB 的小下载，是轻量级的，易于安装
*   它支持 ASP.NET 和经典 ASP(微软还没有确认 PHP，但我怀疑它会工作)
*   它不需要管理员权限
*   它实际上是 IIS 7，但它可以在 Windows XP 中工作
*   没有并发连接限制
*   它支持大多数 IIS 选项和模块，如 SSL 和 URL 重写
*   它可以与其他两台服务器安装在同一台 PC 上，不会产生冲突
*   它可以从命令行启动和配置
*   它与 Visual Studio 集成，你不需要改变现有的代码
*   可以将其配置为启用远程请求
*   您可以将它与您的应用程序一起分发。

第一个测试版本将很快推出。

我喜欢这个想法，尽管它无疑会引起一些混乱。有趣的是，对于想要 IIS7 而不升级操作系统的 XP 开发人员来说，这是一个可行的解决方案。对于不想安装完整 web 服务器的 PHP 开发人员来说，这甚至是一个不错的选择。当测试版发布时，我们将更详细地了解 IIS Express。

你会使用 IIS Express 吗？会有助于还是阻碍发展？

## 分享这篇文章