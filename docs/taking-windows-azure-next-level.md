# 让 Windows Azure 更上一层楼

> 原文：<https://www.sitepoint.com/taking-windows-azure-next-level/>

在我之前的教程中，我向你展示了在 Windows Azure 上建立一个简单的 HTML 网站或 Magento 网络商店是多么容易。现在是时候更进一步了，因为这些例子仅仅触及了 Windows Azure 特性集的一小部分。我现在将向您展示如何设置:

*   **流量管理**:将您的流量分配到多个位置，有助于负载平衡、地理分布和故障转移。
*   **监控**:使用不同类型的显示器来监控性能、正常运行时间和可用性。

此外，我还将介绍一下 Windows Azure 提供的其他一些有趣的功能。

## 交通管理

为了描述 Windows Azure 流量管理器的能力，我可以最好地利用他们自己对产品的介绍:

> “使用 Traffic Manager 作为您整体网络解决方案的一部分——Traffic Manager 将智能策略引擎应用于您域名的 DNS 查询，以便您可以出于性能、业务连续性、价格、合规性、法律或税务目的将流量发送到最佳数据中心。”

简而言之，这是一种非常先进的方式，可以根据您认为重要或需要使用的指标来分发您的内容。我将重点关注性能和连续性方面。让我们来看看这是如何实现的。

![aznextfig1](img/f73227da9a035ac1b39bc1d95ed7f316.png)

Traffic Manager 的设置非常少，只有一个 DNS 前缀(我将再次使用我的 Magento 演示)和一个负载平衡方法。您的选择是:

*   **性能**:基于离访问者最近的服务器的分布。
*   **Round Robin** :负载平衡，你设置多个相同的服务器，流量平均分配到这些服务器上。
*   **故障转移**:这是用于备份的，你可以保留一份完全相同的副本，以防你的主服务器出现故障。

对于本教程，我将采用“循环法”，但设置方法与其他两个选项基本相同。我想看看用 Windows Azure 设置负载平衡有多简单。

![aznextfig2](img/5ae7c7242892247a0b095a9038a360b0.png)

首先添加“端点”，这是您创建的虚拟机。对于本教程，我添加了两个以上的 Magento 演示服务器，基于我的[前一个教程](https://www.sitepoint.com/use-windows-azure/)的图像。

![aznextfig3](img/eaf19a37a94da29d9842ea89ef762259.png)

添加后，您可以看到终端的状态。如果灯是绿色的，您只需要配置如何处理它们。

![aznextfig4a](img/a0934913ff22a09a0da16b2eccec5296.png)

实际上，如果简单的循环赛是你所需要的，你不需要改变什么。它已经在使用这些设置了。

![aznextfig5](img/e955d2d4fa3616619fd7e3fc7b2fae3c.png)

而且，如果你不满意或者想尝试别的，你可以很容易地转换。配置保持不变，除非您选择了故障转移，在这种情况下，您可以优先考虑您的服务器。这当然是一个非常简单的方法。

附带说明:我还没能在仪表盘中找到如何根据特定类型的访问者来分配流量，尽管微软声称你可以根据税收目的来发送流量。这种配置给底层引擎本身留下了很多空间。但是，如果您想真正深入了解 REST API，可以参考大量的 REST API。

总而言之，我对设置这个流量管理器的易用性印象非常深刻。

## 监视

您启动的每个虚拟机都带有一组基本的监控选项。您可以使用一些重要的正常运行时间和响应指标来扩展这一功能，并设置警报规则。与大多数 Windows Azure 功能集一样，这非常容易。我将向你展示:

*   如何设置响应时间和正常运行时间监视器
*   如何设置警报规则，以便在出现问题时通知您

### 设置监视器

![aznextfig6](img/119b5a556cf9382d1e67c10c3ed06f82.png)

对于您的每台虚拟机，您会发现一个名为“monitor”的页面。一些基本的统计数据(磁盘读/写、网络输出/输入和 CPU%)已经设置好了。您可以使用“正常运行时间”和“响应时间”来延长这些时间，这将 pings 您的端点。这基本上是外部监控，其他参数在内部测量。

![aznextfig7](img/6d5b36c4b6e533af46b57d471e931f15.png)

您首先需要选择最多三个位置来放置监视器，这可以通过访问虚拟机的“配置”部分来完成。

![aznextfig8](img/262f600e4c3e7954fbcd5f07a0bcf3d6.png)

然后，您可以通过单击底部栏中的“Add metrics ”(添加指标),选择“Endpoints ”(端点),然后选择所需的监视器来设置这些指标。

![aznextfig9](img/d923126d53e7bccc127456c0ae92833b.png)

在监视器屏幕中，您将看到显示的正常运行时间百分比，例如，当您选择正常运行时间监视器时，有一个名为“Metric Details”的按钮(也在底部栏中)。单击此按钮将会出现您上面看到的屏幕，其中包含有关测量的更多详细信息。

### 设置警报规则

![aznextfig10](img/c34979aebedfe3b00aafb39eb5416907.png)

如果您希望在虚拟机性能出现任何干扰时得到通知，可以设置警报规则。这可以通过点击底部栏中的“添加规则”来完成。首先向您显示一个弹出窗口，您可以在其中命名和描述您的规则。

![aznextfig11](img/ccaa74b486bb4274c2f7864ab8c74ef7.png)

下一个屏幕更重要，因为这是您定义接收电子邮件的条件的地方。在这种情况下，我希望在过去 15 分钟内，当我的服务器在过去 10 分钟内达到 99.5%的正常运行时间时得到通知。

当然，所有这些都是非常基本的，并且依赖于管理员根据警报规则电子邮件执行的手动操作。这不是很高级，但是对于这一点，您会发现 REST API 非常有用。你可以[在这里](http://msdn.microsoft.com/en-us/library/windowsazure/ee460799.aspx)找到文档。

![aznextfig12](img/157e834db61c7caefe47d870ff4d2510.png)

由于 Windows Azure 是关于图形的，你的停机时间也以图形方式显示，以及所有必要的细节。

## 然后还有这个…

Windows Azure 功能集非常广泛，您在这里看到的只是来自管理门户内部。API 背后隐藏着一个完整的世界，微软对它做了很好的记录，所以你可以好好利用它。但是当查看管理门户时，也有一些好的宝石。

### 移动服务

Windows Azure 为 iOS、Android，当然还有 Windows Phone 的移动应用提供了完整的后端。它允许建立数据库，支持处理和广泛的监测。

### 后见之明

用于 Apache Hadoop 数据处理，您可以选择多达 32 个节点进行数据处理和分析。

### Windows Azure 缓存

仍然处于预览模式，但这允许基于内存的缓存，从而显著提高性能。你可以将它用于你的网站和应用程序，最大 150 GB，这当然是巨大的。

### 媒体服务

从编码到流媒体内容，这是一个成熟的媒体服务器。您可以将它用于点播和直播，并将其与 CDN 结合使用，以改善用户加载体验。

### Visual Studio Online

Windows Azure 通过提供代码存储、项目管理和测试以及轻松部署到云中，为 Visual Studio Online 提供了深度集成。

### 活动目录

作为另一个成功引入云的微软产品，Windows Azure 中的 Active Directory (AD)扩展了当前的本地 AD 安装。通过这种方式，您可以允许广告用户根据他们的广告凭据轻松地在线访问云服务或外部程序。

## Windows Azure 免费试用

你可能已经猜到了，我对 Windows Azure 提供的功能感到惊讶。我起初认为这是一个“我也是”的产品，但在浏览了他们所有的功能后，我发现它远不止如此。这一切都以你对微软的期望方式运行，并且它比亚马逊 AWS 以更直观的方式提供。

我最喜欢的是你不局限于微软技术。你可以用 Magento 构建一个完整的灯堆栈，而不会受到平台的限制。它是由微软创立和运营的，但也仅此而已。当然，它也提供微软自己的产品和技术，但这是互补的。

如果你想亲自试用，Windows Azure 提供 30 天免费试用。你可以用克里斯蒂安·利兹的文章[在 Windows Azure 上安装 WordPress](https://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/)，或者[我之前的文章](https://www.sitepoint.com/use-windows-azure/)来学习如何安装 Magento 或者一个简单的 HTML 网站。

有了这一切，你将处于一个有利的位置，不仅可以参加而且可以赢得由 SitePoint 和 Windows Azure 举办的[推动网络前进](https://www.sitepoint.com/community/t/push-the-web-forward-and-win/36018)竞赛。浏览[在线画廊的参赛者](https://www.sitepoint.com/community/t/push-the-web-forward-and-win/36018)，看看你在对抗什么。参赛作品将于 12 月 20 日截止。

## 分享这篇文章