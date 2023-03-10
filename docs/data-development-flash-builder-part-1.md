# 使用 Flash Builder 进行数据驱动的开发，第一部分

> 原文：<https://www.sitepoint.com/data-development-flash-builder-part-1/>

在本系列的第一部分中，我们学习了如何将模型作品导入 Adobe Flash Catalyst，并开始将各种元素转换为工作组件。在练习结束时，我们有了一个简单的联系人应用程序的活生生的原型，我们可以在 Adobe Flash Catalyst 中运行并与之交互。

本文将向您展示如何采取下一步行动:从 Adobe Flash Catalyst 迁移到 Adobe Flash Builder。我们将使用 Adobe Flash Builder 的数据驱动开发功能来将我们的 Flash Builder 应用程序与 PHP 集成，并快速创建 CRUD 服务来支持我们刚刚起步的应用程序。

我假设您对 Eclipse 平台有基本的了解:只要您理解透视图、编辑器和视图，就应该没问题。我们还假设您对 Flex 和 MXML 有一些初步的了解。您应该在本地 web 服务器上运行 PHP 和 MySQL，并且能够轻松地创建数据库和表格并向其中添加数据。

要继续学习，您应该有我们在上一个教程中创建的 Flex 项目。该项目，以及包含我们将要添加的所有代码的完整版本，都包含在本文的[示例代码档案中。如果你想看看完成的项目，你仍然需要设置一个 PHP/MySQL 数据源，如](http://sitepoint-examples.s3.amazonaws.com/flashbuilder/flex_data_1.zip)[中“设置 PHP 连接”](#section_php_connection "Set Up PHP Connections")一节所述。我建议只使用`start.fxp`文件并一步一步地完成教程，因为您将更好地理解如何从 Flex 项目连接到数据源。

阅读完文章后，请务必在我们由 Adobe 赞助的文章测验中[测试自己！](https://www.sitepoint.com/quiz/flex4/data-development-flash-builder-part-1)

## 项目概述

在第一篇文章的[中，我们开始构建一个非常简单的联系人列表应用程序，提供联系人列表、搜索框和联系人视图窗格，如图](https://www.sitepoint.com/article/prototype-with-flash-catalyst)[图 1，“联系人应用程序模型”](#fig_mockup "Figure 1. Contact application mockup")所示。

**图一。联系应用模型**

![Contact application mockup](img/fe78e7e85138e3366b8d8ebb30adc5b4.png)

如果我们加载我们的 Flash Catalyst 项目，我们可以看看为我们生成的代码。点击工作区弹出菜单，如图[图 2，“设计工作区”](#fig_design "Figure 2. Design workspace")。此菜单允许您在设计和代码工作区之间切换。上面会写着设计。

**图二。设计工作空间**

![Design workspace](img/721f0a302a6708c67b61e9332e368bf1.png)

点击它，切换到代码工作区。如果您展开右侧的项目导航器面板，您会看到 Flash Catalyst 为我们生成的各种包、皮肤和类。花点时间浏览一下，习惯一下包装布局。

## 从 Catalyst 转向 Flash Builder

Flash Catalyst 和 Flash Builder 共享一个通用的项目文件格式，这使我们可以在两者之间轻松切换。按照以下步骤在 Flash Builder 中启动并运行您的项目。

1.  打开 Flash Builder。

2.  点击文件菜单，选择导入 Flex 项目(FXP)… 。

3.  确保文件:选项被选中，然后点击相应的浏览… 按钮。

4.  导航到您保存 catalyst 项目文件的位置(如果您从本文开头的链接下载了该文件，则该文件被命名为`start.fxp`，选择它，并单击 OK 。

## 创建数据库

为了使我们的联系人应用程序有用，我们需要一个实际的数据库来存储我们的联系人数据。我们现在只需要一个表来保存联系人记录，所以只需在 MySQL 中创建一个名为`contacts`的新数据库，并使用这个脚本来创建`contact`表:

```
CREATE TABLE contact (  contact_id int(11) NOT NULL AUTO_INCREMENT,  first_name varchar(20) NOT NULL,  last_name varchar(20) NOT NULL,  phone varchar(20) DEFAULT NULL,  email varchar(150) DEFAULT NULL,  address1 varchar(30) DEFAULT NULL,  address2 varchar(30) DEFAULT NULL,  state varchar(10) DEFAULT NULL,  city varchar(20) DEFAULT NULL,  country varchar(20) DEFAULT NULL,  postcode varchar(10) DEFAULT NULL,  PRIMARY KEY (contact_id)) ENGINE=MyISAM DEFAULT CHARSET=latin1;
```

我们将使用 Flash Builder 的功能连接到该数据库，因此通过在 MySQL 中执行以下命令为该数据库添加一个用户:

```
grant all on contacts.* to fb@'127.0.0.1' identified by 'catalyst2';
```

## 设置 PHP 连接

下一步是将 Flash Builder 配置为连接到 PHP。

在 Flash Builder 的 Package Explorer 面板中，右键单击您的新项目。从上下文菜单中，选择属性。当属性对话框出现时，从左侧的选项中选择 Flex Server ，并配置选项以匹配[图 3，“Flex Server settings”](#fig_flex_server_settings "Figure 3. Flex Server settings")中显示的选项。

**图 3。Flex 服务器设置**

![Flex Server settings](img/08cf06c2afc064193fc2e8785758d8c0.png)

您需要更改 web 根目录和根 URL 以匹配您的 PHP 设置——使用验证配置按钮以确保 Flash Builder 可以连接到您给定的位置。一切正常后，点击确定保存您的设置。

## 分享这篇文章