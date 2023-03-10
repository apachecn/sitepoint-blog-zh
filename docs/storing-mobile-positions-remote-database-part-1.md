# 在远程数据库中存储移动位置，第 1 部分

> 原文：<https://www.sitepoint.com/storing-mobile-positions-remote-database-part-1/>

## 介绍

在我以前的文章中，我们看到了如何使用瓦坎达工作室开发移动网络应用程序，以及如何使用 PhoneGap 扩展将它打包到本地应用程序中。虽然应用程序使用地理定位 PhoneGap API 来获取移动位置(经度和纬度)，但是应用程序仅在警报消息中显示位置，而不处理或在数据库中存储该位置。

在本文中，我们将了解如何使用 Wakanda AJAX 框架(WAF)通过 REST/HTTP 将位置发送到 Wakanda 服务器数据库。

这样，应用程序的管理员就可以知道用户的位置，跟踪用户的路径，并在地图中表示出来。代码和概念可能对有这种需求的其他开发人员有用。

在第一部分中，我们将看到应用程序的架构并设计数据模型，我们将在其中保存移动位置。

## 背景

在您开始使用本指南之前，您应该看看我以前的文章，或者熟悉 Wakanda、PhoneGap、REST/HTTP 和数据库。

## 体系结构

该应用程序将使用[瓦坎达数据存储库](http://doc.wakanda.org/Datastore/Datastore.100-588923.en.html)作为 NoSQL 数据库，我们的本地应用程序将远程访问该数据库，以使用 REST/HTTP 和 JSON 格式保存移动位置。

Wakanda 服务器是一个 HTTP 和数据库服务器，可以使用 REST/HTTP 远程访问，因此不需要创建 web 服务或 REST API 来管理移动应用程序(控制器)对数据库中数据的操作。我注意到使用 NodeJS 和 MongoDB 可以实现相同的后端。

![MVC ](img/f5bc893834ffba37c248bfd30d1b068d.png)

在我们的应用程序中，我们将使用模型-视图-控制器(MVC)架构来分离数据层和表示层。这种分离对于代码可重用性和应用程序维护非常有用:

*   **视图:**应用程序的客户端部分，显示数据的网页
*   **模型:**将使用 Wakanda Studio 设计的数据模型
*   **控制器:**模型和视图之间的中间部分，将 Rest HTTP 请求转换为数据库查询，反之亦然。它是 WAF 一部分

## 发展环境

将使用 Wakanda Studio 模型设计器设计数据模型。此功能使开发人员能够添加数据类(RDBMS(关系数据库管理系统)中的表)、属性(RDBMS 中的列)、事件、数据存储方法(存储过程)和许多其他数据库功能。

可以使用模型设计器，通过拖放数据类并向这些数据类添加关系，或者通过使用 Wakanda 数据模型 API 的代码，来设计数据模型。在 Wakanda 中，所有的编码都是用 JavaScript 完成的。

在 Wakanda 中，数据模型大致相当于 UML 类图，它表示数据类、每个数据类和属性的属性、事件和方法。我注意到每个数据类都有一个范围属性，可以是“Public”或“Public on Server”。在我们的应用程序中，我们需要访问服务器外部的数据类，所以我们应该确保我们的数据类的范围是公共的。

![Design a model](img/8a90084c5a941ea69406fd85027a6399.png)

## 创建数据模型

正如我在以前的文章中解释的那样，您应该[下载](http://www.wakanda.org/downloads)Wakanda 版本 4 的生产发行版。下载瓦坎达后，你会有两个文件夹:第一个是瓦坎达服务器，第二个是瓦坎达工作室。在 Wakanda Studio 文件夹中，您应该运行“Wakanda Studio.exe”以进入 ide 并创建一个解决方案。

**注意:**Wakanda Studio 也可用于 Mac 或 Linux 版本，但我们可以在 Windows 或 Mac 上开发应用程序，并将其托管在 Linux 服务器上。只有 Wakanda Studio 在 Linux 中不可用，但在 Linux 中运行 Wakanda 服务器是可能的。

### 创建解决方案

打开瓦坎达工作室，创建一个新的解决方案。

![Wakanda Studio](img/0c20497cfa9643b097686a3677db5470.png)

为您的解决方案命名，然后单击 OK 按钮。

![Give it a name](img/bd53821631f323c04f266322ae0dc911.png)

现在，点击 Wakanda Studio 工具栏中的模型设计器图标。

![model designer icon](img/bcb7f9f59ad1f2b36999ff6678c768f9.png)

现在向您的数据模型添加一个 dataClass 默认情况下，您会发现一个数据类 dataClass1 只包含一个属性:ID，我们将修改这个数据类的名称并添加一些属性。

![dataclass](img/1e504f5dd9cecd59288e8279999903a6.png)

在我们的数据模型中，我们有一个数据类 Position，它包含四个属性:ID (long 和 primary key)、经度(string)、纬度(string)和 creationDate (date)。

## 结论

在本文中，我介绍了使用 MVC 架构的应用程序的完整设计和架构；我还解释了如何创建 Wakanda 解决方案和存储移动位置的数据模型。

在下一篇文章中，我们将了解如何在数据存储中保存数据，开发视图和将数据发送到 Wakanda 服务器的代码。

## 分享这篇文章