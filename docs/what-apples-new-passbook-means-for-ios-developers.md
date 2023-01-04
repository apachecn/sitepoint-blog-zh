# 苹果的新 Passbook 对 iOS 开发者意味着什么

> 原文：<https://www.sitepoint.com/what-apples-new-passbook-means-for-ios-developers/>

Passbook 是每次 iOS6 安装都会附带的一款新应用。它基本上是一个智能的“虚拟钱包”，可以组织你的许多会员资格、礼品卡、奖励计划和数字购买。Passbook 不仅可以让用户通过丢弃实体卡和通行证来瘦身钱包；它还根据用户的位置和偏好提供及时的更新。Passbook 可以跟踪活动门票、旅游门票、优惠券以及其他促销活动。一些商业分析师将 Passbook 视为提升 iOS 操作系统人气的一条途径。iOS 过去只方便购买纯数字商品，如应用程序、音乐和视频；现在，有了 iOS6 中的 Passbook，苹果设备在购买更多有形商品时也可以一样得心应手。

对于开发者来说，苹果提供了 Passbook 工具，如“Pass Kit”web 服务和“Pass Kit”API，使第三方应用程序能够利用 Passbook 用户数据。这些开发工具不仅有利于那些希望将现有奖励计划整合到 Passbook 中的大型老牌公司；它们还为小型开发人员提供了利用 Passbook 技术实现自己目的的机会。以下是在 2012 年 WWDC 展会上展示的 Passbook 的三个关键属性:

*   **动态** —存折是动态的；用户可以自动得到航班延误、登机口变更、新优惠券和其他更新的通知。
*   **地理和时间感知** —如果您在某个您感兴趣的位置附近，Passbook 将根据您存储的 Passbook 信息从您的 GPS 坐标中收集信息。如果你有某种可兑换的物品，并且你就在兑现促销的地点附近，Passbook 可以向用户发送推送通知。
*   **个性化** —用户可以删除“通行证”，他们将不再收到相关更新。因此，尽管 Passbook 听起来很有帮助和方便，但营销人员和开发人员必须小心他们的 Passbook 产品(及其附带的推送通知)发送给用户的频率和持续时间。

### Passbook 开发者详细信息

如前所述，苹果的新 Passbook 应用包括一个相当强大的 Pass Kit REST([representative State Transfer](http://en.wikipedia.org/wiki/Representational_state_transfer))web 服务，以及一个 Pass Kit API。这些工具是 Passbook 的开发者端，它们旨在允许开发者将 Passbook 集成到他们自己的应用中。以下是对这两种技术的全面概述——你可能需要一个苹果开发者账户来查看它们。

**Passbook API(需要 iOS 开发者程序)**
https://Developer . apple . com/library/pre release/iOS/# documentation/user experience/Reference/pass kit _ Framework/_ index . html

**Passbook web service(需要 iOS 开发者程序)**
https://Developer . apple . com/library/pre release/iOS/# documentation/PassKit/Reference/PassKit _ web service/web service . html

对于 Passbook 功能的非技术性概述，Apple 提供了以下资源。(你可能需要创建一个苹果开发者账户才能看到全部，但你不应该支付任何费用。)

*   [IOs 6 上的 Passbook 入门](https://developer.apple.com/passbook/getting_started_with_passbook.pdf)
*   [介绍存折，第一部分](https://developer.apple.com/videos/wwdc/2012/?id=301)和[第二部分](https://developer.apple.com/videos/wwdc/2012/?id=309)
*   以及 [Passbook 编程指南](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/PassKit_PG/Chapters/Introduction.html)、[框架参考](https://developer.apple.com/library/ios/documentation/UserExperience/Reference/PassKit_Framework/_index.html#//apple_ref/doc/uid/TP40012158)、[包格式参考](https://developer.apple.com/library/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html)、 [Web 服务参考](https://developer.apple.com/library/ios/#documentation/PassKit/Reference/PassKit_WebService/WebService.html#//apple_ref/doc/uid/TP40011988)。

### 用法力**和通行证** 制作通行证

作为一名移动开发人员，您可能需要为您的客户做更多的事情，而不仅仅是编写一些 Pass Kit web 服务调用。如果你在与小企业或初创公司合作，你的客户可能没有优惠券或客户奖励计划。由于没有实际的通行证，您无法使用 Passbook 向客户进行宣传，因此您可能需要帮助您的移动设计客户进行 Passbook 的宣传。幸运的是，已经有一些服务可以满足这种特殊的需求。

Mana 是一个新生的营销解决方案，旨在帮助小企业充分利用苹果的新 Passbook。企业主可以使用 Mana 创建促销活动或优惠券，并在 Passbook 中与潜在客户分享。那些来到实体店附近的人会收到他们存储在 Passbook 中的促销提醒，许多人会走进商店。Mana 对企业主有一个特别强的吸引力——当客户实际上接受他们的存折促销时，他们只收取(少量)费用。这大大降低了通常与这些促销活动相关的财务风险，这使得 Mana 对希望为客户利用 Passbook 的企业主和移动开发者来说都非常有吸引力。

Passk.it 是另一项帮助公司创建、分发和管理存折促销的服务。企业主可以使用一步一步的 [pass designer](https://create.passkit.com/) ，在几分钟内创建并分享一个存折推广。Passk.it 和 Mana 之间的一个关键区别是，Passk.it 超越了苹果的 Passbook，也适用于 Android 设备。对于希望将 Passbook 内置到下一个应用 Passk 中的移动开发者来说，这是一种让客户能够开发和提供自己的促销活动的方式。

*您有计划将 Passbook 集成到自己的应用程序中吗？你认为 Passbook 会像预测的那样受欢迎吗？*

## 分享这篇文章