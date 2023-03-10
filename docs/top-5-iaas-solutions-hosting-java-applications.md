# 托管 Java 应用的五大 IaaS 解决方案

> 原文：<https://www.sitepoint.com/top-5-iaas-solutions-hosting-java-applications/>

### 目录 

*   [亚马逊 EC2](#amazonec2)
*   [谷歌计算引擎](#googlecomputeengine)
*   [IBM 的 SoftLayer](#ibmssoftlayer)
*   [微软 Azure](#microsoftazure)
*   [数字海洋](#digitalocean)
*   [总结](#summary)
*   [评论](#comments)

如今，云计算无处不在。简而言之，云计算是通过互联网存储和访问不同的数据和程序，其基本构建模块是基础设施即服务(IaaS)。IaaS 为云环境中应用程序的自然开发、测试和使用提供支持。在本文中，我将向您简要介绍 IaaS，然后比较五个提供商:*亚马逊 EC2* 、*谷歌计算引擎*、 *IBM 的 SoftLayer* 、*微软 Azure* 和*数字海洋*。

IaaS 帮助我们避免购买和运营我们自己的服务器的复杂性。此外，不需要维护和升级软件。有了这些好处，很明显，它在许多情况下都具有较低的基础架构成本，从而帮助没有时间和资金投资于自己的硬件的新组织。

IaaS 的其他一些优势包括:

*   服务以大量资源的形式分布
*   IaaS 非常灵活，可以提供动态扩展
*   成本因使用情况而异
*   多个用户可以使用一个硬件
*   位置独立性:只要有互联网连接并且云的安全协议允许，通常可以从任何地方访问服务

既然您已经意识到 IaaS 对于构建您的 web 应用程序的好处，那么您所要做的就是决定选择哪个解决方案。唯一的问题是今天有太多的选择。作为一名 Java 开发人员，我最初在评估各种 IaaS 提供商时有些纠结。因此，在本文中，我将描述托管 Java web 应用程序的五种最佳解决方案，并对它们进行相互比较。

![Infrastructure as a service](img/8e6dcd8ad671490f7e46f3ea35f01dd7.png)

## 亚马逊 EC2

*亚马逊弹性计算云*(亚马逊 EC2)是一种在云中提供可调整计算能力的 web 服务。它有助于启动和管理服务器实例，从而使其适合开发人员进行 web 规模的计算。根据您的要求，您可以将服务器实例用于任何合法目的。

实例有不同的大小和配置。EC2 允许 AWS 提供不同的实例类型，您可以使用它们来满足特定的需求。因此，您可以根据需要启动不同的虚拟机。如果你正在部署一个 Java 应用，你可以安装你的应用服务器(Tomcat，Web Sphere 等。)集成到一个虚拟机中，并使用其他服务，如微服务、数据库等。在具有适当配置的独立虚拟机上。更多细节，特别是 AWS 如何与 Java 一起工作，可以在这里找到。

有了 Amazon EC2，人们可以选择不同的实例类型、操作系统和软件包，这使得它非常灵活。Amazon EC2 的内存、CPU 和实例存储配置有助于选择存储。此外，您可以选择最适合您所选择的操作系统和应用程序的引导分区大小。

使用 EC2 的唯一缺点就是**费用**。随着我的工作流变得越来越复杂，我使用了更多的服务，很难预测成本。然而，为了帮助估计成本，亚马逊提供了一个每月计算器。

与其他服务提供商相比，网络不太灵活。由于大量数据必须从局域网的一端移动到另一端，数据传输的距离比需要的距离要长得多，这导致了因特网上常见的拥塞问题。

Amazon EC2 是当今的行业标准。许多领先的网站和应用程序都使用 EC2。其中比较突出的有 [Instagram](https://www.instagram.com) 、 [Reddit](https://www.reddit.com) 、[网飞](https://www.netflix.com)、 [Adobe Systems](http://www.adobe.com) 、 [Smugmug](https://www.smugmug.com/) 等等。有很多关于亚马逊合作的文章，比如为什么[网飞选择亚马逊](http://techblog.netflix.com/2010/12/four-reasons-we-choose-amazons-cloud-as.html)。

## 谷歌计算引擎

*谷歌计算引擎* (GCE)是一个 IaaS 产品，它可以帮助你在谷歌用于其自身服务的相同基础设施上运行虚拟机。GCE 为您提供了更大的灵活性和控制力，以及对计算资源的访问，从根本上改变了您处理问题的方式。GCE 很像 EC2，但是你的虚拟机运行在 Google 的基础设施上。[在谷歌计算平台上部署 Java](https://cloud.google.com/java/) 很容易。

网络定价很贵。由于 GCE 相对较新，与 AWS 不同，编程语言的选择有限。AWS 中支持的一些 Java APIs 在 Google 计算引擎中不可用。从 GCE 过渡到任何其他平台都不是那么容易的。您还应该注意到，要运行任何 Java 框架，GCE 都需要用户付出更多的努力，因为它没有针对他们的集成包。

GCE 最常见的客户是 [EverNote](https://evernote.com/) 、 [Philips](http://www.philips.com/) 、 [CocoCola](http://www.coca-colacompany.com/) 、 [HTC](http://www.htc.com/) 、[摩托罗拉](https://cloud.google.com/blog/big-data/2016/07/supersize-it-how-motorola-transformed-its-data-warehousing-and-analytics-with-google-cloud-platform)，它们成功地用 GCE 转变了其数据仓库和分析方式、 [KhanAcademy](https://www.khanacademy.org/) 、 [Spotify](https://www.spotify.com/) 、 [PocketGems](http://pocketgems.com/) 和 [BestBuy](http://www.bestbuy.com/) 。

## IBM 的 SoftLayer

正如他们在 IBM 所说，“不折不扣的云”，这个技术巨头提供高容量、可伸缩性和安全性。IBM/SoftLayer 的高速内部网连接可能正是您的复杂应用程序从 IaaS 解决方案中所需要的，但是您必须支付额外的费用才能使它工作。

Softlayer 的裸机基础架构可以与虚拟化服务器相结合，形成采用一系列 hyper-V 技术的解决方案，因此您可以优化生产工作负载。它在 SoftLayer 的数据中心之间提供了巨大的高性能带宽，使您的云环境能够承受从一个数据中心到另一个数据中心的突然激增的数据。同时，SoftLayer 的单租户服务器模型通过全面的防火墙保护您的数据，为敏感信息提供全面保护。除了 IBM/SoftLayer，没有其他云提供商提供定制的 VM 类型。SoftLayer 为调用其 API 提供了 [maven 依赖关系](https://github.com/softlayer/softlayer-java)。

缺点是 SoftLayer 的服务没有很好地捆绑在一起。IBM 提供广泛的服务，但不仅仅是云服务。相反，他们提供公共云服务与内部和 IBM 品牌服务的组合，从而提供一个“虚拟 IT 部门”，即联系 IBM 获得托管服务以进行软件的任何更新和维护。

顶级用户有 [Artomatix](https://artomatix.com/) 、 [Cedato](http://cedato.com/) 和 [ClickTale](https://www.clicktale.com/) 。

## 微软 Azure

一套全面的服务，可帮助您在全球范围内快速开发、部署和管理微软管理的数据中心中的应用程序。它支持 Windows、Linux(是的，没错，微软现在喜欢 Linux)和社区 VM 镜像，以及跨平台开发工具和框架的选择。您将为您使用的东西付费，这意味着您可以节省大量成本并实现更高的效率。

Azure 的服务器让你完全掌控云端。 [Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-overview) 是一项面向多平台 web 开发者的服务。它非常注重安全性，遵循标准的安全模式*检测、评估、诊断、稳定和关闭*。Azure 还提供简单、用户友好的服务来增强保护，如多因素身份验证和应用程序密码要求。如果你需要使用定制第三方软件的解决方案或众多应用程序在单台机器上运行，那么 Azure 的 IaaS 解决方案将帮助你。作为一名 Java 开发人员，你会发现在这里部署和扩展你的 web 应用[很容易。](https://azure.microsoft.com/develop/java/)

实例之间的负载平衡可能会中断应用程序的状态；也就是说，不能保证来自同一个用户的两个请求会到达同一个实例。Azure 不提供选择实例类型的灵活性。此外，它需要专业的管理和维护，包括打补丁和服务器监控。

微软 Azure 托管的顶级网站有 [Essar](http://www.essar.com/) 、[使用 Azure 节省 65%的部署](http://www.networksasia.net/article/essar-saves-65-app-deployment-cost-azure.1336348659)、 [CloudMunch](http://www.cloudmunch.com/) 、 [AppPoint](http://www.apppoint.com/) 和[shephetz](http://www.shephertz.com/)。

## 数字海洋

数字海洋针对的是 Linux 开发者。它提供了一个简单、不复杂的设置，并为用户提供了一个干净、易于使用的界面，具有很少的功能和一键式部署。它有一个控制面板来管理双因素身份验证、良好的 API、自动备份和 DNS 管理等功能。它提供 SSD 托管，与其他服务相比非常便宜。

所提供的服务是基本服务，如无负载平衡的虚拟机和 DNS 管理、托管数据库、分析、Hadoop 集群等。如果您关心的不仅仅是自动伸缩和高可用性，并且在管理服务器方面投入更多，那么 Digital Ocean 适合您的使用。对于 Java 开发者来说，他们有一个社区可以在他们的官方网站上提供[教程](https://www.digitalocean.com/community/tutorials?q=java)。

Digital Ocean 的局限性在于，其磁盘位于虚拟机管理程序中，并且不使用集中式存储，因此如果托管在故障虚拟机管理程序上，数据完全丢失的风险相对较高。然而，如果你能承受这种风险，并且你有备份，那么你会以一个很好的价格得到一些性能很好的服务器。

数字海洋上托管的顶级网站有[nodeb](https://nodebb.org/)、 [Rockerbox](http://rockerbox.com/) 、 [Ghost](https://ghost.org/) 和 [taskRabbit](https://www.taskrabbit.com/) 。数字海洋的成功故事之一是 jQuery 如何使用它。

## 摘要

让我用表格总结一下上面的内容:

| **属性** | **AWS EC2** | **GCE** | **IBM Softlayer** | **微软 Azure** | **数字海洋** |
| --- | --- | --- | --- | --- | --- |
| **主要特征** | 支持 windows 和 linux 服务器。很多服务选项，API 都是可用的。 | 出色的表现。缩放是好的。 | 管理、软件和安全特性的结合对企业云管理员来说是有益的。 | 管理工具易于使用。 | 设置很简单。对创业公司来说更便宜。 |
| **定价** | [看到了吗](https://aws.amazon.com/ec2/pricing/) | [看到了吗](https://cloud.google.com/compute/pricing) | [看到了吗](http://www.softlayer.com/info/pricing) | [看到了吗](https://azure.microsoft.com/pricing/) | [看到了吗](https://www.digitalocean.com/pricing/) |
| **数据中心** | seven | six | Nineteen | Fourteen | Eleven |
| **CoreOS 支持** | 不 | 是 | 不 | 是 | 是 |
| **Docker 支持** | 是 | 是 | 不 | 是 | 是 |
| **Hyper-V 虚拟机管理程序** | 不 | 是 | 不 | 是 | 是 |
| **基于用户的 VPN 访问** | 不 | 不 | 不 | 是 | 是 |
| **限制** | 价格昂贵。网络不够灵活，导致拥塞问题。 | 管理功能没有那么有用。有限的编程语言选择。 | 服务没有很好地捆绑在一起。 | 成本高。表现一般。 | 没有集中存储。 |

有了这些知识，您将能够决定哪个 IaaS 平台最适合您的应用程序。我们将在下一篇文章中讨论 IaaS 的替代方案。

## 分享这篇文章