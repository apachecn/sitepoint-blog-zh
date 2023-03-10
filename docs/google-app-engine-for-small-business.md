# 面向小型企业的谷歌应用引擎

> 原文：<https://www.sitepoint.com/google-app-engine-for-small-business/>

如今，每个企业，无论大小，都使用 IT 来实现业务流程的自动化。虽然大企业有资金和人力来开发和维护软件和硬件，但对小企业来说，这可能总是不值得投资回报。好消息是，在当今世界，尤其是最近几年，我们有了“随用随付”的选择，包括软件和硬件。虽然市场上有许多选择，但我们将着眼于“谷歌应用引擎”。

Google app Engine 让您可以轻松地将自己开发的应用程序部署到他们的云中。您还可以利用谷歌的其他应用程序，如 Gmail、谷歌文档，即插即用您定制开发的应用程序。

## 你得到了什么:

**基础设施和平台仅按使用成本**
GAE 为您的应用部署和支持基础设施提供平台。虽然有一个免费的配额，如果你的业务需求超过，你只需支付尖峰。你的网站流量的增长是动态的，你可以放心，速度会一直很快。

**即插即用**
一个典型的企业需要电子邮件系统、办公套件、日程安排系统等。认证等功能很简单，但是开发起来很麻烦&维护起来。您可以依靠 Google 的基础设施来开发这些模块，这可以减轻您企业的开发和维护负担，同时利用行业中最好的模块。Gmail、Google docs 和 Calendar 是为数不多的经过用户验证的产品，可以很好地与定制开发的应用程序相兼容。

**快速开发，快速运行**
谷歌应用引擎的最佳功能之一是一键部署。这使得你的开发人员可以专注于他们的任务:开发，从而提高开发人员的效率。对 Java 和 Python 等流行语言的支持让您可以接触到大量可用的开发人员。

## 你需要计划什么

**不同类型的数据库&备份:**
Google app engine 采用非 SQL 数据库，不像传统的关系数据库。这意味着您的领域模型可能需要不同的建模，开发人员需要不同的编码。没有一个 SQL DB 非常适合模式变化太大的情况，并且在大多数情况下工作得非常好，但是如果您从一个现有的关系数据库迁移，您必须考虑迁移的工作&重新建模。
您还需要有一个适当的策略来频繁备份您的数据库，因为这在 SQL 数据库中是一个新事物，在最坏的情况下，您可能需要计划将您的数据库移回关系数据库。

**请求限制:**
由于 NO SQL DB 的固有结构及其不断发展的技术，对您可以执行的查询类型有一些限制。谷歌还对数据/请求时间等进行了一些限制。您需要根据业务流程的需求来评估这些限制。例如，如果您计划开发或迁移照片打印订购应用程序到 GAE，您可能需要评估照片上传是否会由于 GAE 的限制而超时。您可能还需要根据您的流程寻找可能的替代方案。

**将整个堆栈移至云**
借助 GAE，您可以将整个应用堆栈移至云，包括应用数据&。虽然从性能角度来看，这是一个好主意，但一些组织可能有法规遵从性/IP 限制来这样做。此外，一些企业可能更喜欢将应用程序迁移到云中，而将数据留在内部。

**总之**
GAE 为小型企业提供了一个利用成熟基础设施的绝佳机会，如邮件、在线办公套件和业务流程可能需要的定制应用插件。但是，企业必须深入评估他们的业务流程，以检查它是否适合可用的技术。如果技术得到了很好的评估，并且工作经过了很好的思考和计划，那么所呈现的成本机会可以增加您的底线。

图片来自[克里斯蒂安·拉格瑞克](http://www.shutterstock.com/gallery-61002p1.html) / [摄影爱好者](http://shutterstock.com)

## 分享这篇文章