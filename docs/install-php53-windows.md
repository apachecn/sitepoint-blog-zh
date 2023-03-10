# 如何在 Windows 上安装 PHP 5.3

> 原文：<https://www.sitepoint.com/install-php53-windows/>

随着围绕着火狐 3.5 的所有兴奋，你可能没有注意到 PHP 5.3 也已经发布了。这是自 5.0 版本以来最重要的更新，最初计划在 6.0 版本中推出的几个功能已经被加入到版本中。你很快就会在 SitePoint 上听到这些，但是，首先，我们需要在我们的 Windows 开发系统上安装 PHP 5.3。

## 一句警告…

PHP 5.3 是新版本，你可能会遇到一些问题。如果你已经有了一个稳定的 php 环境，确保你保留了当前 PHP 文件夹和设置的备份。

## Web 服务器安装

如果您没有安装 Apache，请参考 [**如何在 Windows** 上安装 Apache Web Server。](https://www.sitepoint.com/how-to-install-apache-on-windows/)也可以选择安装 MySQL 数据库——参见 [**如何安装 MySQL**](https://www.sitepoint.com/how-to-install-mysql/) 。

## PHP 5.3 安装

Windows PHP 发行版现在可以从位于 http://windows.php.net/download/的一个专门的微型网站上获得。这个选择有点令人困惑，说明也很模糊，但是因为我们将使用 [Apache Lounge build](http://www.apachelounge.com/) 安装 PHP 作为 Apache 2.2 模块，所以你应该下载 **VC9 x86 线程安全** Zip 文件。

*一般说明:*
*如果您使用的是标准的[Apache.org](http://www.apache.org/)web 服务器，请下载 **VC6** 版本。 **VC9** 构建应该用于 [Apache Lounge](http://www.apachelounge.com/) 二进制文件或 IIS。*

如果你把 PHP 作为 Apache 模块安装，应该使用线程安全的 T2 版本。如果你把 PHP 作为 CGI 二进制文件安装，应该使用非线程安全的 T4 版本。

从第二步开始，你现在可以按照 [**的说明在**](https://www.sitepoint.com/how-to-install-php-on-windows/) 的 Windows 上安装 PHP 了。注意，在步骤 3 中，*PHP . ini-推荐的*现在被命名为*PHP . ini-开发*；您仍然需要制作一个副本，并将其重命名为 php.ini。

如果您之前已经定义了一个有效的 PHP 配置文件，那么像 [WinMerge](http://winmerge.org/) 这样的工具可以帮助您比较现有的值并将其复制到新的 php.ini 文件中。

## PHP 5.3 错误报告

第一次加载之前没有错误的 PHP 应用程序时，可能会遇到大量冗长的错误消息。不要慌！PHP 5.3 引入了许多新的错误指令，这些指令在开发 php.ini 配置中默认启用:

*   E_STRICT 建议进行改进，以确保代码的最佳互操作性和向前兼容性，以及
*   E_DEPRECATED 警告你关于在 PHP 未来版本中不工作的代码。

非常有用。根据我的经验，大多数消息都报告没有定义时区，并且不赞成使用 new by reference 来分配返回值。

不幸的是，应用程序可能会显示太多的小错误，以至于无法使用。您可以通过在 php.ini 的第 514 行将 error_reporting 值设置为“E_ALL & ~E_DEPRECATED”并重新启动 Apache 来禁用新的错误指令。

然而，包括 WordPress 在内的一些 web 应用程序定义了它们自己的错误报告设置，并可能继续显示消息。开发者需要一段时间来升级他们的应用程序，使其完全兼容 PHP 5.3。

PHP 5.3 安装成功了吗？您在现有代码中遇到过任何重大问题吗？

## 分享这篇文章