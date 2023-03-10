# 你想过云安全吗？

> 原文：<https://www.sitepoint.com/have-you-thought-about-cloud-security/>

# 云安全:简介

云，每个人都在争先恐后地加入，博客和论坛是一个热门话题，而且已经持续了一段时间。尽管我个人认为云计算不一定是新的。这是我们设计、部署和管理应用和计算服务的全新方式，值得兴奋。有了这么多令人兴奋的事情，云安全往往没有得到应有的重视。

考虑以下关于云的事实:
[最近一项由 Forbes.com](http://www2.lse.ac.uk/newsAndMedia/news/archives/2012/01/cloud.aspx)报道的由微软赞助的伦敦经济学院的研究显示，智能手机领域与云相关的工作岗位将增长 349%。
[到 2014 年，云计算服务的收入将在 1，187 亿美元到 1，488 亿美元之间，这取决于你相信哪个估计值](http://blogs.wsj.com/digits/2011/04/21/more-predictions-on-the-huge-growth-of-cloud-computing/)。
[IEEE spectrum 报告](http://spectrum.ieee.org/telecom/wireless/cloud-computing-drives-mobile-data-growth)预测到 2014 年将有近 10 亿人订阅移动云应用。

这些巨大的数字显示了人们对云计算的极大兴趣和兴奋。当我们将我们的信息存储在云供应商(如 Rackspace、谷歌、微软、戴尔、苹果、亚马逊和众多其他供应商)或作为一个企业时，潜在的风险很大。这涵盖了很多东西，包括我们的隐私和我们的客户。因此，我们的首要任务是考虑旨在使云计算与任何其他技术解决方案一样安全的安全计划和实践。我们做尽职调查很重要，这样我们就能为潜在的不利因素做好准备，并最大化有利因素。

在本文中，我们讨论并提出几个问题供您考虑。有些问题你可能已经想到并讨论过了，有些你可能没有。这些问题旨在帮助确保您已经很好地了解了含义、法律要求和其他云安全问题。

## 有哪些法律责任？

根据提供商的地理位置，提供商将遵守许多法律要求和责任。这些立法包括:
[萨班斯-奥克斯利法案](http://www.soxlaw.com/)。
[格拉姆-里奇-比利雷法案](http://www.ftc.gov/privacy/glbact/glbsub1.htm)。
[PCI(支付卡行业)合规](https://www.pcisecuritystandards.org/)。

此外，存储的数据类型可能会受到更进一步的法律约束，如 [HIPPA](http://www.hhs.gov/ocr/privacy/hipaa/understanding/summary/index.html) 。因此，在与提供商签订合同之前，您是否确认他们遵守了各自的法律？

您是否完全熟悉与您的业务和信息相关的法案？在对您的供应商和供应商解决方案进行尽职调查的过程中，您是否花时间考虑了这些因素？

## 如果提供商破产了会怎么样？

我不认为像苹果、谷歌和亚马逊这样的公司会破产。但近年来，全球金融危机及其影响也拖垮了老牌知名企业。如果需要，您应该考虑覆盖您的数据的计划和向不同供应商的迁移策略。您确定吗？您是否检查过您的数据恢复实践，以确保在发生这种情况时，您将获得服务连续性，并能继续遵守您声明的 SLA？

## 数据中心有多安全？

这个问题对于云计算来说并不新鲜，我们都在考虑这个问题。但它值得重复。您的提供商采取了哪些安全措施来保护数据中心和其中的信息？供应商是否有良好的入侵检测程序？网络级安全功能和策略是什么样的？

然后是人员问题。他们对员工有什么样的检查？数据是否受到保护，使员工无法将其带离现场？是否有适当的访问控制，以便只有适当授权的人员才能访问相应的数据？他们是否考虑过基于角色的、强制的或自主的访问控制？

有什么记录？当信息被访问时，是否有访问记录？能够从安全漏洞中恢复是一回事，但重要的是要知道发生了什么，由谁在何时发生，以帮助确保它永远不会再次发生。

员工是否经过适当培训？无论您是否有最好的安全措施，如果员工能够绕过它们，那么它们的有效性几乎为零。想想最近 Mat Honan 账户的苹果 iCloud 安全漏洞,它点燃了博客和 Twitter。发生在[亚马逊](http://www.telegraph.co.uk/technology/apple/9461218/Apple-and-Amazon-review-security-after-hack.html)或者 [GoDaddy](http://www.americanbanker.com/issues/176_186/godaddy-breach-details-1042485-1.html) 身上的类似安全漏洞呢？

## 您的 Web 应用程序有多安全？

考虑前面关于供应商安全性的问题当然很好，但是考虑离家更近的地方呢？在将应用程序迁移到云的过程中，我们有没有停下来考虑它们的安全性？众所周知，如果我们周围有一堵 12 英寸厚的墙，但让后门开着，如果它被滥用来获取我们的数据，我们很难抱怨。

[根据 2011 年安全周刊](http://www.securityweek.com/addressing-cloud-security-concerns-key-issues-and-recommendations)的一篇文章，超过 75%的攻击是通过 web 应用程序发生的。[根据另一篇 2011 年的文章](https://blog.cloudsecurityalliance.org/2011/01/27/moving-to-the-cloud-take-your-application-security-with-you/)，这一篇由云安全联盟:

<address>…only 18% of IT security budgets allocated to address the threat posed by insecure Web applications, while 43 percent of IT security budgets were allocated to network and host security.</address>

您的公司重视应用程序安全性吗？您是否投入了足够的预算来确保您的应用程序能够抵御各种可用的攻击媒介，例如跨站脚本(XSS)、跨站请求伪造(CSRF/XSRF)和 SQL 注入攻击？

PHP 安全联盟(PHP Security Consortium)为保护您的 web 应用程序提供了可靠的指导。您的开发团队重视安全性吗？如果您外包，您是否规定了安全性是所生产的应用程序中的一个必要条件？

# 结论

这些只是我们应该考虑的几个问题，我希望它们没有吓退您，但它有助于确保您牢记云安全。我真的相信云计算能够并且应该是一个很好的飞跃，并带来大量的好处，我们都可以从中受益。但我们必须尽职调查。您正在采取什么措施来确保您的业务在云中的安全性和连续性？

## 进一步阅读

如果您想了解更多关于云中安全性的影响，我提供了以下链接。
[Rackspace CEO 的云计算业务战略](http://www.businessweek.com/videos/2012-04-16/rackspace-ceos-cloud-computing-business-strategy)
[云计算:法律法规问题](http://technet.microsoft.com/en-us/magazine/hh994647.aspx)
[HIPAA 隐私规则概要](http://www.hhs.gov/ocr/privacy/hipaa/understanding/summary/index.html)
[向云转移？随身携带您的应用安全](https://blog.cloudsecurityalliance.org/2011/01/27/moving-to-the-cloud-take-your-application-security-with-you/)
[云计算推动移动数据增长](http://spectrum.ieee.org/telecom/wireless/cloud-computing-drives-mobile-data-growth)
[法律云:随心所欲](http://cloudsecurity.org/blog/2009/05/08/legal-cloud-have-it-your-way.html)
[云计算安全](http://en.wikipedia.org/wiki/Cloud_computing_security)

## 分享这篇文章