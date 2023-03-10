# 中央桌面将数据库 API 添加到协作套件中

> 原文：<https://www.sitepoint.com/central-desktop-database-api/>

总部位于加州帕萨迪纳的[中央桌面](http://www.centraldesktop.com/)，提供 SaaS 协作和项目管理套件，今天宣布其“夏季版本”，其中最显著的包括一个新的数据库 API。这一版本非常重要，因为它将允许这家成立三年的公司的 175，000 名用户向第三方应用程序推送数据，并从外部应用程序吸回数据。该 API 还将使第三方能够创建在中央桌面工作区内运行的迷你应用程序。

Central Desktop 成立于 2005 年，每年的销售额约为 250 万美元，并希望在 5 年内将年收入增加到 1 亿美元。为了实现这一增长，该公司于去年 3 月获得了第一轮融资——来自 OpenView Venture Partners 的 700 万美元。Central Desktop 在一个拥挤的市场中运营，有 [37signals](http://www.37signals.com/) 、 [activeCollab](http://www.activecollab.com/) 、 [Huddle](http://www.huddle.net/) 和[微软](http://www.microsoft.com/)，但今年赢得了一个主要的公共客户，当时美国总统候选人巴拉克·奥巴马[的竞选团队使用他们基于维基的软件](http://www.readwriteweb.com/archives/barack_obama_campaign_central_desktop.php)来组织加利福尼亚州和得克萨斯州的志愿者。

首席执行官艾萨克·加西亚告诉我，新的 API 增加了人们非常需要的功能。与许多 web 应用程序开发人员信奉的“少即是多”的原则相反，Garcia 告诉我，除了公司已经提供的两个挂钩(例如，中央桌面挂钩到 EditGrid)，他的客户一直在要求更多的挂钩到外部软件和服务。因此，新的数据库 API 将允许开发人员从中央桌面导入和导出数据。

实际上，该软件内置数据库创建工具已经有好几个月了，但是大部分数据都被储存在中央桌面中。导入和导出数据是一个繁琐的手动过程。不过，有了新的动态数据库 API，所有这些都可以自动化。

根据新闻稿，新的 API 可用于完成以下任务:

*   将第三方数据集成到中央桌面工作区数据库中
*   从集成的第三方应用程序自动更新中央桌面数据库
*   从中央桌面数据库查询和导出自定义数据集
*   将自定义数据集自动导入中央桌面数据库

中央桌面还发布了两个迷你应用程序，它们将成为默认安装的一部分。因为两个应用程序，一个时间跟踪应用程序和一个帮助台应用程序，是基于新的数据库 API 构建的，所以它们可以在 Central Desktop 中编辑。Garcia 向我展示了向应用程序与之交互的数据库中添加新字段是多么容易，例如，添加一种跟踪费用和时间的方法。

![](img/4b994a9b1c958c2c18bfa764eaba033c.png "central-desktop_edit-screen")

我问 Garcia，最终目标是否是创建一个类似 Salesforce 的平台(Central Desktop 实际上是 Salesforce AppExchange 的成员)，在这个平台上，第三方将在 Central Desktop 上开发应用程序，客户可以挑选安装哪个。他告诉我，最终，这很可能是开发人员采用 API 的方向，他对此很高兴。

不过，目前的目标是为客户最需要的服务建立挂钩——比如与 Quickbooks 交互或从 LinkedIn 导入数据的能力——并帮助客户自己建立从外部应用程序吸取数据或向外部应用程序推送数据的服务。Garcia 说，Central Desktop 已经与许多公司就使用新 API 构建应用程序进行了洽谈，但不能透露是谁。

## 分享这篇文章