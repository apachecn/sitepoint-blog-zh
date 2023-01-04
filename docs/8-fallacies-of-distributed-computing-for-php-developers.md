# PHP 开发人员分布式计算的 8 个谬误

> 原文：<https://www.sitepoint.com/8-fallacies-of-distributed-computing-for-php-developers/>

1997 年，Peter Deutsch 提出了开发分布式计算应用程序的七个谬误。后来，第八个由詹姆斯·高斯林(Java 之父)创造。

这些谬误与我们 PHP 开发人员直接相关，因为我们每天都在构建分布式应用程序。我们构建混搭，即与 SOAP 和 REST 服务交互的应用程序，通过脸书、谷歌或 Twitter APIs 认证用户，从远程数据库和缓存服务中检索信息，等等。毫无疑问，我们正在构建分布式计算机应用程序。

鉴于我们正在构建分布式应用程序，理解这八个谬误以及它们如何影响我们是很重要的。

## 1.网络是可靠的

公平地说，这显然是不真实的。尽管自 1995 年以来，网络延迟时间逐年减少，带宽显著增加，但说网络可靠是错误的。

假设我们已经设置了一个不使用太多服务的简单应用程序——一个使用 MySQL 作为后端的基本 PHP 应用程序。可以说不会有太多问题。然而，假设我们后来决定选择 MySQL 的托管提供商，比如 Xeround 来满足我们的数据库需求。尽管有很好的可伸缩性和高可用性，但是如果他们的终端出了问题怎么办？如果他们的基础架构遭受 DDoS 攻击或因内部问题而停机，该怎么办？

我们经常听到 99.999%的正常运行时间，但即使这样也从来不是 100%。随着今天服务的激增和可用带宽的高可用性，人们很容易忘记没有什么是完美的。

您如何解释应用程序中服务的失败？

## 2.延迟为零

虽然延迟可能很低，甚至比几年前还低，但它永远不会为零。引用 Arnon Rotem-Gal-Oz 在他的[分布式计算的谬误解释](http://www.rgoarchitects.com/Files/fallacies.pdf "Fallacies of Distributed Computing Explained")帖子中的话:

> 以大约每秒 30 万公里(每两周 3.6 * 10E12 太埃)的速度，从欧洲向美国发送一个 ping 信号至少需要 30 毫秒，即使处理是实时进行的。

这是一件坏事吗？嗯，是也不是。取决于我们如何构建我们的应用程序和我们可用的资源，我们可以在很大程度上减轻延迟的问题。

我们可以将应用程序部署在单个数据中心，而不是托管在亚马逊网络服务等服务中，并利用 T2 S3，这样我们就可以将数据分布在世界各地，让数据更接近我们的最终用户，并减少应用程序在网络上的延迟。但是即使我们可以减少延迟，我们也不能消除它。我们可以采用一系列的方法和架构来减少它对我们的影响，但无论我们做什么，它都会存在。

您在设计应用程序时考虑过这一点吗？

## 3.带宽是无限的

带宽真的可以无限大吗？如果有，在什么价位是无限的？当我们考虑到网络正变得越来越移动化时，一切旧的都是新的。

我并不是说我们要从拨号上网的速度开始，新的 4G 网络明显比早期的 2G 和 3G 网络要快。但是，即使它们的峰值数据速率目前也低于标准宽带连接的数据速率。

此外，随着移动宽带的日益普及，寻求使用我们服务的潜在用户数量正在以惊人的速度增长(我们都希望变得受欢迎，并至少获得一些脸书的成功)。考虑这些来自[移动思维](http://mobithinking.com/mobile-marketing-tools/latest-mobile-stats "Global mobile statistics 2012: all quality mobile marketing research, mobile Web stats, subscribers, ad revenue, usage, trends... | mobiThinking")的统计数据:

*   有 59 亿移动用户。
*   3G 覆盖了 12 亿移动网络用户。
*   移动设备占全球点击量的 8.49%。

有鉴于此，公平地说，尽管带宽速率和全球 it 渗透率都在增加，但用户的增长率起到了平衡的作用。更进一步，随着移动宽带所提供的巨大灵活性，一种明确的特定服务消费自然会到来。

您准备好应对服务的巨大潜在负载了吗？你能应付这种可用性带来的高峰吗？

## 4.网络是安全的

我认为不需要太多的细节就可以公平地说，这是错误的，而且将永远是错误的。如果你有任何疑问，也许你应该和 LinkedIn 或者 eHarmony 的会员谈谈。

当我们设计和部署我们的应用程序时，无论是在应用程序托管的地方，如 [Rackspace](http://www.rackspace.co.uk/ "Rackspace Managed Hosting, Web Hosting | Rackspace UK") ，PagodaBox 或 [cloudControl](https://www.cloudcontrol.com/ "cloudControl - Cloud App Platform - supercharging development") ，还是在应用程序本身的设计中，我们对安全性的重视程度如何？

[根据 SecurityAffairs](http://securityaffairs.co/wordpress/4468/cyber-crime/ddos-detailed-analysis-of-the-phenomenon.html "DDoS, detailed analysis of the phenomenon | Security Affairs") ，Prolexic 报告:

*   针对金融服务行业的恶意数据包流量环比增长 3，000%。
*   2011 年第 4 季度，针对金融服务行业的 19.1TB 数据和 140 亿个恶意流量包在 2012 年有所增加。
*   2012 年发现并缓解了 65TB 数据和 1.1 万亿个数据包，是 2011 年的 80 倍。

鉴于网络不安全，我们需要确定我们正在理所当然地使用良好的安全实践。考虑到来自诸如克里斯·希福莱特的博客、T2 的基本 PHP 安全、T4 的 PHP 安全联盟以及其他来源的大量好建议，很难不知道如何以及为什么将安全性嵌入到我们的应用程序的核心。

你的安全措施是什么？您是否评估了与您一起部署的供应商？

## 5.拓扑不会改变

不是吗？真的吗？它没有改变，还是我们只是不知道？当我们和其他人一起托管我们的应用程序时，我们只是不知道。如果提供商重新配置、升级、调整他们的数据中心，无论出于何种原因，拓扑都会发生变化。

考虑到之前提到的智能手机使用率的增加，拓扑结构经常发生变化。从用户和提供商的角度来看，拓扑几乎每天都在变化！

如果拓扑发生了变化，它所依赖的外部服务无法再访问，导致无法访问数据库，那么这肯定是一个问题。但是，如果在我们的提供商内部，事情发生了变化，而应用程序继续运行，那么这可能不是问题。

诚然，编写一个小型的、托管在简单配置中的应用程序是很容易的。但是应用程序会发生变化，越来越受欢迎的应用程序更是如此。您在设计中考虑过拓扑结构的变化吗？您如何解释或处理应用程序设计和部署设计中的失败？

## 6.有一个管理员

“但我的应用程序由一家服务提供商托管。他们提供操作系统、数据库和网络服务器支持”，你说。好吧，假设这是您的应用程序，真的有一个管理员吗？如果真的只有一个管理员，您真的会信任应用程序的提供者吗？我不愿去想如果他们生病或去度假会有什么问题。

通常情况下，至少会有几个管理员，尽管每个人在技术上和更广泛的范围内可能没有相同水平的培训和敏锐度。应该有适当的策略，如网络入侵检测和其他安全策略，但不能保证它们都以相同的彻底性和勤奋程度遵循这些策略。

鉴于现在有太多的托管服务提供商，而且更新 DNS 记录所需的时间很短，我们有很多选择和灵活性，如果一家提供商不能满足我们的需求和期望，我们可以从他们那里转移到另一家。

你考虑过这对你有什么影响吗？如果你没有能力轻易更换供应商，该怎么办？如果您有大量的供应商锁定，或者迁移成本很高，该怎么办？如果您的应用程序的架构不够灵活怎么办？你能做些什么来减轻这些风险？

## 7.运输成本为零

与迄今为止的所有说法一样，这种说法的正确性也是极不可能的。如果支持我们的应用程序的服务器在同一个数据中心的同一个机架中，那么运输成本可以大大降低，但这是在时间成本方面。货币成本呢？

是的，我们可以根据需求弹性地无限扩展和缩减，我们可以跨地理位置不同的数据中心存储我们应用程序的数据，以便数据在物理上尽可能靠近我们的最终用户，但代价是什么呢？

您的应用程序或服务的架构组成是什么？就成本或时间而言，它是否接近零？如果你能减少一个，会增加另一个吗？

## 8.网络是同构的

与其他谬误不同，我认为这是我们作为 PHP 开发人员固有的理解。我们在 Windows、Linux、Solaris、BSD 和 Mac OS X 服务器上托管我们的应用。我们使用 MySQL、SQLServer、SQLite、PostgreSQL、mongoDB、Hadoop 和 Oracle 来存储数据。我们通过需要不同接口的 XML 或 JSON 来消费外部服务。作为一个多操作系统和多服务社区，可以说从早期开始，我们就从来没有期望过一个同构的网络。

但是问题还是要问，你的方法灵活吗？你能处理多个数据库和数据源吗？您是否使用相关的设计模式，比如[抽象工厂](http://en.wikipedia.org/wiki/Abstract_factory_pattern "Abstract factory pattern - Wikipedia, the free encyclopedia")，通过透明的代码接口来消费来自各种来源和类型的数据？或者，如果您需要做从 XML 到 JSON 这样简单的事情，您的代码会崩溃吗？

## 最后

我认为作为 PHP 开发人员，分布式计算的八个谬误和以前一样相关。考虑到可用的信息和资源过多，我们处于一个很好的位置来理解它们，并减轻因相信它们而产生的风险。

你怎么想呢?在开发您的应用程序和服务时，您会考虑它们吗？你认为八大谬误对你的申请有什么影响？

### 进一步阅读

*   今天分布式计算的谬误有多相关？
*   [新遗迹——分布式计算的谬误重生:云时代](http://blog.newrelic.com/2011/01/06/the-fallacies-of-distributed-computing-reborn-the-cloud-era/ "The Fallacies of Distributed Computing Reborn: The Cloud Era | New Relic blog")
*   [网络与谬误](http://www.tbray.org/ongoing/When/200x/2009/05/25/HTTP-and-the-Fallacies-of-Distributed-Computing "ongoing by Tim Bray - The Web vs the Fallacies")
*   [carbon five–iPhone 分布式计算谬论#1:网络是可靠的](http://blog.carbonfive.com/2010/11/17/fallacy-1-the-network-is-reliabl/ "iPhone Distributed Computing Fallacy #1: the network is reliable: The Carbon Emitter")
*   [分布式系统:原则和范例](http://www.cs.vu.nl/~ast/books/ds1/ "Distributed Systems: Principles and Paradigms")
*   [分布式系统设计介绍](http://code.google.com/edu/parallel/dsd-tutorial.html "Introduction to Distributed System Design - Google Code University - Google Code")
*   [网飞——我们从使用自动气象站中学到的 5 个教训](http://techblog.netflix.com/2010/12/5-lessons-weve-learned-using-aws.html "The Netflix Tech Blog: 5 Lessons We've Learned Using AWS")

<small>图片 via[Perrush](http://www.shutterstock.com/gallery-73007p1.html "Stock Photos | Shutterstock: Royalty-Free Subscription Stock Photography & Vector Art")/[Shutterstock](http://www.shutterstock.com "Stock Photos and Royalty-Free Images by Subscription")</small>

## 分享这篇文章