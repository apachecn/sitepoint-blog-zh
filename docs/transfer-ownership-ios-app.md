# 如何转让 iOS 应用程序的所有权

> 原文：<https://www.sitepoint.com/transfer-ownership-ios-app/>

2013 年 6 月，苹果让开发者转让 [iOS 应用](https://www.sitepoint.com/series/ios-application-development/)的所有权成为可能。这对开发者来说是个好消息，因为这使得在像 [Flippa](https://flippa.com/sell-apps) 这样的市场上销售你的应用程序变得更加容易，甚至可以购买一个已经建立起来的改进应用程序。

转移应用程序涉及两个步骤:

1.  在苹果内部转让所有权
2.  将代码转移到苹果之外

让我们从苹果内部的所有权转移开始。

## 在苹果内部转让所有权

我假设你已经和另一方达成了协议，并且你很乐意完成转移。转让方必须同意主协议，并且您的开发者帐户不得处于迁移过程中。

只有拥有 Apple ID 和团队 ID 的团队代理可以启动和完成转移。如果你是这样描述的，那么访问你的 iTunes Connect 面板，然后按下 Transfer App 按钮将你的请求发送到 Apple。在此阶段，如果您的应用程序符合转移要求，您将会收到通知。目前，以下标准适用:

### 可转移状态

你的应用需要处于可转移状态。Apple 认为以下状态是可转移的:

*   待定合同
*   准备上传
*   开发商停止销售
*   无效的二进制
*   开发商拒绝
*   拒绝

您的应用内购买也必须处于特定状态。Apple 支持以下状态:

*   被认可的
*   准备提交
*   开发商停止销售
*   拒绝

### 其他重要限制

您不能转移订阅运行的应用程序，如杂志应用程序。这包括为应用内购买设置了自动续订订阅、免费订阅和非续订订阅的应用。使用 iCloud 和 Passbook 授权的应用程序也不能被传输。

你的应用程序符合标准吗？如果是这样的话，你就可以办理转学了。你需要查看 iTunes Connect 开发人员指南来了解最新的指南。

### 转移时间

当您发送转移请求后，收件人有 60 天的时间登录 iTunes Connect 并接受提议。一旦被接受，苹果需要大约两个工作日来完成转移。在此期间之后，该应用将不再出现在转让方的帐户中。

这款应用的用户不会受到转移的影响。他们仍然可以像往常一样下载应用程序，并查看评分和评论。应用程序的图表位置也将保留。

恭喜你！转移的第一部分现已完成。对于第二部分，您需要与新的所有者及其团队共享代码，以便他们可以继续构建和维护应用程序。

## 传送代码

但是如何让新的所有者访问代码呢？最流行的选择是转移一个 [GitHub 库](https://www.sitepoint.com/the-designers-guide-to-git-or-how-i-learned-to-stop-worrying-and-love-the-repository/)。这允许您安全地与新所有者共享您的代码。由于该流程是由您发起的，因此没有等待或批准时间。

要移动 GitHub 存储库，您需要成为该帐户的管理员。如果您已经是管理员，请执行以下步骤:

1.  登录到您的 GitHub 存储库页面。
2.  在存储库操作栏中选择“设置”。
3.  点击“转移”。
4.  请仔细阅读警告并留意它们。输入存储库名称以确认您已经完成了此操作。
5.  输入新主人的 GitHub 用户名，点击“我明白，转让此回购”。

### GitHub 存储库要求

Github 传输有问题吗？你的 GitHub 库只有满足一定的要求才能被移动。以下是一些最常见的问题:

*   您的目标帐户在同一个网络中是否有同名的存储库或分支？记得先改。
*   此存储库是私有的吗？确保目标帐户有一个付费帐户，并且至少有一个可用的私有存储库。
*   你是想转让私叉吗？只能转移私有根存储库。

如果你遵循上面的要求，你应该能够直接转移你的 GitHub 库。一旦你把所有权和代码交给了新的所有者，你就正式转让了你的第一个 iOS 应用。

## 最后…

正在创建和销售的 iOS 应用数量正在急剧增加，随着苹果公司简单的转移过程，这一数字可能会增长更多。应用程序仍然是宝贵的商业资产，但现在它们终于可以转手了。

您最近是否转移过任何应用程序，或者您是否计划在未来这样做？请在评论中告诉我们你的想法。

## 分享这篇文章