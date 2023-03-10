# SaaS 和云服务监管

> 原文：<https://www.sitepoint.com/saas-cloud-service-curation/>

软件即服务在客户关系管理(CRM)、人力资源管理(HRM)等业务中得到了发展。像 Salesforce、Google Apps、Zoho 这样的公司已经成功地在这些领域实施和交付了商业解决方案。但随着平台即服务(PaaS)提供商的出现，出现了一批提供软件即服务的利基参与者:但不是严格意义上的服务。GigaOM 将这种现象称为“[云服务监管](http://gigaom.com/2012/09/15/paas-vendors-should-lead-the-way-in-cloud-services-curation/)”，但我更愿意称之为“**软件组件即服务**”。我使用这个术语的原因来自于开发背景:例如，在开发软件时，日志或安全性本质上是应用程序的一个组件，因此也是这个术语的来源。不要对这个术语本身争论太多，让我们来看一看应用程序的几个组件或功能，它们可以作为服务提供，并且是这个领域的佼佼者。在接下来的几周里，我们可能会探索一些正在运行的提供商，但今天我们将坚持介绍。

## Memcached 即服务

Memcache 是一个动态内存缓存，它在动态内存中缓存经常使用的数据或对象，并减少到数据源(可能是数据存储或 API)的行程。MemCachier 提供了自己的 memcache 实现，但是您可以使用 memcached 客户端 API 来访问缓存。亚马逊网络服务(AWS)也有一个 memcache 服务，目前仍处于测试阶段:[亚马逊弹性缓存](http://aws.amazon.com/elasticache/)。

## 将日志记录作为服务

日志记录是应用程序和服务器的重要组成部分。在云环境中，访问集群的每个节点可能不是一个好主意，在大多数情况下也不可行。在这种情况下，将日志提取到中央服务器并拥有生态系统的统一视图是理想的。 [Loggly](http://www.loggly.com/) 从您的应用程序服务器等收集日志，并为您提供日志的统一视图。 [Papertrail](https://papertrailapp.com/) 也提供日志服务，并声称可以处理来自 GitHub 等网站的数十亿条日志。AppFog 使用 [logentries](http://blog.appfog.com/logging-as-a-service-with-logentries/) 作为部署在 AppFog 上的应用程序的日志服务。

## 应用性能管理即服务

监控和提高云中应用程序的性能是一个关键方面。在这一领域大放异彩的一些名字是[新遗迹](http://newrelic.com/)、 [AppDynamics](http://www.appdynamics.com/) 。

虽然我已经介绍了一些组件/服务，但是这个列表当然不是详尽的。电子邮件即服务有像 [SendGrid](http://sendgrid.com/) 、 [MailGun](http://www.mailgun.com/) 、[邮戳](https://postmarkapp.com/servers)、 [MailChimp](http://mailchimp.com/) 等提供者。有些提供商提供数据库即服务，包括 sql 和 NOSQL。CDN 即服务，我们最近在文章[中讨论过，这里](https://www.sitepoint.com/author/jaccob/)，或者后端即服务，这里[。这份清单还远未完成。每个提供商提供的产品都有某些独特的优势，您应该在做出选择之前了解这些优势。期待未来几周这些有趣的讨论，并随时发表评论。](https://www.sitepoint.com/backend-as-a-service-part-1/)

## 分享这篇文章