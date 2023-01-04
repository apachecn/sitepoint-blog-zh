# 使用 Vultr & OpenVPN 在 10 分钟内创建您自己的 VPN 服务器

> 原文：<https://www.sitepoint.com/create-your-own-vpn-server/>

你的企业需要设置 VPN 吗？您是否在寻找一种更安全的方式来连接到互联网？如果是这样，那么这个指南就是给你的！我们向您展示如何使用 Vultr 创建您自己的云 VPN 服务器。我们将向您展示如何创建一个新的虚拟机，安装和配置 OpenVPN，最后测试您的设置。我们开始吧！

*本文由* [*Vultr*](https://www.vultr.com/?utm_source=sitepoint&utm_medium=syndication&utm_campaign=q122) *合作创作。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

## **什么是 Vultr？**

Vultr 是一家受欢迎的云托管提供商，在全球多个地方提供虚拟机。它提供高性能虚拟机，有多个位置可供选择。

如果你想制作自己的 VPN 服务器，Vultr 是一个很好的选择。它以合理的价格提供高性能，并且交付可靠。Vultr 也很好用。它有一个用户友好的控制面板，提供直接访问一些伟大的 VPN 设置工具。

![](img/ec2c72ec911ea8104fef6e7fe8c054a8.png)

Vultr 提供了[多种虚拟机](https://www.vultr.com/pricing/?utm_source=sitepoint&utm_medium=syndication&utm_campaign=q122)供选择，因此您可以找到符合您需求的虚拟机。这些计划的起价为每月 2.50 美元。

## 为什么要自己跑 VPN？

有许多原因可以解释为什么您想要建立自己的 VPN，而不是使用商业 VPN 提供商。

### 设置 VPN 以取代付费的 VPN 服务

最大的原因是安全和隐私。当您使用商业 VPN 服务时，您的 IP 地址在大部分网络中都是隐藏的。然而，VPN 提供商仍然可以看到你的流量，因为它通过他们的服务器。这意味着他们可以看到你访问的所有网站和你传输的数据。

如果他们愿意，他们可以记录这些数据并出售给第三方。很难知道哪些宣传无日志策略的服务遵循了这一策略。

在我们对最好的 VPN 提供商的回顾中，我们发现法庭案例是一个有用的指南:知道宣传无日志策略的服务是否能够提供日志作为证据是一个开始。但是并不是所有的供应商都经过了这样的测试，经过测试的公司可以随时改变他们的内部流程。

你被迫信任第三方，这在安全和隐私方面根本站不住脚。

运行你自己的私人 VPN 服务器意味着你在控制。您可以选择 VPN 服务器的位置，并且可以根据需要添加或移除服务器。你不会受虚拟专用网提供商的摆布。

### 为什么不运行家庭 VPN 服务器？

你可能想知道为什么不能在你的家用电脑上运行 VPN 服务器。毕竟，这可能比使用 Vultr 更方便。

答案是不太安全。你的家庭 IP 地址很容易被找到，如果有人想针对你，他们可以相对容易地做到这一点。通过使用 Vultr，你把你的 VPN 服务器放在了云中，这使得攻击变得更加困难。

因此，如果你在寻求安全和隐私，最好使用像 Vultr 这样的云托管提供商来保持控制。如果你只是为了方便，那么在你的家用电脑上运行 VPN 服务器可能就可以了。

既然我们已经看到了你可能想要在云服务器上运行自己的 VPN 的一些原因，那么让我们来看看如何在 Vultr 上设置一个。

## 1.创建 Vultr 帐户

第一步是创建一个 Vultr 帐户。你可以通过[访问 Vultr 网站](http://vultr.com/promo/try100?promo=100VULTROFFER&utm_source=sitepoint&utm_medium=syndication&utm_campaign=q122)(使用此链接可获得 100 美元的免费积分！).输入您的电子邮件地址和您选择的密码，然后点击“创建帐户”按钮。

Vultr 将向您发送一封电子邮件来验证您的帐户。您可以立即登录，但是在该步骤完成之前，您将无法部署服务器。

## 2.在 Vultr 上创建新的虚拟机

接下来，在 Vultr 上创建一个新的虚拟机。为此，[登录你的 Vultr 账户](https://my.vultr.com/?utm_source=sitepoint&utm_medium=syndication&utm_campaign=q122)，点击*部署服务器*按钮。这是您真正开始创建自己的 VPN 服务器的地方。

在此页面上，您将选择您的虚拟机服务器类型、位置、大小和软件，并且能够看到您在各种选项之间进行选择时的价格影响。

![](img/9e98cd2842a5f3c5c3bb51cd83a87320.png)

您需要在这里进行一些选择:

*   **选择服务器:**在这里选择云计算—这些共享的 vCPU 服务器适合我们的需求，价格也合适。
*   **CPU &存储技术:**我们在这里选择了常规性能，因为我们不需要先进的硬件来处理生产工作负载。
*   **服务器位置:** Vultr 提供世界各地的位置。就我们的目的而言，最好的选择是最接近您的选择。
*   **服务器图像:**切换到市场应用标签。这里有几个 VPN 服务器选项，但是对于本指南，我们将选择 OpenVPN 访问服务器。
*   **服务器大小:**选择 5 美元/月选项(0.007 美元/小时)，除非您知道您需要一台能够处理更多流量的服务器，例如整个团队的流量。如果这对您来说不够，您可以随时升级。

至于其他选项，如自动备份，请根据您的喜好随意选择。

最后，给你的虚拟机一个名字。这可以是你想要的任何东西。点击*立即部署*按钮。

您的虚拟机将被创建，您将能够在 Vultr 控制面板中看到它。

现在您的虚拟机已经创建好了，我们可以继续设置 OpenVPN 了。

## 3.使用 OpenVPN 创建您自己的 VPN 服务器

既然我们已经建立并运行了一个虚拟机，是时候设置 OpenVPN 了，这是一个免费的开源 VPN 实现。

这个过程相当简单。

首先，登录你的 Vultr 账户，确保你在*产品*页面的*实例*选项卡上。找到您刚刚创建的虚拟机，单击其名称以查看其管理控制面板。

![](img/9565fee84b52aecfe780445114ac346c.png)

在此页面上，向下滚动到*应用程序说明*部分，找到服务器上 OpenVPN 管理目录的直接链接，以及设置期间创建的用户名和密码。

### **验证 VPN 服务正在运行**

在新标签中打开管理面板。您将看到一个浏览器安全警告页面，如下所示:

![](img/86ff0e74e120f79a3a6d8cd6c44f7957.png)

这只是我们自签名证书的结果，所以只需点击*继续*忽略警告*。*

然后，您将被带到 OpenVPN 访问服务器验证页面。从服务器管理控制面板中放入凭据并登录。

在这里，您可以找到配置 VPN、升级 OpenVPN 服务器访问(如果您需要添加 VPN 连接席位)等选项。

使用左侧边栏进入*状态概述*页面，获得相关详细信息的摘要，并打开和关闭 VPN 服务。

![](img/89191f6daae9a7e0733aca084611b20d.png)

如果一切顺利，它应该在您第一次登录时打开。否则，在这里单击“启动 VPN 服务”。

### **在您的设备上安装 OpenVPN 客户端**

下一步是在您的电脑上安装并打开 OpenVPN 客户端。

编辑地址栏，删除 */admin/* 和其后的任何内容，然后点击 enter。在这里，我们只是离开管理面板直接访问服务器 IP，在那里我们可以轻松地访问客户端应用程序下载。

您可能需要使用与管理面板相同的凭据再次登录。然后您会看到这个页面:

![](img/80e5a54fb271d3339700f4cd1ca910b8.png)

单击适用于您的平台的按钮，并在您的计算机上运行安装程序。

*注意:如果您已经使用 OpenVPN 或想要使用不同的客户端，您可以通过与客户端下载相同的页面(在平台图标下方)下载连接配置文件。*

当你打开应用程序时，你应该看到这个安装程序已经为你预装了正确的 OpenVPN 配置文件。

您应该只需要输入一个密码——这就是我们之前用来访问管理面板的密码。

然后，连接到 VPN 并确保一切正常。

恭喜您，您已经在几分钟内部署并连接到您自己的 VPN 服务。

## 4.配置您的 OpenVPN 服务器

现在你已经运行了你的虚拟机，安装了 OpenVPN，并且测试了你的第一次成功连接，是时候配置你的 VPN 来满足你的需求和偏好了。

有几个设置您可能想立即调整，如更改管理用户密码，以及大量的高级选项来做进一步的事情。

由于这些变化会因人而异，这里就不赘述了。以下是进一步改进的一些好的起点:

*   [安装后立即提高安全性的建议](https://openvpn.net/vpn-server-resources/recommendations-to-improve-security-after-installation/)
*   增强 OpenVPN 安全性的更多方法
*   [设置分割隧道](https://openvpn.net/for/split-tunneling-with-access-server/)，仅通过 VPN 路由特定流量或应用
*   [根据硬件地址限制连接](https://openvpn.net/vpn-server-resources/access-server-post-auth-script-host-checking/#restricting-automatic-registration-of-mac-addresses-for-new-accounts)
*   [实施多因素认证](https://openvpn.net/for/endpoint-authorization/)(包括现成的 Google 认证器集成)
*   通过[安装有效的 SSL 证书](https://openvpn.net/vpn-server-resources/installing-a-valid-ssl-web-certificate-in-access-server/)跳过浏览器警告

## Vultr 上的 OpenVPN 替代方案

除了 OpenVPN 之外，Vultr 应用市场中还有其他几种选择。

*   [UTunnel](https://www.vultr.com/marketplace/apps/utunnel-vpn?utm_source=sitepoint&utm_medium=syndication&utm_campaign=q122) 是一款 VPN 服务器，支持多种 VPN 协议，提供易于使用的管理控制台来配置设置和访问，以及强大的附加功能选择，如 2FA 和内置防火墙。
*   WarpSpeed 是一个基于网络的 VPN 服务器，使用开源的 WireGuard 协议。
*   [Pritunl](https://www.vultr.com/apps/pritunl?utm_source=sitepoint&utm_medium=syndication&utm_campaign=q122) 是一个支持 OpenVPN、IPsec 和 WireGuard 协议的分布式 VPN 服务器。您可以连接无限的设备和无限的用户，无需支付许可费。

所有这些应用程序都可以在 Vultr 上一键部署。

## 你知道怎么做虚拟专用网吗

如果你按照上面的步骤，你现在应该有你自己的 VPN 启动和运行。祝贺您创建了自己的 VPN 服务器！

现在，您可以使用它更安全地连接到互联网，或者访问您的虚拟机上原本无法访问的资源。

我们希望本指南对您有所帮助。如果您有任何问题或反馈，请随时联系我们的支持页面。感谢阅读！

## 分享这篇文章