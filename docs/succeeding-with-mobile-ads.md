# 移动广告的成功

> 原文：<https://www.sitepoint.com/succeeding-with-mobile-ads/>

*本文最初发表在 [TextNow 工程博客上。](https://engineering.textnow.com/succeeding-with-mobile-ads-2402e9ce1dec)*

对于大多数开发者来说，总有一天他们需要评估他们的盈利方式，并决定应用内广告是否适合他们的应用。如果答案是肯定的，并且他们想继续做广告，那么下一步就是评估整合什么样的广告中介平台对他们来说是有意义的。

## 什么是广告中介平台？

一个[广告中介平台](https://support.google.com/admob/answer/3063564?hl=en)，通过允许出版商与各种广告网络或<ahref target="_blank">需求方平台(DSP)集成来简化广告，以最大化</ahref> <ahref>eCPM。这些广告提供商被放置在一个瀑布上，将根据您设置的各种阈值提供广告。使用中介的最大好处是你只需要集成任何 ad 单元一次。添加一个新的广告提供商就像添加一个新的 SDK 到你的 *build.gradle* 或者 *Podfile* 一样简单，然后添加适配器文件。中介 SDK 随后将使用</ahref> <ahref>反射来调用我们的适配器文件，这些文件调用各自的 ad SDK 来显示广告。</ahref>

选择使用哪个特定平台将取决于许多因素，例如:平台支持(iOS、Android、Unity)、广告单元类型可用性(横幅、插播广告、视频)、社区支持和客户端代码的开源。一些热门平台有: <ahref target="_blank">MoPub、 [AerServ](https://www.aerserv.com/) 、 [Fyber](https://www.fyber.com/) 、 [AdMob](https://www.google.com/admob/) 。</ahref>

## 选择 MoPub

我们决定在 TextNow 使用 MoPub，因为它易于集成，广泛的广告网络支持，以及对 <ahref>JS 标签的支持。到目前为止，我们的经验是相当积极的。</ahref>

在这个平台上添加新的提供商非常简单，我们还可以通过 JS 标签灵活地添加新的广告网络。尽管 MoPub 出于性能和跟踪的原因不推荐 JS 标签，但我们已经成功地使用了它们。有时，为了添加新的广告提供商而发布新的 clientrelease 需要太长时间，或者根本不值得这么做，因为 JS 标签也能很好地工作。

## 莫普布来救援了

MoPub 仪表板可以有效地充当特定 ad SDKs 的功能切换机制，这对于故障排除和解决 ad 问题特别有用。过去，当我们为特定 SDK 发布一个带有损坏的 ads 集成的客户端时，这个特性救了我们。我们看到崩溃的数量在 <ahref target="_blank">Crashlytics 中激增，原因是一个与广告相关的活动被意外地从 AndroidManifest.xml 中删除了。为了防止我们的 Android 应用崩溃，我们改变了特定广告提供商的 MoPub</ahref> <ahref>行项目，以阻止该版本应用的流量广告。当我们准备进行热修复时，它立即停止了崩溃。</ahref>

## 结果

使用广告中介平台处理广告帮助我们实现了收入最大化，并让我们有更多时间专注于 ourapp 的核心功能。这使得管理我们的广告变得不那么痛苦了。A/B 测试广告单元和最大限度地降低未填充广告位的机会成本相结合，使 TextNow 成为迄今为止最成功的季度之一。

## 分享这篇文章