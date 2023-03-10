# 条纹 vs 布伦特里:终极指南

> 原文：<https://www.sitepoint.com/stripe-vs-braintree-the-ultimate-guide/>

![Stripe vs Braintree](img/9ea35ac5f861e145dc724cc5e9b4807d.png)

如果你正在开发一个需要接受支付的应用，你可能会考虑将 [Stripe](http://stripe.com) 和 [Braintree](http://braintreepayments.com) 作为支付提供商。

他们是新一代的全栈支付公司，对企业和开发者都很友好。它们处理诸如商家账户和 PCI 合规性等细节，并且集成起来相当简单。

一份不科学的评论暗示，Stripe 在开发人员中更受欢迎，在过去的一个月里有 99 个 StackOverflow 问题，而 Braintree 的有 34 个 T2 问题。Authorize.net，一个老的供应商，只有 [10](http://stackoverflow.com/questions/tagged/authorize.net) ，说明了向新支付公司的转变。

本文深入比较了 Stripe 和 Braintree，以帮助开发人员和企业做出明智的决策。我在生产中使用了这两个提供者。

## 贝宝

PayPal [在 2013 年收购了](https://www.braintreepayments.com/blog/braintree-teaming-up-with-paypal/) Braintree。从那以后，Braintree 使得在现代网络应用中接受 PayPal 变得比以往任何时候都容易。

如果您使用 Braintree 接受信用卡，那么接受 PayPal 就不需要额外的工作。这只是另一种支付方式，就像 Visa 或 MasterCard。无论是一次性交易还是订阅付费，Braintree 都能处理所有的细微差别。

接受 PayPal 对您的业务重要吗？如果是这样的话，布伦特里有一个强有力的理由。

Stripe 目前没有 PayPal 集成。

获胜者:布伦特里。它为大多数网站提供了最流畅的 PayPal 集成。

[![Braintree drop-in PayPal and credit card form](img/f6e1dca0206f4f43fa83148a5a3d5a69.png) *布伦特里的“顺带”PayPal 和信用卡形式*](https://twitter.com/braintree/status/491304131861041155)

## 支付表单，前端

Stripe 和 Braintree 提供预建的支付表格，您可以将这些表格带到您的网站上:

*   [条纹结账](https://stripe.com/checkout):一个<q>弹出</q>信用卡表单

*   [布伦特里插件 UI](https://www.braintreepayments.com/features/drop-in) :嵌入页面的信用卡表单

布伦特里嵌入式用户界面是我个人的偏好。它内嵌在你的用户界面中。它还可以包括一个贝宝按钮，让客户无需离开您的网站就可以登录贝宝。

除了预建的表单，Stripe 和 Braintree 都允许您创建自己的表单。

Braintree 要求您的服务器在显示支付表单之前，从 Braintree 的 API 中检索一个一次性客户端令牌，无论是使用其嵌入式 UI 还是托管字段(其自定义表单解决方案)。Stripe 不要求您的服务器在客户端提交支付表单之前检索一次性令牌，从而节省了一个步骤，并可能将加载时间缩短 1-2 秒。

获胜者:平局。Braintree 提供了一个更好的通用支付 UI，但需要一个额外的服务器请求，这可能会延迟加载 1-2 秒。

[![Braintree vs Stripe forms](img/286cb0da9e7a97e981190e583cce01d9.png) ](https://www.sitepoint.com/wp-content/uploads/2015/10/1445952285demos-1024x428.jpg) * [树状形态](https://www.braintreepayments.com/features/drop-in) vs [条纹形态](https://twitter.com/stripe/status/441309926372040704)*

## 支持

在周一的早些时候，我向 Stripe 和 Braintree 发送了以下问题:

> 你好，获取信用卡被拒原因的最佳方法是什么？在创建套餐时，以及在现有套餐期间卡被拒绝时，我可以获得此信息吗？

布伦特里在 6 小时内做出了回应。Stripe 在大约一天半后回复。两个答案都回答了这个问题，但布伦特里的反应要快得多。

这和我之前在两家公司的经历是一致的。布伦特里在工作日 6 小时内回复了我的请求，在周末 24 小时内回复。

布伦特里的主要支持是通过电子邮件([support@braintreepayments.com](mailto:support@braintreepayments.com))和电话。Stripe 的是通过[的网络表单](https://support.stripe.com/email)，没有电话支持。

两家公司都参与了 GitHub。他们的图书馆在那里是开源的，他们直接在问题页面上对每个问题做出回应。(例子:[条纹](https://github.com/stripe/stripe-ruby/issues?q=is%3Aissue+is%3Aclosed)，[布伦特里](https://github.com/braintree/braintree_ruby/issues?q=is%3Aissue+is%3Aclosed)。)

两家公司都在 Twitter 上参与，他们的反馈显示他们在短时间内直接回应开发者。(例子:[条纹](https://twitter.com/stripe/status/652185317286498304)，[布伦特里](https://twitter.com/braintree/status/471679454813908992)。)

Braintree 参与了 StackOverflow，这是一个流行的面向开发者的问答论坛。在 9 月 19 日布伦特里提出的四个问题中，布伦特里直接回答了两个。[stack overflow 上的条纹问题](http://stackoverflow.com/tags/stripe-payments)一般不会得到官方回应。

Stripe 有一个 [IRC](https://stripe.com/contact) 频道，这是一个重新下载你最喜欢的客户端的绝佳借口。你可能会也可能不会联系到团队中的某个人。

获胜者:布伦特里。两者都提供了很好的支持，但 Braintree 的更快、更可用。

## 折扣

![discounts](img/840a29814de252346eb6ed59b15bfd99.png)

Stripe 和 Braintree 都支持订阅折扣。例如，客户可能会从第一个月的订阅费中获得 5 美元。

每项服务的折扣大致相同。管理员在仪表板中创建一个折扣，来自您的应用程序的 API 调用可以应用该折扣。

但是，有一些差异会影响您实施折扣的方式，这些差异表明 Stripe 在这方面更强。

*   布伦特里不支持百分比折扣(如七五折)；条纹有。在 Braintree 中，如果您的月度、季度和年度计划有 25%的折扣，您需要为每个计划创建一个单独的折扣，每个折扣的金额不同。使用 Stripe，您可以创建一个适用于所有三种计划的 25%折扣优惠券。
*   Braintree 不支持优惠券的过期日期；条纹有。

因此，使用 Braintree，如果您的计划有不同的计费周期(例如每月和每季度)，或者您的折扣仅在有限的时间内可用，您将需要做更多的工作。

**获胜者:条纹。它的折扣提供了更多的功能，包括百分比折扣和有效期。**

## 升级和按比例分配

Stripe 和 Braintree 都能顺利处理共享相同计费周期的计划之间的升级。例如，如果有人需要从您的每月银牌计划升级到您的每月金牌计划，一个简单的 API 调用任一服务就可以完成。

然而，Braintree 无法处理从不同计费周期的套餐升级，比如从月套餐升级到季度套餐。你需要推出自己的升级逻辑([联系我](https://twitter.com/coreyitguy)获取提示)。另一方面，Stripe 处理这些复杂的升级，并应用按比例定价。

**获胜者:条纹。与 Braintree 不同，它处理不同计费周期的按比例分配。**

## 服务器集成

Stripe 和 Braintree 都为各种编程语言提供了库。一般来说，它们是开源的、有文档记录的和受支持的。

我已经集成了两个服务的 Ruby 库。它们完全足够了，允许你创建和更新订阅，执行一次性交易，并接收网页挂钩。

获胜者:平局。强大的开发者库是 Stripe 和 Braintree 的核心。

## 证明文件

您需要掌握您选择的服务的 API，而要做到这一点，您将非常依赖于文档。

Stripe 的文档比 Braintree 的更精确。在 Stripe 的文档中，我没有看到一个短语是含糊不清的，或者是可以解释的。布伦特里的，另一方面，可以更模糊一点。

例如，当 Braintree 试图向客户收取订阅费失败时，它可以通知你的应用程序(通过一个 webhook)。它传递的一部分是`failure_count`，它将其定义为<q>网关对过期订阅重试收费的次数。</q>

注意<q>重试这个词。</q>`failure_count`是包括第一次失败还是仅仅包括之后的重试？

我给 Braintree 发了电子邮件和推特，得到了相反的答案。 [Twitter 的回答](https://twitter.com/braintree/status/639889511892258817)被证明是正确的。

布伦特里已经修复了他们的[文档](https://developers.braintreepayments.com/ios+ruby/reference/response/subscription#failure_count)，将<q>已重试</q>改为<q>已尝试。</q>我对布伦特里的回应表示赞赏，并希望他们进一步收紧和澄清他们的文件。

也就是说，这两种服务都有按照编程语言组织的结构良好的文档——允许开发人员为他们的工具集获得特定的指导。不过，在从头开始实现了这两种服务之后，Stripe 的文档让我少了一些挠头。

**获胜者:条纹。它的文件在深度和清晰度上胜出。**

## 过期的信用卡

![expired credit card](img/e74f3dcc646716ab6b0deadaec8d3340.png)

Stripe 和 T2 都与信用卡公司达成协议，自动更新即将到期的信用卡。在许多情况下，获得替换卡的客户不需要更新他们的信用卡信息。这可能会减少客户流失，有助于你的业务。

使用 Braintree，你必须[请求这个功能](https://www.braintreepayments.com/blog/account-updater/)，这是一个快速且免费的过程。带条纹的，是自动的。

获胜者:平局。这两项服务都会自动更新过期的卡。记得向 Braintree 申请该功能。

## 试用期

Stripe 和 Braintree 允许订阅试用期，但只有 Stripe 允许客户在没有信用卡的情况下试用。

条带试用期不需要支付方式。如果试用期结束，客户还没有提供付款信息，Stripe 会登记拖欠付款。您可以使用 webhooks 友好地要求客户输入账单信息。超过您的(可自定义)重试限制后，Stripe 会取消客户的帐户。

另一方面，Braintree 甚至需要一个试用账户的支付信息。因此，如果你想在没有信用卡的情况下提供免费试用，并且你正在使用 Braintree，你将不得不编写比 Stripe 更多的代码。

**获胜者:条纹。它不需要试用期的支付方式，而 Braintree 需要。**

## Webhooks

你的支付处理器需要让你的应用程序知道某些事件。例如，如果一个帐户由于拖欠付款而被取消，您可能需要调整用户在您的应用程序中的访问权限，以便他们不再能够访问高级区域。

Stripe 和 Braintree 都有 webhooks，可以告诉你的应用程序某些事件。它们在大多数用途上非常相似。

Stripe 以其强大的 webhook 状态和日志与众不同，可从其仪表板中获得。例如，你可以看到哪些 webhooks 失败了。布伦特里不提供这个；只有他们的支持人员可以查看 webhook 日志。

**获胜者:条纹。与 Braintree 不同，它在仪表盘中显示 webhook 状态和日志。**

## 取消

![exit](img/a10b7e553387451cace6c8fcd5c89a79.png)

万一客户取消了他们的帐户，您可能希望他们在当前计费周期的剩余时间内保留访问权限。(毕竟他们付钱了！)

使用 Stripe，您可以在账单周期结束时取消账户。在周期结束时，Stripe 向你的应用程序发送一个`subscription_deleted` webhook，此时你的应用程序知道切断用户的访问。

有了布伦特里，所有取消立即生效。没有什么可以通知你的 app 本期结束。因此，你的应用程序必须记住在期限结束时切断用户的访问，在此之前会有一个奇怪的差异，即用户的 Braintree 订阅被取消，但他们仍然可以访问。

总体来说，我相信 Stripe 的取消逻辑更有道理。如果您使用 Braintree，并希望取消在计费期结束时生效，您需要进行一些额外的编码。请随时向我寻求建议。

**获胜者:条纹。它的取消流程对大多数网站来说更有意义。**

## 测试

如果你使用的是 Stripe 或 Braintree 这样的服务，商务可能对你的网站至关重要。您将希望自己测试每个服务，然后实现自动化测试，以确保它们在未来继续工作。

Stripe 和 Braintree 都提供了测试环境，拥有自己的仪表盘和测试 API 密钥，您可以将它们放入您的应用程序中。这允许你在开发中点击你的应用程序来创建虚假的订阅和收费，并在仪表板中查看它们。

您可以注册 Braintree 和 Stripe 的测试环境，而无需提供任何银行信息。此时，您可以在开发中使用该服务。在生产中使用该服务之前，您需要提交您的个人和银行信息以获得批准。一般来说，两种服务都会在几天内批准。

Stripe 和 Braintree 都可以进行自动测试。 [Braintree](https://developers.braintreepayments.com/ios+ruby/reference/general/testing) 比 [Stripe](https://stripe.com/docs/testing) 内置了更多的测试功能——比如假的网络挂钩和支付随机数(这可能会产生各种支付错误)。Stripe 提供了从它的仪表盘向你的应用程序发送假网页挂钩的能力。你可能需要用像 [stripe-ruby-mock](https://github.com/rebelidealist/stripe-ruby-mock) 这样的工具来补充 Stripe 的库，Braintree 的库很可能自己就能支持好的测试。

获胜者:平局。这两种服务都提供了低门槛的沙箱，以及自动化测试的助手。

## 仪表盘

作为 Stripe 或 Braintree 的帐户所有者，您可以访问一个仪表盘，在那里您可以查看客户、订阅和支付情况。这两项服务都提供了管理这些信息、退款和更改账户设置的能力。

获胜者:平局。这两种服务都在用户友好的仪表板中提供常见的管理任务。

## 总体印象

您的决定将取决于每个功能对您的重要程度。

Braintree 提供 PayPal 集成和更具响应性的支持。如果 PayPal 是你的商业案例的核心，Braintree 将使整合它变得容易得多，并可能成为你的选择。如果您预计会有许多文档中未涉及的问题，Braintree 的支持可能会赢得您的支持。

Stripe 拥有优秀的文档、试用期、折扣、webhooks 和升级。你不必像有时不得不使用 Braintree 那样绕过条纹。如果您的应用程序有无信用卡免费试用或升级到不同的计费周期，您可以选择 Stripe 以节省一些开发时间。如果你是一名经验丰富的开发人员，而 PayPal 不在商业案例中，你可能会倾向于 Stripe。

从这里，查看[条纹](https://stripe.com/docs)和[布伦特里](https://developers.braintreepayments.com/ios+ruby/)的快速入门指南。注册他们的沙箱，在 dev 中的应用程序上安装他们的库，感受他们不同的方法。令人欣慰的是，两家公司都处于支付的最前沿，但请注意它们的差异。

## 分享这篇文章