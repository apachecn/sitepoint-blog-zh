# 10 分钟内在云上创建一个 WooCommerce 商店

> 原文：<https://www.sitepoint.com/how-to-create-a-woocommerce-store-on-the-cloud-in-10-minutes/>

*本文是与 [Cloudways](https://www.cloudways.com/en/) 合作创作的。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

虽然通过电子商务的在线购物多年来一直稳步增长，但自疫情开始以来，却出现了前所未有的爆炸式增长。

即使你拥有一家实体店，拥有一个网上商店也从未如此重要。

电子商务商店不只是增加你的收入。它提供了另一种收入来源，限制了你对像我们现在这样的事件的暴露。当然，一个电子商务商店使企业能够达到一个比店面大得多的市场。

在本教程中，我们解释了如何在十分钟内用云上的 WooCommerce 创建你的电子商务商店。

WordPress 是世界上最流行的内容管理系统之一。它现在为互联网上 37.5%的网站[提供动力](https://w3techs.com/technologies/history_overview/content_management/all/y)，这个数字并没有因为那些被遗忘的只有一个读者的博客而膨胀。[Quantcast 排名前十的网站中有 38%也是 WordPress 支持的。](https://trends.builtwith.com/cms/WordPress)

如果你越过 Gatsby 的前端，你正在阅读这篇文章的网站也是由 WordPress 支持的。

WooCommerce 是一个流行的 WordPress 插件，可以将你的 WordPress 站点转换成电子商务商店。WordPress 和 WooCommerce 都是开源的，正在积极开发中。社区和对平台和插件的支持是巨大的。对于网站开发的初学者*和专家*来说，这是一个简单的选择。

但是说的够多了。让我们在云上启动服务器，创建一个电子商务商店。

## 在 Cloudways 上设置您的服务器

我们将使用 [Cloudways](https://www.cloudways.com/en/) 来管理我们的主机。Cloudways 允许你在云上创建一个预装 WordPress 的实例，而 WooCommerce 已经准备就绪。这样节省了很多时间。不需要配置网络服务器，创建数据库，安装 WordPress，然后[安装 WooCommerce](https://www.cloudways.com/en/woocommerce-hosting.php) 。Cloudways 有免费试用——之后，[计划的起价是 10 美元/月](https://www.cloudways.com/en/pricing.php)。

登录到 Cloudways 帐户后，单击按钮添加新的服务器。您可以为您的服务器选择五个平台中的一个——最便宜的选项是 DigitalOcean。选择服务器的大小和位置。

![Version Note](img/447be0a4fc070fbf40153ec9d8698747.png)

在应用程序和服务器详细信息中，选择用 WooCommerce 安装 WordPress 的选项。

![Application and Server Details](img/539d692587a1dbc609b8d33e277948a4.png)

单击“立即启动”创建服务器。

![Adding Server](img/0fb4dfba8852e0e6667edc800b0d3a39.png)

部署服务器和安装 WordPress 和 WooCommerce 需要几分钟时间。

一旦状态显示您的服务器已准备好运行，您就成功创建了您的电子商务商店！是时候将您的域指向您新创建的实例了。

## 将域链接到您的服务器

转到服务器的设置页面查看其一般信息。前往域名管理，我们将指向我们的电子商务网站的一个`store.`子域。本节还告诉您应该通过 DNS 注册商将您的域或子域指向何处:

![Domain Management](img/9625f5699a8d9908fbc200ece934cfba.png)

接下来，访问域名注册商，为您的子域创建一个`CNAME`条目，指向上一步中的 URL。

![CNAME Entry](img/aaa0bbed32690ee8f88807df5324fc29.png)

保存并访问`store.yoursite.com`查看您网站的当前版本。

![Initial Site Version](img/e2ddfc21bfaeb99477ec23537ce10a17.png)

请注意，URL 仍然是 HTTP。您需要让 HTTPS 确保您客户的安全。返回 Cloudways 上的服务器管理页面，选择 SSL 管理上传您的 SSL 证书。这里有一个指南[通过让我们加密](https://www.sitepoint.com/a-guide-to-setting-up-lets-encrypt-ssl-on-shared-hosting/.)来设置 SSL

![SSL Management](img/34c6a1de1536eec2fa7550d89d2d0d6c.png)

## 配置您的电子商务商店

设置好我们的商店软件和链接到它的子域后，让我们来配置站点。首先，您需要通过 Cloudways 设置三个凭证:

*   您的 WordPress 管理员登录信息，
*   数据库登录、
*   和通过 SSH 或 SFTP 访问服务器的凭证

您可以在 Cloudways 服务器页面上的 Access Details 部分中设置所有这些属性。

![Access Details Page](img/38f3feb2529e7fcacc6969cc43a1d052.png)

前往你的商店的 WordPress 登录页面，使用上一步的凭证进入管理区。要设置 WooCommerce 配置，请前往左侧菜单的 WooCommerce 部分。

![WooCommerce Configuration WordPress](img/d69b2470bc79f16c9aa356bb2b9a4683.png)

一旦你对这些设置感到满意，就去 URL `store.yoursite.com/shop/`预览你的 WooCommerce 网站。在这个阶段，我们仍然有商店的默认主题:

![WooCommerce Site Preview](img/09b966dcf86a6e78f3a9a77d439f627a.png)

## Cloudways:高级特性

我们已经看到了您可以多么轻松地配置您的网站。让我们探索一下 Cloudways 为我们的商店提供的一些高级选项。

您可以检查服务器上各种服务的状态。即使你没有手动安装这些服务，WordPress 和 WooCommerce 也需要它们来运行你的商店。可以在“服务器管理”页面的“管理服务”部分看到这些服务的状态。

如果您的商店面临任何技术问题，此页面是开始调试可能出现的问题的好地方。

![Manage Services Page](img/2d921115c83d5fc42fb7e613ce69cf72.png)

云托管服务的一个关键功能是定期备份和恢复服务。转到备份和恢复部分，获取最新备份的信息。点击“立即备份”按钮开始备份过程。在对你的商店做任何重要的改变之前，或者更新 WordPress 或者一个重要的插件之前，确保你有一个备份。

![Backup and Restore Page](img/ec85a24ffe37ce896e5ea65ae3355c2f.png)

在任何时间点，您都可以从下拉菜单中选择一个备份，然后点击“立即恢复应用程序”以恢复到您的商店的早期快照。

如果您通过 Git 管理您的代码，您可以生成一个 SSH 密钥，下载并链接到 GitHub 或 Bitbucket 等 Git 供应商。这将使您能够直接从 Git 供应商同步到您的 Cloudways 服务器。

![Deployment via Git Page](img/bd6bfad828838bd735e6c39f33e246ff.png)

其他高级功能包括 [Cloudways CDN](https://www.cloudways.com/en/cdn-services.php) 和迁移现有 WordPress 站点的能力。试用期内，还可以免费迁移自己创建的网站。

## 关于云上电子商务的最后思考

Cloudways 托管平台为企业提供了网站托管的灵活性和选择。借助全年全天候支持、简单且可扩展的仪表板、各种云提供商和透明计费等功能，您将有太多的选择。

作为一家企业，在网上开展业务对于吸引客户、增加订单和使收入来源多样化非常重要。虽然开设电子商务商店有很多选择，但通过 [Cloudways](https://www.cloudways.com/en/) 在云上一键创建 WooCommerce 实例是最简单的入门方式之一。

## 分享这篇文章