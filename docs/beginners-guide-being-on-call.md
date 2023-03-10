# 随叫随到初学者指南

> 原文：<https://www.sitepoint.com/beginners-guide-being-on-call/>

*本文由[page duty](http://www.pagerduty.com/)赞助。感谢您对使 SitePoint 成为可能的赞助商的支持。*

DevOps 这个词是两个词的组合:开发和操作，它是敏捷系统管理中使用的一个相对较新的术语。

过去，开发人员会构建产品、服务和基础设施，然后维护它们的责任会转移到系统管理员身上。

相反，DevOps 强调开发人员和 IT 系统操作人员之间的沟通和协作，将人们更好地融入软件工作流程——从开发到生产——与敏捷价值观和原则保持更紧密的联系。

当您提供 SaaS 时，客户期望全天候正常运行。当你的营销团队把你的产品作为一个不可或缺的软件来销售时，你的客户会不知道当它出问题时该怎么办。采用 DevOps 方法意味着每个人都致力于编写好的代码并保持正常运行时间。

## 待命时保持头脑清醒

无论什么样的团队结构，让系统一直保持运行不可避免地意味着让您的 DevOps 团队随叫随到。问题不会按照时间表出现，所以你需要一个随时准备处理它们的团队。

对于一个新的团队成员来说，随叫随到可能会令人生畏。白天，当你血液中有咖啡因，音响里有音乐，有可以召唤的团队成员时，解决问题和解决问题是很容易的。

但当你在凌晨 2 点被短信吵醒，需要迅速采取行动解决一个每秒钟都可能让你的公司损失很多钱的问题时，情况就有点不同了。

以下是我多年来在 SitePoint 和其他组织待命时获得的一些技巧。

### 谁负责？

一般来说，我们确保每个待命的人都知道同一个名单上的人。一旦警报发出，第一个做出响应的人的任务是直接解决问题，而其他人可以通过提供建议和信息来帮助解决问题。

这种方法避免了多个开发人员都试图修复同一个问题，无意中使问题变得更糟，或者产生新问题的情况。这可以是一个默认的协议，就像我们所做的那样，或者有人可以在那个时候带头。无论哪种方式，有一个人“在点上”保持事情清晰，并防止进一步的问题。

其他隐含的线索可以帮助您的开发团队了解情况。我只会在办公时间之外登录我们的聊天客户端来解决问题，所以如果有人看到我在线，他们会知道我正在忙着解决问题，并可以提供帮助。

知道如果我不尽快解决问题，其他人会被第二个或第三个警报吵醒，这也是一个强大的动力。

培养 DevOps 的协作文化也很重要。如果有人正在控制一个修复，但超出了他们的能力范围，并感到有压力，重要的是他们知道他们可以向他人寻求帮助，而不会因为修复缓慢而被操练。

### 时刻做好准备

当我随叫随到时，我几乎到哪里都带着我的笔记本电脑。例外的情况是，当我知道我要进行一次短暂的旅行，并且访问为工作设置的机器只需要几分钟的时间。我现在已经挺习惯了，也不妨碍我。

一旦你养成了确保笔记本电脑总是在身边的习惯，你甚至不会想到它。最令人沮丧的是，如果我想去游泳池，在那里我可能听不到手机的提醒，或者去跑步。如果是后者，我仍然可以这样做，但我必须在离家近的地方跑几圈，而不是远离电脑的长距离跑。

这听起来可能是显而易见的，但是要确保你的机器能够处理你要求它完成的任务。它有您需要的凭证和证书吗？它是否具有蓝牙连接功能以支持网络共享？如果你的待命机器不是你经常使用的，确保它是最新的，并定期测试。

### 定制您的环境

在问题出现之前，确保你的房子随时待命。你需要一个稳定的物理环境，以确保你在睡眠后感到压力和困惑时能够在黑暗中导航。

我有几个晚上熬夜到很晚，上床睡觉 20 分钟后才被短信吵醒。那是最糟糕的时候。

当我在一片阴霾中醒来，几乎不能睁开眼睛或站立时，至少我会知道我的手机在哪里，我的电脑在哪里，我可以不用想就能到达那里。从那里我可以着手解决出现的任何问题。

另一个提示:为你的桌面壁纸选择深色背景，并调低亮度——或者使用类似于 [Redshift](http://jonls.dk/redshift/) 的应用程序——以避免在你登录修复问题时被蒙住眼睛。

### 防胜于治

在我受雇于 SitePoint 的早期，当我第一次接管系统管理员的职责时，几乎每天早上我都会被闹钟吵醒。最后我彻底崩溃了。这是解决问题的巨大动力。

在解决了潜在的问题之后，现在我可能每个月都会在工作时间之外收到一次警报。

如今，我们的基础设施是这样的，当每个人都在办公室时，当开发人员推出新代码时，问题更有可能出现。

改善您的基础设施，并在实际问题之外对其进行测试，这意味着您可以对您的系统充满信心，并知道在出现问题时该做什么。

### 了解基础设施

了解组织的依赖关系以及它们之间的关系有助于您快速了解问题的根本原因。很多时候，我能够更快地解决问题，因为我知道系统是如何布置的，所以我知道先检查什么。当然，最难的问题是那些你意想不到的问题。

与此相关:确保你的文档是最新的，并涵盖所有的基础。糟糕的文档会阻碍而不是帮助。

### 合适的工具

当然，当你有工具不碍事并帮助你专注于重要的事情时，在待命时保持清醒就更容易了。

PagerDuty 是一个运营绩效平台，旨在为您提供基础设施的单一视图，这意味着事件和事故可以由分布在世界各地的团队来处理，每个人都知道出现的问题。

作为企业计划的一部分，该服务提供详细的分析，衡量团队和系统在事件响应方面的表现，从而进一步提高了这种态势感知水平。有了这些工具，您可以帮助团队缩短确认事件的平均时间以及解决事件的平均时间。

在事故期间，警报通过电子邮件、电话、推送通知或通过与其他服务的集成发出。该服务具有连续路由和自动升级功能，以确保每个警报都得到应有的关注。

PagerDuty 与 AppDynamics、Crashlytics、New Relic 和 Sensu 等服务进行了 100 多次集成。但是，如果您使用的服务不在列表中，PagerDuty API 可以与任何可以进行 HTTP API 调用或发送电子邮件的系统一起工作。

当谈到让随叫随到变得更容易时，PagerDuty 有丰富的日程安排选项，为全球团队提供全天候日程安排，这意味着给定位置的每个团队成员都可以在工作时间工作(再也不用在凌晨 2 点醒来了！).如果第一个人没有响应，第二待命名单也可以自动升级事件(这种情况会发生！).

该服务还智能地避免了“狼来了”的警报，为你负责的服务中的每个事件发送一个警报，并且只在该事件需要紧急行动时发送。如果多个服务同时生成警报，
PagerDuty 会将警报捆绑在一起并通知您一次(您仍然可以单独查看每个警报)。

一旦您解决了一个事件并喘过气来，您就可以深入了解该服务的详细事件时间表出了什么问题，然后寻找其分析服务的根本原因或趋势。

## 结论

对于 DevOps 团队的新成员来说，随叫随到可能是一种令人畏惧的经历。但是，通过正确的方法、协作文化、基础架构知识和正确的工具，凌晨 2 点的叫醒服务是可以管理的，并且您可以在不失去太多睡眠或理智的情况下解决问题。

你是如何做到随叫随到的？你有什么建议吗？你试过传呼机吗？请在下面的评论中告诉我们。

## 分享这篇文章