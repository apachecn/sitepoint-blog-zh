# 用函数 Compute 和 MongoDB 构建无服务器 REST API

> 原文：<https://www.sitepoint.com/building-a-serverless-rest-api-with-function-compute-and-mongodb/>

在最近的比赛中，我们收到了许多优秀的参赛作品，以寻找充分利用阿里云服务的最佳技巧。对于我们的评委来说，从这么多有帮助和有趣的参赛作品中选出获胜者是一项有趣但具有挑战性的任务。但是，经过激烈的讨论和激烈的辩论，他们决定比赛的二等奖授予倪南河。他的获奖技巧是关于阿里云上无服务器应用架构的[系列的一部分，它涵盖了几个阿里巴巴产品。](https://www.sitepoint.com/community/t/building-a-serverless-application-using-alibaba-cloud-services/308043)

本教程将向您展示如何在阿里云上部署一个 MongoDB 数据库，并使用[函数 Compute](https://int.alibabacloud.com/m/1000018812/) 开发一个后端 API 系统与数据库进行交互。

## NoSQL 和 MongoDB 概述

长期以来，关系数据库一直被选为软件开发中管理数据的主要系统。它的 ACID 原则促进了数据持久性、事务完整性和并发控制。在过去的几年中，NoSQL(不仅仅是 SQL)变得流行起来。这个模型解决了关系数据结构(表、行、字段)和应用程序的内存数据结构(对象)之间的阻抗不匹配问题。最重要的是，NoSQL 的设计是水平伸缩的，这使得它成为现代 web 应用程序的绝佳选择。

NoSQL 可以分为四类:

*   键值
*   文件
*   柱族
*   图表

MongoDB 是文档数据库组中最受欢迎的系统。如[mongodb.com](http://mongodb.com)所定义，

> MongoDB 中的记录是一个文档，是由字段和值对组成的数据结构。MongoDB 文档类似于 JSON 对象。字段的值可以包括其他文档、数组和文档数组。

## 阿里云 ApsaraDB for MongoDB

MongoDB 数据库可以通过其 [ApsaraDB for MongoDB](https://int.alibabacloud.com/m/1000020292/) 服务部署在阿里云上。用户可以在 3 种定价方案中进行选择:

*   订阅(副本集)
*   PAYG(现收现付)(副本集)
*   PAYG(分片)

复制和分片指的是数据分布模型:

*   复制跨多台服务器复制数据。同一条数据存储在不同的地方。ApsaraDB for MongoDB 使用 3 服务器副本集。复制可能有助于横向扩展读取。
*   分片将数据分布在多台机器上，因此不同的机器包含不同的数据子集。这种模式允许水平扩展写入。

## 创建实例

在阿里云控制台，点击产品，会在 ApsaraDB 组下看到 ApsaraDB for MongoDB。或者，您可以使用搜索框来过滤所需的服务。

选择定价方案、地区、服务器规格，并为您的数据库设置密码。阿里云会根据你的选择告诉你这个服务花了你多少钱。

关于实例参数的更多信息见[这里的](https://www.alibabacloud.com/help/doc-detail/26572.htm)。

**注意:**如果您使用的是免费试用账户，记得选择订阅实例。PAYG 实例不包括在试验计划中。

## 设置 IP 白名单

为确保数据库安全，阿里云自动屏蔽所有对数据库的访问。您必须在白名单中指定 IP 地址，才能访问目标实例。

![ip](img/8a3cf17b9ef15d0e3e937a7251d17abe.png)

## 连接字符串

设置好 IP 白名单后，点击“*数据库连接*查看连接参数

![connection](img/c7faa6abc18168d3e2fefc5c93028272.png)

使用此连接字符串连接到 Node.js 代码中的数据库。

## 函数计算

Function Compute 允许您在不提供或管理服务器的情况下运行代码。该服务为您准备计算资源，并代表您灵活可靠地运行您的代码。您只需为运行代码时实际消耗的资源付费。如果你的代码没有被执行，你就不用付钱。

函数 Compute 运行您的代码来响应事件。当事件源服务触发事件时，会自动调用关联的函数来处理事件。

从阿里云控制台，选择 Function Compute，点击“ **+ 【T1””图标，创建一个新服务**

![1](img/c6896d4c6056fea4034979c9c0b3cf7e.png)

在新创建的服务中，点击“ **+** ”图标创建一个新功能。您将通过一个多步骤向导来为您的功能选择选项。

对于函数模板，选择“空函数”

![2](img/98c74c2ee5e1c532cca4393f4cca0c33.png)

在“配置触发器”步骤中，选择“HTTP 触发器”并为其命名。其他设置如下图所示

![trigger](img/7b1067eaf168e5416973e958ea520603.png)

在“配置函数设置”步骤中，为函数设置一个名称，并选择“nodejs6”作为运行时

![4](img/01717126130b8b5f23bb090e9e8b6db3.png)

在最后两步中单击“下一步”以完成向导并创建功能。

## 编程您的功能

单击您刚刚创建的函数，然后单击“Code”选项卡。这是您为函数运行提供代码的地方

![code](img/8fe087649a79a7b82386d04b7caff6c4.png)

使用来自 MongoDB 服务器的连接字符串。

此外，在这个屏幕中，您可以查看将调用您的函数的 HTTP 触发器。您也可以在这里运行触发器来测试您的功能。

![run](img/89799e5c5bde5b912ae1c549fa656871.png)

## 摘要

在本教程中，我们以 MongoDB 为例学习了 NoSQL 数据库。[阿里云](https://int.alibabacloud.com/m/1000018805/)向那些希望在其云上运行 MongoDB 服务器的人提供其 ApsaraDB for MongoDB 服务。然后，本教程将讨论 Compute service 函数，这是一种按照新兴的无服务器架构构建应用程序的新方法。它展示了一个由 HTTP 请求触发的 Node.js 函数的示例，该请求连接到 MongoDB 数据库并执行一个“ *insert* 命令。

## 分享这篇文章