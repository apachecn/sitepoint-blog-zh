# 在线支付受理指南-第 2 部分

> 原文：<https://www.sitepoint.com/online-payment-acceptance-2/>

在《买家指南》的第 1 部分中，我们介绍了信用卡处理以及这些交易可能采取的各种形式。现在我们进入实质问题——费用、收费和你应该问潜在供应商的问题。

##### 如何处理在线交易

交易的过程实际上非常复杂，但是只需要几秒钟就可以完成。如果您使用实时处理解决方案，下面是 onlione 事务的发生方式:

1.  顾客选择将他们放入购物车或从商家网站上的订单中选择的商品移至收银台。
2.  然后，客户选择“信用卡”作为他们的支付方式。
3.  他们的浏览器连接到网站主机的安全服务器，并打开安全支付表单。

5.  客户在安全支付表单上输入他或她的信用卡信息，并通过点击“完成订单”类型的按钮来授权交易。
6.  交易信息通过 SSL 加密流向网站主机的安全服务器。
7.  安全服务器通过安全支付网关(第三方通过陆线连接到处理银行)或直接连接到商户的处理银行(一些处理商拥有自己的专有安全支付网关，因此不需要第三方提供此服务)。
8.  处理器直接轮询卡网络，例如 Visa 或 Master 卡，并且确认卡的有效性和资金的可用性。
9.  如果交易被批准，授权码被返回到处理器，或者从处理器返回到安全支付网关。
10.  授权由支付网关或处理器加密，并以加密的形式传输到商家的网络服务器，从而触发订单的履行。
11.  然后，商家的网络服务器向顾客的浏览器发送确认收据。
12.  应付金额从持卡人的银行转移到商户的处理银行。然后，商户的处理银行将在 2 到 3 个工作日内将资金转移到商户的当地银行。

##### 涉及的费率和费用

当决定你想和哪一个商家交易时(有很多)，影响大多数人的一个因素是费用。获得接受信用卡交易的设施可能会很贵，所以要确保你比较不同的供应商，以获得最好的交易。并且阅读所有的细则——你可能会在这里发现一些其他地方没有提到的“隐藏”费用。

以下是与商户账户相关的大致费率和费用的基本概述:

**申请/设置:0-100 美元+(一次性费用)**

所有供应商都有申请费。有些在您的协议开始时收取，有些则将其添加到解决方案的购买或租赁成本中。

**硬件/软件:购买:99 美元及以上租赁:20 美元/月及以上**

尽可能远离租赁。从一开始就购买总比在接下来的 12 个月、24 个月、36 个月或 48 个月里支付租金要好。为什么？因为与一开始就购买解决方案相比，通过租赁，您最终可能要支付 3 倍甚至更多的费用。

虽然 48 个月 29.95 美元的月租听起来很不错，但事实并非如此。一旦你同意了租约，就很难撕毁。如果你的公司在 48 个月内倒闭，你仍然需要支付硬件费用，直到租赁公司收到最后一分钱。此外，你同意的租赁费通常不包括你所在州的销售税，也不包括损害或损失豁免的费用。

如果你决定租赁，一定要确定租赁的买断条款、租赁期限的结束，特别要注意那些允许租赁公司在 48 个月过去后继续向你收费的条款(他们通常会说你应该在租赁结束前一个月书面联系他们，或者你可以让他们继续向你收费)。

**编程:0-100 美元+(一次性费用)**

这项费用通常只适用于从一个供应商转向另一个供应商的零售商。编程过程并不难，但是要注意成本——一些提供商会向你收取编程费。如果你用自己的设备，他们为什么要收这个费用？该公司经常用这笔费用来弥补他们没有向你出售或出租设备而造成的损失。

**折扣率:每笔交易 1.49%-4%**

这是从采购成本中扣除的固定百分比金额。较低的折扣率适用于零售企业，而较高的折扣率适用于邮购/电话订购(MOTO)和基于互联网的企业。为什么零售成本更低？因为信用卡欺诈的例子在零售情况下要少得多，所以银行能够对这些业务收取较低的百分比。

**交易:每笔交易 0.10-0.50 美元**

除了折扣率，交易费也从购买成本中扣除。与折扣率一样，零售企业的交易费较低，而手机和互联网企业的交易费略高。地址验证服务(AVS)可以作为附加费用收取，也可以包含在基本交易费用中。

**每月最低消费:每月 20-25 美元**

这项费用是基于您的交易和折扣率的费用，每月对您的信用卡销售收取。例如，如果您的银行每月最低收费为 25 美元，则银行每月收取的交易费和折扣率费用必须等于或超过 25 美元。如果是这种情况，将不收取每月最低费用。但是，如果当月收取的费用没有达到 25 美元的最低限额，您将被收取差额。然而，并不是所有的加工商都有最低费用。

**网关接入:每月 0-25 美元以上**

在大多数情况下，安全支付网关提供商是一家独立于商户处理器的公司，他们收取单独的费用。您的帐户在他们的系统上的每个月都要支付接入费。三大网关(如前所述)每月收取 15-25 美元的网关接入费。找到一个较小的供应商或有自己的免费网关的商业处理器，以获得最好的交易，在某些情况下，这可能意味着免费访问。

**声明:每月 10-15 美元**

在每个月末，你会收到一份来自你的处理银行的对账单，其中列出了该月发生的所有交易——与你的信用卡或电话账单没有什么不同。在许多情况下，对账单显示在网上，所以没有邮寄对账单，这笔费用可能被称为账户维护费或技术支持费。

**每日结算:每天 0-0.15 美元**

这项费用与那些要求您在每个营业日结束时结清所有交易的软件和终端处理解决方案有关。大多数提供商不再收取这项费用。

**地址验证系统(AVS):每笔交易 0-0.05 美元**

AVS 服务检查客户给出的账单地址是否与信用卡相关联的地址相匹配。这项服务只在美国有效，目前美国以外的信用卡持有者没有 AVS 服务。

如果你选择不使用 AVS，VISA 和 MasterCard 将不会支持你的交易，他们将向你收取额外的销售额的 0.17%至 1.25%。大多数商家账户都有免费的 AVS。如果有费用，在大多数情况下，它与每笔交易的费用相结合。

**按存储容量使用计费:每个实例 5-25 美元**

当持卡人对其每月信用卡账单上的费用有争议时，就会发生拒付。大量的退款会导致你的商家账户被完全撤销，并且当你试图为你的企业获得另一个商家账户时，会让你陷入困境。如果是这种情况，你可能会在几年内无法获得另一个商家帐户。

**预订:费用各不相同-详情请咨询特定提供商**

一些供应商会要求您持有一个储备账户，其余额由您企业的预计销售收入决定。准备金几乎总是向不在美国的商户收取，这些商户试图在美国获得一个商户账户。此外，每月完成大量销售的企业可能会被收取保留费。

在大多数情况下，预留费用于支付商家账户上的任何退款成本。

…大多数服务提供商的费用都在上述范围内。你可能会发现一两家公司会对他们服务的特定方面收取较低的费用，但要小心——他们可能会在其他地方以另一种费用弥补差额。

##### 购买还是租赁？

如果您选择租赁，您最终将比在第一天购买处理解决方案支付更多的费用。假设你找到一个商家账户提供商，他会收取你 200 美元来购买一个实时互联网信用卡处理解决方案。或者他们会在接下来的 48 个月里以每月 20 美元的价格向您租赁相同的解决方案。这份租赁协议总共将花费您 980 美元——比您购买该解决方案并在同一时期使用它要多支付 760 美元。此外，请注意，上述计算不包括租赁的州销售税，也不包括损坏/损失豁免的费用。有了这两项额外成本，您最终可能要多支付 20 美元，以及针对该解决方案收取的租赁费用。另一件事你应该注意的是，48 个月的租约是不可取消的，所以即使你停业，你可能仍然要支付租金，直到 4 年(48 个月)完成。

并非所有租赁期限都是 48 个月，有些提供商提供 12 个月、24 个月和 36 个月的租赁。但是，租期越短，每月的租赁费用就会越高。你的信用等级也会影响你每月支付的租金。那些信用差或没有信用的人几乎总是比那些信用好到优秀的人付出更多。有关详细信息，请咨询商家帐户提供商。

不用说，购买加工解决方案是目前最好的方式，而且可以 100%免税。

##### 要问的一般问题

除了确定这些费用之外，在你冒险购买他们的解决方案之前，还有几件事你应该问公司:

*   你能给我提供至少 5 个客户的电子邮件地址或网址吗？我可以联系他们作为证明人。
*   你愿意把一切都写下来吗，包括所有的费用？
*   加工银行的名称和地址是什么？
*   你用 Visa 和 MasterCard 注册了吗？
*   您是否通过电话提供 24/7 全天候支持？(如果是这样，他们应该测试一下)
*   是否需要预留账户？(如果答案是肯定的，去别处看看)
*   我的资金什么时候能到位？(目标为 3 天或更短时间)
*   你是当地商会和/或商业促进局(BBB)的成员吗？(如果是，查看他们的记录中是否有任何以前的投诉或争议)
*   与你合作发行商业账户的银行是否有联邦存款保险公司的保险？
*   你是电子交易协会(ETA)的成员吗？ETA 是一个贸易组织，支持独立服务组织(ISOs)的公平和道德的商业实践，这是一个对商家帐户提供商的花哨名称。

##### 互联网实时处理问题

*   安全支付网关和商家账户是否已经与您想要使用的购物车或解决方案兼容？集成可能非常昂贵，或者在某些情况下是不可能的。
*   你的虚拟主机公司支持你喜欢的网关/处理器吗？他们也许能够适应你，但通常这需要时间和耐心，而且可能是不可能的。
*   网关/处理器支持电子检查吗？如果没有，你将不得不付钱给另一家公司为你做这件事。目标是找到一个在同一屋檐下提供支票、信用卡和网关支持的提供商。
*   提供商是否在其网站上公布所有费用？那些不知道的人可能会隐瞒这些信息，这样他们就可以提高他们向你收取的价格！
*   公司对他们(和你)来说是不是太大了？在许多情况下，较小的公司能够更好地提供直接支持，帮助集成和一致的服务。
*   商家处理器或网关是否只提供互联网服务？我们都听说过“网络公司的失败”，互联网处理器也不例外。CyberCash，也许是最著名的网关，最近申请破产！确保您的处理器/网关也有真实世界的商家作为客户，以确保您业务的长期稳定性。
*   网关是否提供虚拟终端和在线报告？确保他们做到了，因为你会需要这些设施。

确保你仔细阅读了所有的条款和条件，并关注所有的相关费用，而不仅仅是折扣和交易价格。同样，我们建议您考虑购买处理解决方案，而不是租赁解决方案——从长远来看，您会很高兴您这样做了。

下周，[第 3 部分](http://ecommercebase.com/article/500)将探讨第三方处理器、欺诈、加密和购物车软件概述。到时候见！

## 分享这篇文章