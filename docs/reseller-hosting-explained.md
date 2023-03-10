# 经销商托管解释

> 原文：<https://www.sitepoint.com/reseller-hosting-explained/>

I’ve seen the following situation arise again and again during the past several years:

1.  一个优秀的网页设计师或开发者会建立一个客户组合，为他们提供良好的推荐业务
2.  设计师每完成一个项目，都会推荐一家自己喜欢的虚拟主机公司给客户使用。
3.  客户接受建议，每月支付 10-50 美元的虚拟主机费用。

这个场景没有任何问题。客户很高兴，网页设计师开始着手下一个设计项目。然而，许多设计师还没有意识到的是，他们可以通过提供自己的网站来从同一个客户那里每月获得经常性收入。事实上，一旦一个受欢迎的设计师积累了一个中等规模的客户群，他们每月收入中的网站托管部分很容易超过设计收入。虚拟主机收入最好的部分是客户每月都需要这项服务——不管未来的设计需求如何。

那么，一个网页设计师如何轻松地建立一个系统来托管客户网站，而不需要自己建立一个复杂的网站托管操作呢？通过经销商托管。

这篇文章将帮助您了解向您的设计和开发客户转售虚拟主机服务所带来的机会。你做的每一个网站项目都需要托管。作为被雇佣来创建网站愿景和技术实现的专家，你处于一个独特的位置来建议你的客户选择虚拟主机解决方案。通过提供管理他们网站业务的这一方面，你将获得额外的月收入，并被视为客户网站战略中更重要的一部分。在准备这篇文章时，我们找到了经销商托管方面的专家。像微软、cPanel、MaximumASP、NaviSite、DedicatedNOW、HostGator 和 34SP.com 这样的公司都提供了他们在转售主机时如何成功的观点。

以下内容可以作为那些考虑进入市场转售虚拟主机服务的人的指南。我们将首先向您介绍经销商托管背后的技术概念，然后提供专业人士对如何选择托管合作伙伴来处理您的经销商业务的见解。

## 经销商托管如何运作

许多大型虚拟主机公司现在提供一种特殊的账户类型——通常称为“经销商”计划。该帐户是专门设置的，以便帐户持有人可以为一个帐户支付固定的月费，然后在其下托管多个子帐户，由帐户持有人管理和计费。这里有一个例子:假设你以每月 30 美元的固定费用购买了一个经销商账户。我们还可以说，你目前有十个客户愿意付钱让你托管他们的网站。如果你收费，这是你每月收入的预测:

*   每月 5 美元:10 个客户 x 5 美元=每月 50 美元–经销商账户 30 美元=每月 20 美元利润
*   每月 10 美元:10 个客户 x 10 美元=每月 100 美元-经销商帐户每月 30 美元= 70 美元利润
*   每月 25 美元:10 个客户 x 每月 25 美元= 250 美元-经销商帐户每月 30 美元= 220 美元利润

如果你有 100 个客户，而不是只有 10 个，这些利润数字会变得更有吸引力(当然，那时你可能需要在托管计划上花更多的钱，这取决于你客户的流量)。

所以，让我们说，你已经决定转售托管给你的客户。让我们首先从技术角度来理解什么是经销商托管；然后，我们将帮助您选择最佳提供商。

## 了解经销商托管

让我们从技术角度出发，对经销商托管进行一个明确的定义。虽然在许多方面，经销商托管计划类似于普通的共享托管服务，但了解它如何允许您在一个主帐户下托管多个子帐户的技术基础可能会有所帮助。接下来的信息是由汤姆·希尔提供的，他是经销商 host[34SP.com](http://www.34sp.com/reseller-hosting)的服务器工程师。

经销商托管计划的基本组成部分包括:

Web server and database components

The web server is the software that resides upon the server hardware, listening for HTTP and HTTPS connections. The most common web servers are Apache and Microsoft’s Internet Information Services (IIS). Multiple websites are served by a single IP address, thanks to what’s called *name-based virtual hosting*, which uses the content of the HTTP headers to distinguish between requests to the same IP address for different domains. This allows resellers to easily and cheaply create multiple websites without paying for extra IP addresses or servers.

数据库服务器是在服务器硬件上提供数据库功能的软件。它允许将数据存储在一种结构化的方法中，这种方法能够使用查询语言(如流行的结构化查询语言(SQL ))快速检索数据。常见的数据库服务器是开源的 MySQL 和微软的 SQL Server。通常，一个数据库服务器实例可以提供多个数据库，每个数据库都有自己的用户。这允许经销商为每个客户端创建多个数据库或数据库用户，提供对数据的独立控制和访问。

Mail server with SMTP/IMAP/POP3

Email servers are tasked with two of the major functions provided for reseller clients: the sending and receiving of email. SMTP (typically used for sending email) is often provided by software like Qmail, Exim, and Postfix, in addition to many others. IMAP and POP are two protocols used for receiving email; both have their pros and cons, and both should be offered by any reseller. Typically, these services are provided by programs like Courier and Dovecot.

特别值得注意的是，一体化服务(如 Microsoft Exchange 和 Zimbra)通常被吹捧为更高层的企业级电子邮件平台，包含所有功能，以及(有时是专有的)邮箱同步、日历和日程安排功能，以及联系人管理。此类产品对于所有级别的经销商主机来说并不重要，但通常是较大的客户所需要的。

DNS server with full zone management

Integral to the Domain Name System is a DNS server, which provides name server and domain name server duties to the reseller and their clients. Often this will involve the creation of branded name server addresses for which to use with domain names. Management of DNS zone files (a list of DNS records for each domain) is paramount to any reseller. It allows them to create subdomains, redirect traffic for other subdomains (say to services hosted at an office), or create new entry points to the same website for marketing purposes. By far the most common DNS server software in use today is BIND, an open source project.

Brand-agnostic servers, to enable white-labeling

Often a reseller would prefer that clients were unaware of the upstream hosting provider, so that they appear to be the provider, rather than simply a reseller. *White-labeling* covers a number of activities, such as company-branded name server addresses, for pointing to your name servers. Also, if clients are given limited access to a control panel (that is, Plesk, cPanel), the clients’ company logos and theme colors could be used. Often the upstream provider will supply separate mail server addresses purely for reseller use—the domain itself would have no relation to the provider company’s branding.

布伦特·奥克斯利是知名主机经销商 [HostGator](http://www.hostgator.com/) 的首席执行官。以下是他如何描述一个主机经销商托管账户的主要组成部分:“一个经销商托管账户的主要组成部分是 WHM(网络主机经理)。它允许经销商创建和修改帐户，更改设置，设置自定义品牌，等等。在 WHM 的下面是一个标准的控制面板(我们使用 cPanel ),允许个人网站添加和删除电子邮件帐户，上传和管理文件，等等。HostGator 经销商帐户还包括各种不错的附加功能，如经销商域名的 Enom 帐户，访问 4，500 个免费网站模板，以及免费的计费系统。”

考虑到经销商托管账户中所有的技术基础，你还应该在提供商中寻找什么呢？以下是奥克斯利的建议:“我会考虑 1)如果子帐户具有您和您自己的客户需要的功能，2)主机提供的额外服务对您有价值和有用，3)主机的总体正常运行时间和可靠性良好(至少 99.5%或更高)。在寻找经销商托管公司时，声誉和寿命就是一切。”

乔希·尤因是成立于 1997 年的托管公司 [DedicatedNOW](http://www.dedicatednow.com/) 的营销总监。该公司为成千上万的客户提供托管和非托管的专用虚拟主机服务。这些客户中有许多是托管经销商。尤因补充了他对建立经销商托管账户时最重要的技术考虑的想法。“确保您了解如何全天候支持您的客户。(重复说 3 遍)。如果你习惯于建立自己的网站，剩下的就很容易了。那东西是可以学的。成功的经销商知道如何建立一个流程来支持他们现有的客户并带来新的销售。掌握这两样东西，你就一定会赢。”

## 选择最佳经销商虚拟主机

研究将是选择你能找到的最好的经销商网站主机的关键。无论是同行设计师的口头推荐，还是搜索引擎或论坛中的在线搜索，你都需要进行一些扎实的研究，以做出最佳决定。这里有几个关于如何研究一个好的经销商主机的建议。

### 这方面的专家

当我自己的知识不足时，我喜欢求助于专家。以下是几个专家如何着手研究一个经销商网站主机。

Monish Sood 是微软全球主机业务的营销经理，并为 [Hosting Insights 博客](http://blogs.technet.com/hosting/)供稿。他说，“口口相传是吸引和留住客户的最有力的方式。说到服务，声誉就是一切。当我在一家代理主机公司工作时，我们的第一大新客户来源是通过客户推荐。我们经常对客户进行调查，以评估他们的满意度，并问了一个非常重要的问题:你会把我们推荐给朋友吗？在微软，我们使用客户推荐在我们的[主机目录](http://www.microsoft.com/hosting/catalogs/smallbusinessmarketplace.mspx)中突出主机合作伙伴。作为微软合作伙伴网络的一部分，托管服务提供商可以对他们的客户进行总体满意度调查。这些结果将用于帮助合作伙伴获得积分，以获得托管解决方案能力认证，这将提供许多巨大的好处，例如被列入目录，这是一个很好的销售线索来源。”

“任何好的解决方案都是从定义自己的需求开始的，”cPanel，Inc. 的运营副总裁 Aaron Phillips 说，“开发人员和设计人员有许多棘手的问题要问自己，一旦目标得到满足，他们就可以寻求一个伟大的解决方案。支持级别、功能和经销商托管公司都因单个开发人员的特定需求而异，拥有某种定义的需求列表会有所帮助。最后，当你开发了需求，如果你有一个特定的控制面板或你喜欢的软件包，你可以直接到源(控制面板公司网站)开始搜索。联盟营销稀释了搜索引擎上的好信息。菲利普斯先生补充说，“在主机行业，有很多很好的选择，找到最符合你需求的那一个，值得你付出每一分努力。”

NaviSite 的 William Toll 建议，“一个网络专业人士应该从他们自己的网络开始:四处打听，听听关于不同主机选择的好消息和坏消息。然后，他们会很快建立一个清单，列出需要寻找和留意的内容。先找一个注重可靠性和安全性的托管提供商；如果这是他们的重点，网络专业人士可以放心，他们的客户会得到很好的照顾。一旦供应商的短名单最终确定，可以向他们寻求参考，最好是另一家网络专业公司。任何有信誉的主机提供商都会很乐意提供这样的参考。此外，如果该公司是一家微软商店，他们应该考虑 WebsiteSpark 计划，该计划为网络 VAP 提供免费软件和工具。”

斯泰西·格里戈斯是以微软为中心的专门服务器提供商 MaximumASP 的销售副总裁。他补充了自己的想法:“到目前为止，选择托管公司的最佳方式是与你个人认识和信任的其他设计师/开发者交谈。此外，你应该寻找一家在托管你的特定技术方面是专家的公司；例如，在 MaximumASP，我们只托管微软技术。验证经销商托管公司的声明，也。如果公司说他们有很棒的 24x7x365 服务，午夜打电话给他们；说你是一个潜在客户，想看看他们的支持有多好。此外，我会要求该公司提供一份 SAS70 报告的副本。SAS70 报告由第三方审计员准备，并验证托管公司是否遵循记录的程序。大多数质量较好的公司都会进行年度 SAS70 审计，并乐意向客户提供审计结果的副本。

[34SP.com](http://www.34sp.com/reseller-hosting)是一家位于英国曼彻斯特的转售主机提供商。斯图尔特·梅尔林在九年前共同创立了这家公司，他给出了这样的建议:“声誉、服务和支持是经销商托管合作伙伴取得成功的关键因素。把你的选择范围缩小到少数几个，然后尽可能地研究它们。如果可以的话，找其他用过该服务的人，看看他们是怎么想的。当然，你可以在搜索引擎上查询，也可以在 sitepoint.com 这样的设计师和开发者网站上查询。此外，不要忘记论坛— [在 SitePoint 也有一个致力于专用服务器和经销商托管的论坛](https://www.sitepoint.com/forums/forumdisplay.php?f=183)。这是一个很好的资源。”

### 行业网站或论坛

另一个很好的研究来源是查看特定于主机行业的论坛。此外，还有一些专业网站发布托管公司的新闻和评论。论坛很受欢迎，因为它们为客户提供了一个机会来评估经销商托管公司，并与他人分享第一手信息。这样，论坛就成了一种扩展的口碑传播渠道。

如上所述，sitepoint.com 的[论坛非常广泛，涵盖了这个主题的许多方面。经销商托管论坛研究还有另一个备受推崇的资源:【webhostingtalk.com](/community/)。该论坛专门讨论虚拟主机主题，并包含一个专门针对经销商虚拟主机的论坛子部分。

### 推特

你可能想知道 Twitter 和传统搜索引擎的研究有什么不同。不同之处在于你所研究的网络主机的真实用户的实时、未经审查的反馈。您可以了解特定 web 主机的最新情况。如果有反复出现的问题，它们会在 Twitter 搜索中表现出来。使用 Twitter 实时搜索的最有效方式是在几天甚至几周的时间里，因为它给你提供了评估表现的最佳机会。方法如下。将潜在经销商主机的数量减少到几个。然后去 Twitter.com，输入搜索网站的主机名称。浏览搜索结果，寻找提到网站主持人的推文，评估作者的满意度。请记住，任何时候，所有企业都可能有一两个不满意的客户，问题是不可避免的。这就是为什么在几周内进行搜索是最好的。如果一家公司在某一天出现了问题，你会在几天后回来时发现情况已经好多了——否则，这可能是一个危险信号。

## 结论

我希望我已经说服你调查经销商托管，如果你是一个网络专业人士；否则你可能会错过一个额外收入的重要机会。更重要的是，通过为你的客户提供虚拟主机，你有助于维持关系。我在这里提供的工具和信息应该可以帮助你为你的经销商主机选择一个合适的提供商。

主机行业专业人士的共识是进行彻底的研究，并依靠其他使用过各种网络主机的开发人员和设计人员的个人建议。你还应该记得使用搜索引擎、论坛和 Twitter 来支持你的研究工作。

## 分享这篇文章