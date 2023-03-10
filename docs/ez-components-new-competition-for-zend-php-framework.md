# eZ 组件:Zend PHP 框架的新竞争

> 原文：<https://www.sitepoint.com/ez-components-new-competition-for-zend-php-framework/>

就在大约一个月前，Zend Technologies [宣布了](http://www.zend.com/news/zendpr.php?id=109) [Zend PHP 框架](http://www.zend.com/collaboration/framework-overview.php)与 Zend PHP 协作项目保持一致，其目标是提供一个“事实上的标准 PHP Web 应用程序开发和部署环境”。Zend 没有公开多少信息，这引起了很多[的讨论](https://www.sitepoint.com/forums/showthread.php?t=308727&page=1&pp=25)。只是这里[那里](http://netevil.org/node.php?nid=633)和[那里](http://andigutmans.blogspot.com/2005/10/zend-framework-post-is-too-long-so.html)，相关人员畅所欲言，传递或多或少有用的信息花絮。目前，Zend 正在开发第一个版本，同时制定协作基础设施和参与指南。

当 Zend 在 PHP 世界享受关注的时候， [eZ systems](http://ez.no/) 坚定地致力于一个非常相似的项目，叫做 [eZ components](http://ez.no/company/news/ez_components_1_0_beta1) 。让我们仔细看看它提供了什么，以及它与 Zend 的 PHP 框架相比如何。

**设计**

这两个项目似乎都是独立的、松散耦合的组件，以减少依赖性，每个用户都可以自己选择使用哪一个。虽然 eZ components 没有显示指定应用程序设计的迹象，但 Zend PHP 框架中的 Zcontroller 和 ZpageController 组件可能预示着某种 MVC 框架。

**组件**

eZ 组件的列表可以在这里找到。不幸的是，很难将其与 Zend 的 PHP 框架相比较，因为只有一个很小的目录列表[可用](http://netevil.org/node.php?uuid=435d92ba-dad2-0602-1074-35d92bab0330)，日期是 10 月 24 日。然而，也可以找到第一个相似之处:在两个项目中都可以找到框架的标准组件，如数据库抽象、异常、日志、模板以及输入过滤器。

除此之外，Zend 的框架还提供了搜索和 URI 组件。虽然我仍然想知道前者会是什么样子，但我已经希望后者是“统治所有人的一个类”，作为我们的 [URI 问题](https://www.sitepoint.com/php-server-api-differences/)的答案。另一方面，eZ 提供了更广泛的工具集:有通用组件，如缓存、配置、模板和翻译，以及更具体的工具，如存档、文件、图像*、邮件和 PhpGenerator。

到目前为止，Zend 只提供了一个作为数据库抽象的 ActiveRecord 的实现。eZ 提供了一个类似的 PersistentObject 组件，但是在 PDO 和 DatabaseSchema 之上还有一个瘦数据库层，用来修补数据库模式。就个人而言，我希望看到一个额外的轻量级 ActiveRecord 实现，它可以在没有显式数据类型定义的情况下工作。

对于模板，eZ 依赖于 [eZ 发布模板语言](http://ez.no/products/ez_publish_cms/documentation/customization/custom_design/examples/template_language)，这与 Smarty 非常相似。目前还不知道 Zend 打算如何实现这个组件。当然，可以自由选择使用哪种模板也不错。

我能想到一些额外的(虽然不太重要)领域，这些领域最好有组件:

*   认证
*   网络服务
*   位置遥控(remote position control)
*   SQL 中的专用数据结构(有序列表、集合、树等)。)

**执照**

最近，eZ[将](http://www.derickrethans.nl/ez_components_lgpl_vs_bsd.php)从 T2 的 LGPL 牌照换成了更加宽松的 T4 BSD 牌照。这意味着它可以被任何人用于任何目的。Zend PHP 框架附带了一个“PHP 类型的许可证”,类似于 BSD 许可证。

**开发**

Zend 的框架依赖于社区的贡献，而 eZ components 是由 eZ publish 的开发者开发的。一方面，我可以想象很多人想要为一个框架做贡献，因为他们已经习惯了开源。另一方面，我们还不知道这对普通用户来说有多简单，也不知道 Zend PHP 框架需要履行哪些义务。然而，eZ 对其框架的设计和实现拥有完全的控制权。这将导致更一致的设计和代码。毕竟，他们的队伍中有一些有才华的开发人员，对这种工作很有经验。

**发布**

eZ 组件的第一个测试版已于 2005 年 11 月 28 日星期一发布。Zend PHP 框架的支持者必须要有耐心:据官方消息称，Zend 仍在“建立协作基础设施和参与准则”，这项工作要到 2006 年 1 月才能完成。

**结论**

由于 Zend Technologies 相当仓促地宣布了它的框架，他们不得不接受一些批评。与 eZ 相反，他避免浮夸的谈话，专注于编程。这得到了回报，因为他们现在能够窃取 Zend 的成果，这对我们来说是好事:eZ 提高了交付高质量 PHP 框架的标准，并迫使 Zend 配合他们的工作。

*   [eZ 组件主页](http://ez.no/company/news/ez_components_1_0_beta1)
*   [如何安装 eZ 组件](http://ez.no/community/blog/components_from_svn_tutorial)
*   [浏览 SVN 知识库](http://zev.ez.no/listing.php?repname=ezcomponents&path=/packages/&rev=0&sc=0)

## 分享这篇文章