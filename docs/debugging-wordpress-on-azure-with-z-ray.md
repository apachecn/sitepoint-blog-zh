# 用 Z-Ray 调试 Azure 上的 WordPress

> 原文：<https://www.sitepoint.com/debugging-wordpress-on-azure-with-z-ray/>

我们都知道 WordPress 是一个非常丰富、可扩展和简单的平台。这可能是它为今天如此多的网络提供动力的原因之一。但在某些情况下，这种丰富性可能会导致网站的衰落。例如，如果你加载了太多插件，或者选择了一个对你的应用程序来说太复杂的主题，你可能会看到繁重的数据库查询和延长的执行时间。

这意味着你需要一个简单、安全和可靠的方法来描述你的 WordPress 应用程序，这将帮助你识别出降低速度的瓶颈。

![Screenshot 2015-10-09 12.39.38](img/9c648680304b4bf59c83dbe94bd3d9f9.png)

本文演示了一种简单的方法来做到这一点。它描述了如何使用 Z-Ray for Azure 调试部署在微软 Azure 的 [web 应用服务](https://azure.microsoft.com/en-us/documentation/articles/app-service-web-overview/)上的 WordPress 应用程序——这是几个月前[宣布的一项新技术，目前处于预览模式。虽然 Z-Ray 作为 Azure 开发者的独立工具是一个新产品，但 Z-Ray 本身并不是。Zend 在一年前将其作为 Zend Server 的一部分推出。网上有很多资源可以用来了解 Z-Ray 是什么，但为了让我们都在同一页上，这里有一个非常简短的描述。](https://azure.microsoft.com/en-us/blog/supercharge-your-php-productivity-on-azure/)

Z-Ray 是 PHP 开发人员的一个调试工具，它提供了关于 PHP 应用程序内部、浏览器中正在发生的事情的信息，而不需要修改任何代码。使用 Z 射线直播！，开发人员还可以分析进入服务器的非基于浏览器的请求，比如移动和 API 请求。除了通用的 PHP 信息，Z-Ray 还提供了特定应用和框架的详细信息，如 WordPress、Magento、Drupal、Symfony、Laravel 和 Zend Framework。关于这些特性的更多信息，这篇文章提供了一个很好的概述。

**注意**:在我们开始之前，请注意，要执行下面的步骤，你需要一个有效的 Microsoft Azure 帐户([免费试用](https://azure.microsoft.com/en-us/pricing/free-trial/))。

## 步骤 1:创建一个 WordPress Web 应用程序

我们的第一步是在 Azure 上创建一个新的 WordPress web 应用。

Azure 提供了许多使用 WordPress 的简单方法。在这种情况下，我们将部署一个名为 Scalable WordPress 的 WordPress 包，它已经包含了一个基本的 WordPress 设置，包括数据库配置。

1.  登录 Azure 门户后，点击左上角的**新建**按钮。

2.  在创建刀片中，选择 **Web + Mobile** ，然后选择**可扩展 WordPress** 。

    ![Scalable WordPress blade](img/7899e8880153437770f2334beb343843.png)

3.  在滑动打开的可扩展 WordPress blade 中，您现在需要配置您将要在 Azure 上部署的 WordPress web 应用程序。
    首先为 web 应用的资源组选择一个名称。在 Azure 中，资源组包含与特定应用程序相关的资源。例如，应用程序本身、数据库和存储帐户。您可以为 web 应用程序的资源组使用建议的名称，也可以输入一个新名称。

4.  接下来，选择 web 应用程序设置部分，并在滑动打开的 Web 应用程序刀片中配置以下内容:

    *   URL–输入您网站的 URL
    *   应用服务计划–输入服务计划的新名称。服务计划基本上是一组你可以在所有 Azure web 应用上共享的功能。
    *   定价等级–选择符合您要求的定价等级
    *   位置–选择托管网站的位置

    ![Web App settings](img/f1fac1614d9189e721b086820be148d5.png)

5.  点击**确定**。

6.  选择 database settings 部分，并选择是使用现有的 MySQL 数据库还是创建一个新的数据库(在这种情况下，您需要定义一个新的名称，选择一个定价层和位置，并接受法律条款)。

    ![SQL configurations](img/4e2c6eda5416d1c5f14d6e39f5c7a928.png)

7.  接下来，选择存储设置部分，并选择是使用现有的[存储帐户](https://azure.microsoft.com/en-us/documentation/articles/storage-create-storage-account/)还是创建一个新的帐户(在这种情况下，您需要定义一个新的存储帐户名称)。

8.  刀片中的最后一部分显示了将要使用的订阅。如果您想使用不同的订阅，请选择此部分。
9.  就是这样。您已经完成了新 web 应用设置的配置，不要担心，如果您忘记了其中一个配置步骤，或者如果您第一次就弄错了，Azure 将确保您知道这件事。

    点击**创建**来部署你的新 WordPress web 应用。
    现在我们被带到开始板，在那里我们可以看到我们的 web 应用程序正在被创建。这可能需要一两分钟，一旦我们的 WordPress 应用程序准备好，它的刀片将自动打开。

    ![Web App blade](img/dc6bcf214ac47dfbf4e730f9b481693b.png)

10.  点击刀片顶部的**浏览**按钮打开你的 WordPress 应用程序。
    著名的 WordPress“五步”配置向导显示出来。

    ![WordPress configuration wizard](img/8192fe0a7f0c60331ce1bf2f644b011b.png)

11.  完成安装步骤，并启动 WordPress(确保记下您定义的进入管理面板的密码)。

    ![WordPress launched](img/b79cdc271d4fae8a3dda88184c9de96a.png)

## 步骤 2:启用 Z 射线

太好了！我们已经在 Azure 上成功部署了一个 WordPress web 应用。现在，我们如何让 Z 射线工作？

1.  在 web 应用程序 blade 中，打开工具菜单。为此，点击刀片顶部的**工具**图标。

    ![Tools blade](img/ef99b378622d4148dc73d5acd6cf14f1.png)

2.  在 Tools blade 的 Develop 部分，您会看到各种可用的开发工具和特性。选择 **Zend Z-Ray** ，然后**启用 Azure 的 Z-Ray**。

3.  接下来，您会得到一个可用定价层的列表，包括每月的成本估计。决定哪一层最适合你，点击**选择**。

    **注意**:微软 Azure 上的 Z-Ray 目前正在预览中，因此价格可能会在未来发生变化，恕不另行通知。

4.  通读随后显示的法律条款。如果您接受它们，请点击**购买**。

5.  过一会儿，你会收到一个通知，告诉你 Z 射线已成功启用。这意味着你的 WordPress web 应用程序已经启用了 Z-Ray。

    ![Z-Ray success message](img/5e7d7b8f828d33a208754438ce197ac3.png)

干得好！要开始使用 Z-Ray，只需在浏览器中刷新您的 web 应用程序。z 射线显示在页面底部。

![Z-Ray enabled on WordPress](img/ddb2beebc292fdeaac1767e85a1dde70.png)

## 步骤 3:用 Z-Ray 开发 WordPress

恭喜你！您已经成功部署了您的 WordPress web 应用程序并启用了 Z-Ray。下一步是什么？

要开始使用 Z-Ray，只需选择 Z-Ray 上的各种面板，并开始深入代码。

开箱即用，Z-Ray 跟踪并显示以下详细信息:由您的应用程序触发的函数、执行的 SQL 查询(包括查询的绑定值、查询的结果、查询花费的时间以及受查询影响的代码行数)、错误和警告、抛出的异常等等(要详细了解 Z-Ray 的主要特性，请阅读此[文章](https://www.sitepoint.com/top-10-z-ray-features-check/))。

对于 WordPress 开发者来说，Z-Ray 显示了 WordPress 元素和代码结构的附加信息。这些信息可以通过打开 Z-Ray 右侧的 WordPress 面板来访问。

让我们仔细看看。

### 仪表盘

仪表板面板给你一个很好的 WordPress 应用程序的概览——用一个图表显示插件、主题和核心的加载时间，以及关于应用程序配置的其他一般细节，包括版本、插件数量、数据库查询数量等等。

![](img/b315f26fca8a44f711394dbf0d8aadf5.png)

### 缓存对象

“缓存对象”面板为您提供了页面缓存对象的精确图片。这包括描述最大缓存对象及其大小的图表，以及包含大小和命中次数的对象的完整详细分类。

![cache_objects_panel](img/94374c6bbddb50cb587c299784597242.png)

### 插件

插件面板是非常有用的，因为它可以给你一个很好的关于加载的插件和它们消耗的资源的视图。该面板显示了一个描述插件相对加载时间的图表，以及一个显示每个插件的名称、版本、状态、加载时间和路径的表格。

![](img/eb8bcbffef6dcaf2be373384b3694efc.png)

### 主题

Z-Ray 还可以帮助你描述你的应用程序所使用的主题。在主题面板中，您可以看到关于所使用的 CSS、执行时间和所使用的文件的信息。

![](img/2a854338578d77d66997b2581c11f127.png)

### 钩住

钩子面板使你能够看到在执行过程中触发的所有 WordPress 钩子，包括核心钩子(要看到核心钩子，只需选择左边过滤器区域中的 **Core** 复选框)。您可以查看挂钩的名称、类型(操作/过滤器)、谁调用了挂钩、执行挂钩所用的时间、挂钩优先级以及文件的名称。

![](img/1b666adab7a7d74c2c4000d8d6227104.png)

### WP 查询

WP 查询面板显示当前 WordPress 主查询的信息。

![](img/322bb00741743a126816e3c9f705974d.png)

### 克朗斯

最后但同样重要的是，在 Crons 面板中，你可以深入了解 WordPress cron 系统。查看挂钩的函数、它们的调度、为 cron 事件定义的任何参数以及下次执行的时间。

![](img/be9686735ef2b717c58cacb6bf5dc7cd.png)

## 步骤 4:调试移动和 API 请求

Z-Ray 还支持使用 Z-Ray Live 调试非基于浏览器的请求！–一种工具，可实时监控向服务器发出的所有请求，包括由 API、web 服务和移动请求发出的请求。

**注** : Z 射线直播！仅在 Z-Ray for Azure 的标准版上受支持。

使用 Z 射线直播！您需要打开 Azure 管理仪表板的 Z-Ray，这是一个用于管理和配置 Z-Ray 的用户界面。
为此，点击 Zend Z-Ray for Azure blade 中的 Z-Ray 管理仪表板链接。

![Z-Ray management dashboard link](img/6013f763576035c74e43b57bbe557d5c.png)

显示 Z 射线管理仪表板后，只需选择 Z 射线实时！页面。页面加载后就开始跟踪请求，所以只需等待请求蜂拥而至！为了快速测试，在浏览器中重新加载你的 WordPress 应用程序。

![Z-Ray Live!](img/7d4aecf9434aa6cadc760921482b3003.png)

## 步骤 5:在生产中使用 Z 射线

在我们分道扬镳之前，这最后一步将解释如何在生产中安全使用 Z-Ray。

Z-Ray 安全模式允许您通过将访问令牌作为 GET 参数传递给 URL，在特定时间段内为特定 IP 或 URL 启用 Z-Ray。只有使用这个令牌的 URL 才能使用 Z-Ray。

那么，如何创建新的访问令牌呢？

1.  在 Z 射线管理仪表板中打开**访问模式|模式**页面。
2.  选择安全模式，点击**保存**。出现提示时，请验证您的选择。Z-Ray 现在处于安全模式，如果你刷新你的 WordPress 应用程序，Z-Ray 将不会显示。
3.  接下来，打开**访问模式|令牌**页面，点击**创建令牌**。将显示“创建访问令牌”对话框。

    ![Create Token dialog](img/5b018651b4d61d6f416aee2f7ca67c5b.png)

4.  配置新的访问令牌:

    *   名称—输入新令牌的描述，仅供参考。
    *   IP 限制–输入以逗号分隔的 IP 列表，代表您希望启用 Z-Ray 的计算机，可以是屏蔽范围或 CIDR。示例:127.0.0.1 将限制对本地计算机的访问，10.0.0.0/8 将开放对任何以 10 开头的地址的访问。
    *   URL 限制(可选)–输入您希望允许访问 Z-Ray 的页面的 URL。**注意**:该令牌设置选项不是强制的。但是，将此字段留空将允许服务器上的所有页面访问 Z-Ray。
    *   时间限制–为访问令牌设置时间限制，超过该时间后将不再启用对 Z-Ray 的访问。
5.  点击**创建令牌**。令牌已创建，您将被告知如何访问 Z-Ray。

    ![New Token](img/a5f913e4bd8a9f824daa1c801427a9cc.png)

6.  要使用这个令牌，您只需将它作为 GET 参数添加到页面 URL 中。

## 摘要

Z-Ray 和 Azure 的结合意味着 WordPress 开发者，事实上 PHP 开发者作为一个整体，可以从微软 Azure 的 web 应用服务和 Z-Ray 的内省功能中获益。能够轻松地深入了解构建页面的各种元素将有助于开发人员缩短开发时间，并最终产生更好的代码。

希望你喜欢阅读！

Zend 和微软一直在寻求反馈，所以如果你有任何改进 Azure 的 Z-Ray 的想法，请随时联系 zrayazure-feedback@zend.com！

## 分享这篇文章