# propono:Ruby 的发布/订阅

> 原文：<https://www.sitepoint.com/propono-pubsub-ruby/>

## 构建面向服务的架构

“单轨”的概念在 Ruby 世界中由来已久——一个大型的 Rails 应用程序，它试图做所有的事情，但是很快就变得笨重和专横。发现自己处于这种情况的开发人员现在正在寻找更好的工作方式。

解决这个问题的一个方法是将一个 Rails 应用拆分成不同的部分，在面向服务的架构(SOA)中协同工作。通过将 web 请求和数据层功能留给 Rails 应用程序，并将更复杂、更简单的代码转移到单独的服务中，可以更容易地分离关注点，并为正确的工作选择正确的工具。在某些情况下，对于许多这样的工作来说，最好的工具可能是不同的语言或系统(例如，Erlang 用于分布式系统，Java 用于需要特定库的时候)，但是我发现，如果没有其他明显的选择，Ruby 通常是一种很好的默认语言。

去年，我在 Meducation 的团队决定将我们的“单轨铁路”拆分成一个面向服务的架构。在过去的 12 个月里，我们一直在进行拆分，现在有 21 个不同的应用程序可以协同工作。每个服务都有非常不同的关注点和知识。

我们有效地将[单一责任原则](https://en.wikipedia.org/wiki/Single_responsibility_principle)视为适用于应用程序，而不仅仅是类。创建新应用并将其集成到我们的生态系统中既快又便宜——事实上，我们创建和部署一个全功能集成应用的记录不到一个小时。实现这一点的关键是构建我们信任的健壮工具，这些工具使我们能够完全专注于应用程序的业务逻辑，而不是它如何组合在一起。这篇文章是关于第一个工具——Propono。

Propono(拉丁语动词 publish)是构建在 Amazon Web Services (AWS)之上的 pub/sub Ruby Gem。它提供了以下好处:

*   它的设置和使用非常简单。
*   一切都由 AWS 本机处理，这意味着:
    *   自动可伸缩性。
    *   卓越的可靠性。
    *   几乎零成本——我们每月发布几十万条消息，花费不到 5 美元。
    *   除了亚马逊没有第三方安全风险。

## 什么是 Pub/Sub？

我们将 Propono 描述为一块 pub/sub Ruby 宝石。让我们快速分析一下这是什么意思。

发布/订阅(publish/subscribe)是一个非常简单的概念。您有许多想要发送消息的应用程序(发布者)和许多想要接收消息的应用程序(订阅者)。通常，同一个应用程序会发布一些消息并订阅其他消息。消息发布到话题，订阅者听自己关心的话题。看…简单。

![](img/4ec257ee7303070210955da1cae357b1.png)

*图片来源:docs.oracle.com*

例如，在 Meducation，我们有一个名为“用户”的主题，许多应用程序都会订阅这个主题。当一个新用户注册时，网站会发布一个 JSON 消息到这个主题，实际上是说“一个用户已经注册了，现在有一个 id # 239372。”订阅的应用程序收到消息，然后做一些事情，如更新我们的搜索索引，生成朋友建议和发送欢迎电子邮件。

有了 Propono，只要你有一个 AWS 帐户，那么在网站上发布这个消息和在应用程序中订阅都是一件事。让我们深入一个基本的例子，然后看看 Propono 是如何与 AWS 一起工作的。

## 他们提议 101

要开始使用 Propono，你需要一个 AWS 帐户和一个拥有 SNS 和 SQS 权限的 IAM 用户。如果你只是在尝试 Propono，你可以用普通的 IAM 用户进行试验。然而，在生产中，我建议为特定用户设置一个允许访问 SQS 和社交网络的策略。例如

```
{ "Version": "2012-10-17", "Statement": [ { "Sid": "Stmt1401034357000", "Effect": "Allow", "Action": [ "sqs:*", "sns:*" ], "Resource": [ "*" ] } ] }
```

我们将创建两个可以相互对话的应用程序——一个发布者和一个订阅者。首先，创建一个名为 **learning_propono** 的目录，并添加一个 **Gemfile** ，如下所示:

```
source 'https://rubygems.org'
gem 'propono'
```

运行`bundle`，你应该得到一个 **Gemfile.lock** 。

我们现在准备创建我们的发布者和订阅者。让我们为他们分别创建一个文件: **publisher.rb** 和 **subscriber.rb** 。两者都需要使用您的 IAM 凭证来要求和配置 Propono。在您最喜欢的编辑器中打开它们，并用以下内容填充它们:

```
require 'propono'
Propono.config do |config|
  config.access_key       = "Your-IAM-Access-Key"
  config.secret_key       = "Your-IAM-Secret-Key"
  config.queue_region     = "eu-west-1"
  config.application_name = "Either-publisher-or-subscriber"
end
```

这就是你需要配置 Propono 的全部内容。在我们的应用程序中，我们倾向于将值放在一个 **propono.yml** 文件中，我们在部署期间(通过 Chef)注入该文件，并在运行时读取。

在 **subscriber.rb** 中，收听关于“用户”主题的消息。这很简单，只需执行以下操作:

```
Propono.listen_to_queue(:user) do |message|
  p "Message Received"
  p message
end
```

旋转控制台并运行`bundle exec ruby subscriber.rb`。您的订户将坐在`user`队列中等待消息。如果此时出现错误，请检查您的 IAM 凭据。

让它继续运行，在你的编辑器中返回到 **publisher.rb** ，发布一个关于新用户的消息到`user`主题。在 Propono 配置下，输入以下内容:

```
message = {entity: 'user', action: 'created', id: 123123} # Any string, hash, or array is valid here.
Propono.publish(:user, message, async: false)
```

默认情况下，消息在单独的线程中发布。因为我们想确保这个线程在主线程结束和应用程序终止之前完成，所以我们调用`async: false`在同一个线程中发布。

在订户仍在运行的情况下，在不同的控制台中运行`bundle exec ruby publisher.rb`。观察您的订户窗口，在一两秒钟内您应该会看到打印出来的消息。

现在，您可以轻松掌握 pub/sub 的所有功能。您可以从任何应用程序发布消息，也可以从任何其他应用程序订阅消息。简单。

## 到底是怎么回事？

在表面之下，Propono 利用亚马逊的简单通知服务(SNS)和简单队列服务(SQS)来存储和传播消息。我将简要介绍这两种服务，然后解释我们如何利用 Propono。

### 简单通知服务(SNS)

在出版方面，我们有社交网络。亚马逊将 SNS 描述为“一种快速、灵活、完全托管的推送消息服务……为了防止消息丢失，发布到亚马逊 SNS 的所有消息都被冗余地存储在多个可用性区域中……发送一百万个移动推送通知需要 1.00 美元……有了社交网络，你可以一次发布一条消息，一次或多次发送。”

用营销术语来说，SNS 是一种非常便宜、强大的服务，它接收消息并将其发送给一个或多个接收者。SNS 有许多可能的用途，但是在 Propono 的世界里，它提供了我们发布消息的主题。当我们发布一条消息时，我们将它发送到 SNS，然后 SNS 会寻找任何其他关心它的服务，并将消息传递下去。

### 简单队列服务(SQS)

另一端是我们的用户，他们使用 SQS 接收信息。亚马逊将 SQS 描述为“一种快速、可靠、可伸缩、完全托管的消息队列服务。您可以使用 SQS 以任何级别的吞吐量传输任何数量的数据，而不会丢失消息或要求其他服务始终可用。”

简而言之，SQS 队列接收消息并将它们保存在那里，直到服务将它们取走。成本也是最低的。在 Propono 中，您的应用程序正在订阅 SQS 队列，并在消息到达时提取消息。如果您的应用程序停止运行，消息将排队等待，直到它再次恢复运行。

### 消息生命周期

让我们从这一行开始探索信息之旅:

```
Propono.publish(:user, {foo: 'bar'})
```

这段代码运行时发生的第一件事是 Propono 在 SNS 上创建了`user`主题。执行完这段代码后，如果你查看你的 [SNS 控制台](https://eu-west-1.console.aws.amazon.com/sns/home)，你会看到`user`主题。在这个阶段，它没有订阅，所以实际上它会忽略发送给它的任何消息。

![](img/74b705fc3a9b9eb25a249991c65b03f4.png)

现在，让我们看看另一面

```
Propono.listen_to_queue(:user) do |message|
  # ...
end
```

代码检查 SNS 上是否存在`user`主题，如果不存在就创建它。但是，它还会在 SQS 端查找该应用程序的这些消息的队列是否存在。当您配置订户时，您选择了一个应用程序名称。该应用程序名称现在与主题名称连接在一起，为我们提供了队列名称。

例如，如果您调用了您的应用程序`subscriber`，执行上面的代码将创建一个名为`subscriber-user`的队列。看一下你的 [SQS 控制台](https://eu-west-1.console.aws.amazon.com/sqs/home)，你会发现这个话题已经被创建了:

![](img/3da5096af15fffaaeed6f4266b82869d.png)

回到你的 [SNS 控制台](https://eu-west-1.console.aws.amazon.com/sns/home)，点击用户主题，你会注意到在 SNS 主题和 SQS 队列之间已经创建了一个新的订阅。现在，发布到`user`主题的任何消息都将被发送到`subscriber-user`主题并存储起来，直到订阅者通过使用`listen_to_queue`读取它们。

![](img/ecfad5930ab7a8a0a7d4d8aac6753a8d.png)

所有这些都是免费设置的，安全策略和映射由 Propono 透明地处理。事实上，还有很多其他的事情发生。您可能已经注意到，有错误队列、损坏队列、慢主题和许多其他已经创建的零碎内容。我会涵盖所有这些，以及将来你可以使用 Propono 的许多其他方式。

我希望这是对 Propono 的一个有用的介绍，希望你能尝试一下。我们在生产中大量使用它，现在有很多其他公司也在这样做。我们希望听到您的反馈，并且非常喜欢您的贡献。请随时参与到 [Propono Github 知识库](https://github.com/meducation/propono)或下面的评论区。感谢阅读！

## 分享这篇文章