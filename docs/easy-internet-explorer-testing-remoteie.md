# 使用 RemoteIE 轻松测试 Internet Explorer

> 原文：<https://www.sitepoint.com/easy-internet-explorer-testing-remoteie/>

Web 开发人员有责任确保他们闪亮的新网站或应用程序可以在各种浏览器上运行。最起码，这意味着 Chrome/Opera、Firefox 和 Internet Explorer。理想情况下，Safari 和一些移动 iOS 和 Android 浏览器将完成这项工作。

测试并不容易。这很费力，而且我们的努力也因为我们选择的操作系统上浏览器的可用性而受挫。Windows/Linux 用户不能安装 Safari，Mac/Linux 用户不能安装 Internet Explorer。

像 [BrowserStack](http://www.browserstack.com/) 这样的服务可以有所帮助，但只适合基本测试，因为很难使用开发工具。最可行的选择是使用另一台 PC 或在虚拟机环境中安装操作系统/浏览器，如 [VirtualBox](https://www.virtualbox.org/) 、 [VMware](http://www.vmware.com/) 或 [Hyper-V](http://www.microsoft.com/en-us/server-cloud/solutions/virtualization.aspx) 。

值得称赞的是，微软在 modern.ie 上提供了一系列虚拟机映像，但下载的文件可能有几千兆字节大小，因为它们包含了整个操作系统堆栈。它们也会在几个月后过期。这不利于快速和肮脏的测试，而且，可以理解的是，许多开发人员都懒得这么做。

幸运的是，微软创造了一个更快、更灵活的选择。 [**RemoteIE**](https://remote.modern.ie/) 允许您在 Windows、Mac、iOS 或 Android 上从云中运行最新版本的 Internet Explorer，而无需安装或管理虚拟机。这项免费服务目前还在测试阶段，但它很有效，技术上令人惊叹！

## 入门指南

首先，请访问 [remote.modern.ie](https://remote.modern.ie/) 并使用微软帐户登录。如果您使用 Windows 8+、OneDrive 或 Outlook.com，您可能已经使用了一个。然后你需要等待你的远程访问被批准——几个小时后我收到了一封电子邮件。

然后，您可以下载适用于 Windows (32 位和 64 位)、Windows RT、Mac OS、iOS iPhone、iOS iPad 和 Android 的 Azure RemoteApp。这通常约为 15MB 它因设备而异，但只需安装一次。

然后，您可以启动 RemoteApp 并使用您用来注册的 Microsoft 帐户登录。您将看到一个“IE 技术预览”图标:

![RemoteIE Launch](img/4e8e77817b77263f58cd19a107cb8f6c.png)

双击它以启动 Internet Explorer 的 Windows 10 预览版:

![Running RemoteIE](img/3a14ec9b427142c498bb70d654210fe0.png)

这是一个完整版本的浏览器，可以访问 F12 开发工具。你不会被允许访问设置或安装附件，但它很快，是快速网站测试的理想选择。

## 访问本地主机

RemoteIE 是一个在线应用程序，因此它只能访问公开的网站。如果您想从本地 PC 或网络进行测试，您可以使用一个免费的工具，如 [ngrok](https://www.sitepoint.com/use-ngrok-test-local-site/) 来创建一个到您的应用程序的 HTTP 隧道。例如，如果您有一个运行在端口 80 上的本地 web 服务器，请输入:

```
ngrok 80
```

ngrok 将返回一个地址，如 http://abcd9876.ngrok.com/，您可以将它输入 RemoteIE(或传递给任何非现场人员)进行进一步测试。

## 遥控器的未来

对于许多人来说，RemoteIE 将彻底改变 Internet Explorer 测试。然而，请记住，它目前是一项测试服务，使用是有限的——我尝试了几次才获得访问权限，因为其他人正在使用它。

其他注意事项:

*   你只有一个小时的时间来测试你的网站，如果你超过一分钟不活动，就会被启动。
*   没有 Linux 支持。我想这在技术上是可能的，因为它可以在 Android 和 Mac OS 上运行，但在撰写本文时还不可用。
*   你只能测试最新的 IE。这是最有用的版本，但它通常表现良好，你会遇到比 IE8 少得多的问题。微软正在考虑其他版本，但鉴于 IE8 的使用率已降至 5%以下，这将变得越来越没有必要。

令人印象深刻。现在，如果我们能鼓励苹果以类似的方式提供 Safari

## 分享这篇文章