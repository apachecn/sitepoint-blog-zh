# 回顾–IsolSoft 支持中心 1.5 版:故障诊断变得简单

> 原文：<https://www.sitepoint.com/v1-5-trouble-ticketing-made/>

任何面向服务的企业都必须提供一种方法来支持它所服务的那些人。在信息技术领域，这一点尤为重要，因为时间就是金钱。企业越依赖技术，就越有必要提供维持业务连续性所需的正常运行时间。

我最近听到的一个统计数据是，如果负责管理股票交易的纳斯达克技术股票数据库发生故障，股东和交易商将在系统停机时每小时损失 100 多万美元。尽管典型的 Web 开发人员可能不承担 NASDAQ 数据库的责任，但作为技术领域的服务提供商，我们的工作同样具有时间紧迫的性质。我们能够快速有效地做出响应，纠正客户遇到的问题，这一点至关重要。

在我最近参与的一个项目中，团队审查了许多跟踪和管理故障单的选项。根据经验，我们知道客户会遇到服务困难，他们需要能够联系我们寻求支持。

##### 项目要求

由于我们希望将票务管理器安装到现有的前端设计中，所以我们寻求一个在颜色和字体等方面易于配置的包。它必须足够直观，便于客户使用，并且足够强大，能够防止失误和门票丢失。

该团队查看了大量的软件应用程序和免费脚本，但找不到一个符合这些要求的。许多脚本过于复杂，执行了太多的任务，以至于完全忽略了要点，而另一些脚本是如此简单，以至于不值得多看一眼。其他的很完美，但是很贵——花费在 500 美元到 2500 美元之间……这是我们的预算无法承受的。最后，我们发现了解决方案。

##### IsolSoft 支持中心 1.5 版

IsolSoft 的支持中心是那些跃出页面的产品之一:在一瞬间，我们和软件之间有一种迷人的联系！好吧，这可能有点夸张，但我可以诚实地说，该产品似乎立即满足了我们提出的所有标准。

事实上，通过三层用户界面，支持中心超出了我们的要求。在可供客户使用的用户控制面板中，用户可以查看曾经提交的所有票证，并按打开的票证、关闭的票证进行排序，或者搜索给定问题的所有票证。

人员控制面板为支持技术人员提供了相当大的管理权限，使他们能够:

*   关闭分配给他们的票据，
*   留下评论和
*   获取票证被打开的日期和时间以及对它们执行的操作的列表。
*   工作人员也可以将门票重新分配给其他人。重新分配后，故障单将自动出现在新工作人员的故障单列表中。

但是最强大的用户级属于管理员，他拥有对软件的完全控制权。管理员可以配置公告板的样式表，包括页眉和页脚，这给了我们所需的灵活性。此外，电子邮件模板(用于标准客户电子邮件)很容易设置，尽管如果我们愿意，我们可以选择使用软件自带的默认模板(实际上相当不错)。

管理员还可以添加新员工，并能够查看所有票证和每个票证被分配到的员工。他或她还可以做出响应，并根据自己的判断干预票证流程。

##### 好人

在我看来，平衡成本和效果，这是市场上最好的产品。整个软件包是用 PHP 编写的，这是我们支持 Apache 的 Linux 服务器的首选。安装很容易——软件包中包含一个方便的浏览器可执行安装脚本。

一旦安装，该产品很容易集成到网站上。我们对服务进行了配置，以修改一些我们认为对我们的特定服务不安全的项目(例如新用户自行注册的能力——我们希望为每个用户分配一个与我们使用的其他软件相对应的 userid ),这种配置既快速又简单。

三层支持系统也使得在实践中跟踪和维护故障单变得极其容易。票证状态的每次更改都会导致向票证作者发送电子邮件，让他们了解票证的进度。

##### 坏事

我们发现支持中心的脚本还没有更新到可以使用 PHP 4.2，这引发了许多安全问题。到目前为止，我还不能确定该软件的补丁版本将于何时发布。然而，我们能够修改代码以在我们的安全环境中工作。

##### 总结

***评级:*** 4/5

***价格:***99.95 美元(包括升级和补丁以及 1 年免费支持)

***更多信息:***【http://www.isolsoft.com】T4

## 分享这篇文章