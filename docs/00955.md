# 在阿里云上安装 WordPress 的 5 个步骤

> 原文：<https://www.sitepoint.com/5-steps-to-installing-wordpress-with-lemp-on-alibaba-cloud/>

*本文是与[阿里云](http://click.aliyun.com/m/49659/)合作创作的。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

WordPress 是市场上最受欢迎的内容管理系统(CMS)之一，因为它可以完全免费使用，而且用途极其广泛。通常，你会在 LAMP 栈上部署 WordPress，而不管网站的类型，因为 WordPress 是基于 MySQL 和 PHP 的。

然而在这篇文章中，我们将发现如何在 LEMP 堆栈上部署一个 WordPress 网站。我们选择 NGINX 作为我们的 web 服务器，而不是更流行的 Apache，因为它对单个站点来说更快、更具可伸缩性和安全性。

通过完成本文中的步骤，你应该会看到一个全新的 WordPress 站点运行在阿里云[弹性计算服务](http://click.aliyun.com/m/49658/) (ECS)实例上，运行高性能的 LEMP 堆栈。

本文假设你已经在[阿里云](http://click.aliyun.com/m/49659/)上有了账号。如果你还没有，只要注册并享受 [$300 新用户免费信用](http://click.aliyun.com/m/49660/)。

## 步骤 1:供应和保护 Ubuntu 16.04 服务器

第一步，我们将创建一个运行 Ubuntu 16.04 的[阿里云 ECS](http://click.aliyun.com/m/49658/) 实例。这一步非常简单，主要包括从阿里云控制台购买一个 ECS 实例，选择您喜欢的配置，并运行一些 Linux 命令。

然而，为了提高安全性，我们也将通过创建一个超级用户和禁用根登录来保护服务器。然后我们将在服务器上配置 Ubuntu UFW(简单防火墙)。

[在这里阅读详细步骤](http://click.aliyun.com/m/49661/)。

这提供了一个通用的基础，我们将在此基础上完成我们的 LEMP 堆栈，这将是我们的下一步。

## 步骤 2:完成 LEMP 堆栈

在这一步，我们将为我们的 WordPress 网站完成一个现代 LEMP 堆栈。具体来说，我们将使用 NGINX、MariaDB 和 PHP7 的高性能组合来建立我们的 LEMP 堆栈。这种设置被广泛认为是现代 WordPress 网站的最佳基础。

[在这里阅读详细步骤](https://click.aliyun.com/m/49662/)。

目前，我们只能通过在 web 浏览器中输入服务器 IP 地址来访问我们的站点，因此我们将在下一步中通过配置域名来为我们的站点提供服务，并使用 SSL 证书来保护一切，从而解决这个问题。之后，我们将继续在我们的 LEMP 堆栈上安装 WordPress。

## 步骤 3:配置一个域，让我们加密 SSL

第三步，我们将对我们的站点进行域管理，添加 DNS 记录，并安装一个加密 SSL 证书。在前两步中，我们提供并保护了我们的 Ubuntu 16.04 服务器安装，然后用 NGINX、MariaDB 和 PHP7 完成了 LEMP 堆栈。但是，我们需要配置一个域，使我们的服务器可以公开访问。此外，我们将使用 SSL 证书来保护我们的 WordPress 站点，因此它只能通过 HTTPS 访问。

[在这里阅读详细步骤](http://click.aliyun.com/m/49663/)。

您的 ECS 实例现在应该已经安装了一个安全且高性能的 LEMP 堆栈，配置了一个域，以及一个保护访问者信息的安全 SSL 证书。

## 步骤 4:在你的阿里云 ECS 实例上安装 WordPress

第四步，我们将通过使用`wget/curl`和 WordPress 命令行(WP-CLI)来安装 WordPress。这是你需要做出决定的地方，因为本教程将向你展示两种不同的安装 WordPress 的方法。

使用`curl`安装并使用`nano`编辑配置文件是常见的方式。这是一个可靠的方法，值得一试，以防你将来需要依靠这个方法。替代`curl`的方法是使用 WP-CLI，这是 WordPress 的一个很棒的命令行界面。这是一个不可思议的省时工具，一旦你习惯了，你就可以用它来管理你所有的 WordPress 站点，并且有一个高效的工作流程。

[在这里阅读详细步骤](http://click.aliyun.com/m/49664/)。

## 步骤 5:对 WordPress 交易邮件使用 DirectMail

在前面的步骤中，我们保护了一个 Alibaba Ubuntu 16.04 ECS 实例，安装了一个 LEMP 堆栈，配置了 DNS 和域名服务器，并安装了 WordPress。那么我们有一个功能齐全的 WordPress 网站吗？嗯，没那么快。

对于任何提供商来说，云托管的一个问题是，虽然你有一个快速的服务器，但你没有托管提供商自动配置和提供的电子邮件功能。目前，我们的 WordPress 网站还不能发送事务性邮件，因此功能不全。

[阿里云的 DirectMail](http://click.aliyun.com/m/49666/) 服务是这一功能的完美选择。在这一步，我们将把 DirectMail 设置为 WordPress 应用程序事务性电子邮件的 SMTP 提供者。

[在这里阅读详细步骤](http://click.aliyun.com/m/49665/)。

## 结论

按照上面的步骤，我们已经使用一个[阿里云 ECS](http://click.aliyun.com/m/49658/) 实例在高性能的 LEMP 堆栈上成功安装了 WordPress。我们的 WordPress 应用程序也配备了电子邮件功能；我们可以订阅新用户，发送电子邮件通知，并通过电子邮件更改密码。

为了确保[阿里云 DirectMail](http://click.aliyun.com/m/49666/) 被正确设置为我们 WordPress 网站的 SMTP 提供商，你可以访问你的收件箱并检查以确保你收到了邮件。

就是这样！我们现在已经有了 WordPress，并且在阿里云栈上运行，功能齐全。更多教程，请访问阿里云官方[入门页面](http://click.aliyun.com/m/49667/)。

## 分享这篇文章