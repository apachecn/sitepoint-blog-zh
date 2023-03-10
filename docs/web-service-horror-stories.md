# Web 服务恐怖故事

> 原文：<https://www.sitepoint.com/web-service-horror-stories/>

我们大多数人都期待着假期，你可能会考虑把你一整年都在思考的想法变成你的假期计划。大量的服务意味着你可以专注于你想法中令人兴奋的部分，而把无聊的细节(比如支付处理)留给另一个人。

如果你这样做，要小心你选择依赖哪些服务！随着网络开发者越来越依赖 PayPal、Vimeo 和苹果应用商店等第三方服务，我们听到了更多合作失败的恐怖故事。就在本周，我的办公桌上出现了两个这样的案例。

首先， [Apparent Software](http://www.apparentsoft.com/) 的 Jacob Gorban 写了 [PayPal 如何积极破坏他的业务](http://blog.apparentsoft.com/business/124/is-paypal-good-for-your-microisv-business-a-short-paypal-horror-story/)。11 月，他与其他六名 Mac 软件开发商合作推出了 [MacGraPhoto](http://www.macgraphoto.com/) ，为期两周的 Mac 图形应用捆绑销售。

销售获得了巨大成功，但 Gorban 的 PayPal 账户上的突然活动触发了反欺诈锁定。他被禁止接受付款，最终他的账户资金被冻结了六个月(T1)，同时终身禁止使用贝宝。所有这一切都是为了成功的销售。

在试图解决这个问题的过程中，他联系的 PayPal 支持人员一再欺骗他，浪费他(以及他的合作伙伴)的时间，要求提交文件，而这些文件一旦提交，就根本不会被看一眼。

在 Gorban 就此事高调发表博客后，PayPal 高层最终联系了他以解决这一问题。但这晚了一个月，而且是一次公关损失控制的演习——而不是真正的客户支持。

**第一课:**避免依赖 PayPal 或任何其他无法提供有意义的合作伙伴支持的市场主导者。至少，要做好准备，一接到通知就换一个替代方案。

接下来，Paul Boag(成功的 [Boagworld 播客](http://boagworld.com/)的成员)写了关于[为什么你会后悔使用 Vimeo](http://boagworld.com/reviews/vimeo) 。作为视频分享网站 Vimeo 的付费高级会员，Boag 使用该服务来托管视频，以便在 Boagworld 网站上发布。然后，出乎意料的是，Vimeo 认定他的几个视频违反了它的使用条款；他们通过电子邮件告诉他，在视频被删除之前，他有 24 小时的时间将视频转移到其他地方。

原来 Vimeo 反对的是商业内容。在 Boag 的案例中，它采用了视频的形式，就如何经营更成功的企业提出建议。在进一步的调查中，Boag 发现 Vimeo 在执行其“无商业内容”的规定方面非常不一致，对微妙的违规行为采取强硬态度，同时对高调的案件视而不见。

简而言之，Vimeo 的使用条款可以归结为一条规则:如果他们不喜欢你的视频(即使你是几个月前发布的)，它将在 24 小时内被删除。

**第二课:**在投资第三方服务之前，阅读其使用条款，然后对其选择如何*执行*这些条款做一点研究。含糊不清和不可预测是商业伙伴不可取的品质。

记住:有时候如果你想把工作做好，你必须自己去做。在对 Paul Boag 的故事的评论中，SitePoint 的作者 Rachel Andrew 提出了一个很好的建议:将视频放在亚马逊 S3 上，并使用一个开源视频播放器来播放。

有时候——只是有时候——合作伙伴可能比他们的价值更麻烦。

## 分享这篇文章