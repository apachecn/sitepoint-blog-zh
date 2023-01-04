# 查看信号 WordPress 托管报告摘要

> 原文：<https://www.sitepoint.com/review-signal-wordpress-hosting-report-summary/>

在网上，有很多公司可供选择。不幸的是，选择一个主机往往是一个反复试验的问题，因为很难区分优质的供应商和不合格的公司。甚至谷歌搜索虚拟主机评论也是没有用的，因为大多数评论网站都是由主机公司所有，并且充斥着附属链接。在其他情况下，许多主机品牌属于同一个母公司，很难逃脱由您当前的主机引起的问题。

幸运的是，Kevin Ohashi 发表了一篇广泛的文章，讨论了 WordPress 在各种主机上的托管基准。就评估公司而言，主要关注的是最佳性能和服务的一致性。来自每台主机的数据收集了一个多月，以确保数据的准确性。

![Review Signal WordPress Hosting Report](img/43a5c90142ac47f5af0252399571b704.png)

Review Signal 并不只是盲目地在托管账户上安装 WordPress，而是直接去找托管提供商，与他们合作，确保每个提供商都使用统一的设置，即使这需要定制安全规则或执行一般客户通常不需要的其他管理工作。

下面是从数据中提取的一些关键信息，而不是重复整个报告:

## 持续流量测试

### 落后者

第一批测试失败的主机(30 分钟内只有 100-1000 个并发用户)是 Bluehost、DreamHost 和 Cloudways(都在 Digital Ocean 和 AmazonAWS 上)。根据测试，DreamHost 几乎立即失败，原因是由于测试脚本触发了安全机制，而 DreamHost 不愿意帮助解决问题。

此外，值得注意的是，Cloudways 是一个覆盖层，有助于简化 AWS 和数字海洋实例的管理。对于任何不受管理的云服务，每当您添加覆盖层来简化操作时，都会影响性能。最终，主机服务的好坏取决于它最薄弱的环节，这使得寻找提供尽可能精简的堆栈的提供商变得非常重要。

### 获胜者

在 30 分钟内 500-2000 个并发用户的持续负载的第二轮测试中，获胜者是:Kinsta、Pantheon 和 Media Temple。最近收购了 Media Temple 的 GoDaddy 也在测试结果中名列前茅。

## 浪涌测试

### 落后者

第一批在超过 60 秒的 1-1000 并发用户测试中陷入困境的提供商是 Bluehost、DreamHost 和 Nexcess。对于 DreamHost，这些问题可能是由主机的安全限制触发的，这意味着在现实环境中，DreamHost 可能更好地支持正常流量。然而，Nexcess 只运行了 30 秒钟就瘫痪了。

虽然不一定是个大问题，但 Cloudways 的错误/超时率在 0.5%-2%之间。尽管他们在这一轮中坚持了下来，但如果你正在寻找为任务关键型环境构建的主机，这可能是一个重要的症结。

在第二轮测试中，12000 名并发用户的时间超过 60 秒，Cloudways 完全失败，Bluehost、DreamHost、Nexcess 和 PressLabs 也是如此。

### 获胜者

在浪涌测试开始时，1-1000 并发用户超过 60 秒，获胜者是小橙，飞轮，GoDaddy，Kinsta，LightningBase，MediaTemple，Pagely，Pantheon，SiteGround，WebSynthesis，它们在这一轮中都有接近完美的结果。

在 60 秒内涉及 1-2000 个并发用户的测试中，获胜者是:小橙、飞轮、GoDaddy、Kinsta、LightningBase、MediaTemple、Pagely、Pantheon 和 WebSynthesis。这些主机的错误率都在 0.5%左右或更低。

## 正常运行时间测量

在 Review Signal 的测试中，没有一台被测主机的正常运行时间低于 99.5%。除了 MediaTemple 和 Bluehost，所有主机的正常运行时间都超过了 99.9%。客观地说，99.5%的正常运行时间是每月 3.5 小时的停机时间。99.9%每月停机时间少于 45 分钟。

尽管在评测信号测试中没有提到这一点，但是在评估一个 web 主机时，你不应该只从表面上看他们的服务水平协议(SLA)保证。这是因为东道主很容易捏造数字，做出不切实际的承诺。主机通常保证正常运行时间，但通常这些担保的细则使得客户几乎不可能索赔。

## 选择合适的主机

正如生活中的大多数事情一样，在选择网站主机时，没有正确或错误的答案。由于网页开发是一个如此广阔的领域，找到一个主机来满足您的需求只能通过考虑多种因素。如果你正在寻找做出更好决定的方法， [SitePoint 有一本入门书](https://www.sitepoint.com/how-to-choose-your-web-hosting-provider/)，告诉你在选择托管公司时应该牢记的注意事项。

## 分享这篇文章