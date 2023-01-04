# 7 个与 Drupal 兼容的 CRM 选项

> 原文：<https://www.sitepoint.com/7-crm-options-compatible-drupal/>

我喜欢 Drupal，并最终用它承担了我的大部分编程项目。我已经使用它很长时间了，我发现用 Drupal 推出项目比用其他任何东西都容易得多，尽管它的学习曲线臭名昭著。

无论你想称 Drupal 为 CMS(内容管理系统)、CMF(内容管理框架)还是 CMS 什么的，C 总是代表内容。内容是 Drupal 的亮点，也是它的设计目的。

![drupal8wide](img/077a30fb14c973a02eea34dedb6e8ea1.png)

当一个组织处于这样一个阶段和心态时，他们也希望有效地管理他们的联系和互动，他们通常需要专门为该功能设计的工具。这些通常被称为 CRM，代表客户关系经理或委托关系经理，取决于行业(分别为营利性或非营利性)。CRM 是一项大业务，有许多免费和付费的选项，都有自己的优点和缺点。

通常，人们与您的组织之间的互动包括注册活动、捐款、成为会员、表达对产品的兴趣或接收时事通讯。这听起来很简单，但在数字领域中表示业务规则通常非常困难，因为每个人都认为“他们的方式”是“唯一的方式”，并且每个现成的系统都应该开箱即用地表示他们。

CRM 和 Drupal 有什么关系？没有直接的，但间接的如果你正在寻求简化你的业务运作和自动化的方式，人们可以与你互动，你的客户关系管理将需要与你的网站很好地工作。

如果你正在读这篇文章，我会假设你的网站很可能是用 Drupal 构建的，并且，不出所料，Drupal 在 CRMs 领域继续发挥其与他人合作的才能。

在本文中，我们将看看 CRM 领域中与 Drupal 合作良好的几家大公司，它们是如何集成的，或者开发人员如何让它们集成。

## 自己卷

虽然我在上面提到 Drupal 的目标是内容，但 Drupal 总是擅长于内容之间的关系，或者用更新的说法，“实体引用”。理论上，您可以为联系人(或用户实体)创建内容类型，为他们可能与您进行的交互以及其他模块(如事件和商务)创建内容类型。然后创建一堆视图，你就有效地拥有了一个“客户关系管理”。在一些简单的情况下，这可能就足够了，您可以完全控制您想要表示的过程。这可能会耗费开发人员大量的时间，使用专门为这项工作设计的工具可能是一个更好的解决方案。

## 雷德肯

ThinkShout 的团队有效地遵循了上面的建议，并创建了一系列模块，这些模块将为您提供大量 Drupal 原生的 CRM 功能。它采用了 Drupal Commerce 的方法，不是为您提供现成的功能，而是为您提供一个创建该功能的工具包。这确实意味着您可能仍然需要自己承担大量额外的工作，但是您并没有向您的技术堆栈中添加新的系统以及可能带来的问题。RedHen 的另一个缺点是它没有定期更新(与列表中的其他选项相比)，它的发布周期更多地取决于 ThinkShout 何时有客户需求或运行代码 sprint。

如果你想定制 [RedHen](https://www.drupal.org/project/redhen) 那么你可以创建自己的模块和主题覆盖集，这就是 Drupal！

## CiviCRM

通常在搜索 Drupal 的 CRM 选项时，人们会遇到 [CiviCRM](http://civicrm.org) ，它最初是作为 Drupal 模块出现的。我用 CiviCRM 做了很多工作(并为这个项目做出了贡献),它伪装成 Drupal 模块的事实常常是一个很大的困惑点。最好想想它实际上是什么，一个恰好与 Drupal 集成得很好的外部服务，看起来就像是它的一部分。CiviCRM 是一个定制的 PHP 应用程序，也集成了 Joomla！和 WordPress，主要面向非营利组织，但也可以定制为与商业组织合作。CiviCRM 自带了许多功能，因此是一个相当重要的程序，作为一个开源项目，它有时可能会有点粗糙和不一致，但在过去的一年中，开发已经增加并成熟了很多。

CiviCRM 在许多地方直接与 Drupal 集成，有些地方比其他地方更好，这些地方包括:

*   Drupal 用户和联系人记录之间的直接(可选)关系，以及 Drupal 角色和 CiviCRM 组和成员之间的同步。
*   用于构建 CiviCRM 实体列表的视图。
*   规则和触发器
*   用于替换 CiviCRM 内置表单的 Webform。
*   商业和 Ubercart
*   有机基团
*   主题化(有点)。您可以使用 Drupal 主题来覆盖 CiviCRM CSS，但 CiviCRM 确实使用了自己的模板引擎(目前很聪明，但可能会切换到 twig)。
*   另一种选择是将 Drupal/CiviCRM 模块放在一边，以“无头”方式使用 CiviCRM，利用它的 API 和 REST 接口。

CiviCRM 提供了大量自己内置的定制选项，我就不赘述了，因为是 PHP，所以你们中的很多人应该都很熟悉。您还可以通过创建自己的 Drupal 模块，直接利用 CiviCRM 进行 Drupal 定制。在该模块中，您可以访问 CiviCRM 挂钩和 API 函数，这些函数提供了无尽的选项来满足过多的客户端需求。

## 销售力量

[Salesforce](https://www.salesforce.com/) 是以商业销售为重点的公司的主要参与者，并且在许多业务流程中占有一席之地。它是一个大型应用程序，有自己长期建立的生态系统和扩展市场。它很贵，是专有的，用自己的编程语言‘Apex’编写。发展速度很快，这是一个相当稳定的系统。

Salesforce 是基于云的 CRM，因此集成通过 REST 接口进行。幸运的是，由于 Salesforce 在这类环境中非常受欢迎，Drupal 也很受欢迎，因此有一些预构建的选项。请记住，您的许可证可能会限制您可用的选项:

*   [SalesForce Suite](https://www.drupal.org/project/salesforce) 是一个历史悠久的模块，它让您开始了整合之旅。它将处理身份验证，将 Drupal 实体映射到 SalesForce 字段，在 Drupal 和 SalesForce 之间推送和提取数据，以及与 SalesForce 的旧 SOAP API 连接的遗留模块。
*   通过一个[模块](https://www.drupal.org/project/salesforcewebform)有一些基本的 Webform 集成，看起来开发停滞了。
*   Springboard 是一个来自 Jackson River 的商业 Drupal 发行版，它将 Drupal 和 SalesForce 捆绑在一起，专门为非营利组织提供服务。不太清楚它提供了什么，但它包括支持。还有一些其他机构提供 Drupal / SalesForce 集成服务。

理论上，一旦您将 SalesForce 条目表示为 Drupal 实体，您就可以利用 Drupal 提供的大量定制选项，如视图、规则和模块挂钩。如果您想增强 SalesForce 的功能，那么它可以通过 Drupal 完成并推送到 SalesForce，但是您可能需要在某个时候学习 SalesForce 本身。

## sugarcrm

Sugar 是另一个历史悠久的玩家，有社区版(开源 PHP)和商业版。它针对的是以商业销售为重点的组织，与 SalesForce 非常相似，开发速度很快，Sugar CRM 也有自己的扩展市场。

OSSCube 创建了一个 [Drupal 模块](https://www.drupal.org/project/drupaltosugar)和附带的 [SugarCRM 项目](https://web.archive.org/web/20140911060507/www.sugarforge.org/projects/sugar2drupal/)以允许一些直接集成。我没有亲自使用过它们，发现文档有些难以理解。然而，它声称提供:

*   Drupal 和 Sugar 实体之间的字段映射
*   两个系统之间的数据同步
*   Webform 集成创建糖实体

同样，您也可以选择利用 Sugar 的 [SOAP](https://support.sugarcrm.com/02_Documentation/04_Sugar_Developer/Sugar_Developer_Guide_7.2/70_API/Web_Services/40_Legacy_REST/SOAP_APIs/) 和[REST](https://apidocs.sugarcrm.com/)API 来创建 Drupal 实体，并从那里继续。Sugar 还提供 LDAP，因此有可能在两个系统之间创建单点登录。

## 其他选项

我把其他几个选项组合在一起。所有的 CRM 都有很大的不同，但是它们的集成选项几乎是相同的，所以我不想一遍又一遍地重复这些选项。过去，我曾尝试与他们中的一些人一起进行 Drupal 集成工作，但常常难以完成任何事情。这很大程度上是由于这些系统的专有性质，找到关于它们的 API 和开发人员选项的信息是一项具有挑战性的工作，除非你支付一些钱。

### 微软动力和 BlackBaud

有必要将 [Dynamics](https://web.archive.org/web/20141216184514/http://www.microsoft.com/en-us/dynamics/default.aspx) 和 [Blackbaud](https://www.blackbaud.com/) 包括在内，因为它们是 CRM 领域的大玩家。两者都为 CMS(和其他系统)提供了自己的选项，所以通常不愿意在他们的生态系统之外提供集成选项。

### 国家建设者和萨尔萨

这两个 CRM 都是针对新一波的专注于参与的组织。它们不仅仅是跟踪贡献和互动，还鼓励互动。

Salsa 有一些针对 Drupal 6 的集成模块，所以如果你觉得慷慨，你可能想更新它们。否则，这就是一个通过 Salsa API 进行交流的过程，幸运的是，有一个正在开发的 Drupal 模块可以帮助您开始。

NationBuilder 已经尽力不创建任何直接的 CMS 集成，称他们内置的 CMS 是更好的选择。然而，他们有一个 [API](https://nationbuilder.com/api_documentation) ，可以和下面提到的选项一起使用。

#### 如何与 Drupal 集成？

以下是一些潜在的选择，但你的里程可能会有所不同:

*   利用 CRM 的 API(如果您的版本/许可证中存在)将实体拉入 Drupal，然后通过[服务](https://www.drupal.org/project/services)或自定义模块以类似于 SalesForce 选项的方式继续。
*   使用 [Migrate](https://www.drupal.org/project/migrate) 模块和可选的 MSSQL Server PHP 扩展。NET 系统直接查询 CRM 数据库并导入 Drupal 实体。同样，您的许可证和设置可能允许也可能不允许这样做。
*   有一个[沙盒模块](https://www.drupal.org/project/1238658/git-instructions)用于 Dynamics 集成，上次我试过了，它已经不起作用了，但你可能想更新它。

## 结论

这绝不是一个全面的 CRM 列表，也不是将它们与 Drupal 集成的可能选项。我主要关注我有经验的那些，不可能跟上所有不断出现的新选择。我也有更多与非营利组织合作的经验，所以我的解决方案有点偏向于这个领域。你尝试过用 Drupal 连接哪些系统？你成功了吗？你必须克服任何障碍吗？让我们知道！

## 分享这篇文章