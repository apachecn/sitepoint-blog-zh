# 区块链是如何运作的？角。一

> 原文：<https://www.sitepoint.com/how-does-the-blockchain-work-pt-1/>

*本文原载于[区块链评论](https://blockchainreview.io/blockchain-work-part-1/)。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

如果你真的想进入区块链和加密货币的世界，我强烈建议你通过阅读这两本白皮书开始你的旅程:[比特币:一个点对点的电子现金系统](https://bitcoin.org/bitcoin.pdf)和[以太坊:下一代智能合约和去中心化应用平台](https://github.com/ethereum/wiki/wiki/White-Paper)——然后一遍又一遍地阅读它们。

如果你像许多人一样问这样的问题:*“我有一个想法，我想知道我的项目是否适合区块链，”*阅读这两篇论文，加上[如何雇用一个区块链开发者。](https://medium.com/@zachpiester/how-to-find-and-hire-a-blockchain-ethereum-and-hyperledger-developer-9d642c93e0e1)

我祝你一路顺风。让我们具体看一下区块链。

## 区块链是如何运作的？

区块链是当今世界的热门话题，但对许多人来说，这项技术仍然是一个难以捉摸的概念。但这不是必须的——一旦你了解了基础密码经济学的架构和理论，这个概念就很简单了。当你真的有了“啊哈”的时刻，这个世界对你来说就不再是原来的样子了。

这本区块链基础指南旨在对我们这个时代最具变革性和最容易被误解的技术之一进行清晰的、非技术性的介绍。如果你想知道什么是区块链技术，它是如何工作的，以及它的潜在影响，而不需要所有的技术术语，那么这篇文章就是为你准备的。

## 交易货币简史

从历史上看，当涉及到交易金钱或任何有价值的东西时，人们和企业都非常依赖银行和政府这样的中介机构来确保信任和确定性。[ [1](http://www.weforum.org/agenda/2016/06/blockchain-explained-simply/) ]中间人执行一系列重要任务，帮助在交易过程中建立信任，如身份验证和记录保存。[ [2](http://www.ted.com/talks/don_tapscott_how_the_blockchain_is_changing_money_and_business/transcript?language=en)

在进行数字交易时，对中介的需求尤其迫切。因为像金钱、股票和知识产权这样的数字资产本质上是文件，它们非常容易复制。这就产生了所谓的双重消费问题(不止一次消费同一单位价值的行为)，这种问题迄今为止一直阻碍着数字资产的点对点传输。[ [3](http://www.ted.com/talks/don_tapscott_how_the_blockchain_is_changing_money_and_business/transcript?language=en)

但是，如果有一种方法可以在没有第三方中介的情况下进行数字交易呢？如今，一项新技术的存在使这成为可能。但是，在我们深入这项革命性技术的机制之前，提供一点背景知识是很重要的。

## 区块链 vs 比特币:有什么联系？

比特币最早出现在 2008 年的一份白皮书中，作者是一个或多个化名为中本聪的人。[白皮书](https://s3-us-east-2.amazonaws.com/br-media/wp-content/uploads/2017/08/31062510/bitcoin.pdf)详细介绍了一种被称为比特币的创新性点对点电子现金系统，该系统使在线支付能够直接转账，无需中介。[ [4](https://s3-us-east-2.amazonaws.com/br-media/wp-content/uploads/2017/08/31062510/bitcoin.pdf)

![How the Blockchain Transfers Value](img/cc6d5b25c488648d28a7115fd716d743.png)

*区块链如何通过[techliberation.com](https://techliberation.com/2013/12/04/why-would-anyone-use-bitcoin-when-paypal-or-visa-work-perfectly-well/)转移价值*

虽然拟议中的比特币支付系统令人兴奋且具有创新性，但真正具有革命性的是其工作机制。白皮书发布后不久，很明显，主要的技术创新不是数字货币本身，而是它背后的技术，即今天的区块链。

虽然通常与比特币联系在一起，但区块链技术还有许多其他应用。比特币仅仅是第一个也是最广为人知的实现。事实上，比特币只是今天使用区块链概念的大约 700 种应用中的一种。[ [5](https://www.youtube.com/watch?v=3lMvo0PPxjQ)

> [区块链]之于比特币，就像互联网之于电子邮件。一个大的电子系统，你可以在其上构建应用程序。货币只是其中之一。— *莎莉·戴维斯，英国《金融时报》科技记者[6](http://video.ft.com/4029769033001/How-bitcoin-and-its-blockchain-work/Companies)*

除了数字货币，区块链的发展和广泛应用的一个例子是以太坊公共区块链的发展，它提供了一种执行点对点合同的方式。[ [7](https://www.cointelegraph.com/ethereum-for-beginners/what-is-ethereum)

## 区块链引擎盖下是什么？

简而言之，区块链是一种分布式分类账或分散式数据库，持续更新谁拥有什么的数字记录。与传统数据库(比如银行、政府和会计师)的中央管理员不同，[分布式分类账](https://www.youtube.com/watch?v=oSP-taqLWPQ&feature=youtu.be)拥有一个由复制数据库组成的网络，通过互联网同步，网络内的任何人都可以看到。[ [8](https://docs.google.com/document/d/1AjE5YERVCExXnyeYfYEZAdtGCocubCDQY9fhLwPyvJ4/pub#ftnt8)

区块链网络可以是[私有的，成员受限，类似于内部网，也可以是公共的](https://blog.ethereum.org/2015/08/07/on-public-and-private-blockchains/)，像互联网，世界上任何人都可以访问。[ [9](https://www.youtube.com/watch?v=3lMvo0PPxjQ) ，[ [10](https://blog.ethereum.org/2015/08/07/on-public-and-private-blockchains/)

当进行数字交易时，它与在过去 10 分钟内发生的其它交易一起被分组在密码保护的块中，并被发送到整个网络。然后，矿工(网络中具有高水平计算能力的成员)通过解决复杂的编码问题来竞争验证交易。第一个解决问题并验证砖块的矿工将获得奖励。例如，在比特币区块链网络中，一名矿工会收到比特币。

然后，经过验证的事务块被打上时间戳，并以线性的时间顺序添加到链中。经过验证的新事务块链接到旧事务块，形成一个事务块链，显示该区块链历史中的每一个事务。整个链条不断更新，因此网络中的每一个分类账都是一样的，让每个成员都有能力证明在任何给定的时间谁拥有什么。

> 区块链是一台神奇的计算机，任何人都可以上传程序，让程序自行执行，其中每个程序的当前状态和所有以前的状态总是公开可见的，并且它具有非常强大的加密经济安全保证，在链上运行的程序将继续完全按照区块链协议指定的方式执行。— *维塔利克·布特林*

区块链的去中心化、开放和加密性质允许人们相互信任并进行点对点交易，从而不再需要中介。这也带来了前所未有的安全优势。通常会影响银行等大型中央中介机构的黑客攻击实际上不可能在区块链得逞。例如，如果有人想要侵入区块链的某个特定区块，黑客不仅需要侵入该特定区块，还需要侵入该区块链的整个历史中的所有进行中的区块。他们需要同时在网络中的每一个账本上做这件事，这个账本可能有数百万个。[ [13](http://www.ted.com/talks/don_tapscott_how_the_blockchain_is_changing_money_and_business/transcript?language=en)

## 区块链会改变互联网和全球经济吗？

对此不要有任何误解。区块链是一项极具颠覆性的技术，有望改变我们所知的世界。这项技术不仅改变了我们使用互联网的方式，也彻底改变了全球经济。[ [14](http://www.mckinsey.com/industries/high-tech/our-insights/how-blockchains-could-change-the-world)

通过实现资产的数字化，区块链正在推动从信息互联网到价值互联网的根本性转变，在信息互联网上，我们可以即时查看、交换和交流信息，在价值互联网上，我们可以即时交换资产。一个新的直接价值转移的全球经济正在形成，大型中介不再扮演主要角色。信任不是通过中央中间人，而是通过共识和复杂的计算机代码建立的经济。[ [16](http://www.ted.com/talks/don_tapscott_how_the_blockchain_is_changing_money_and_business/transcript?language=en)

> 可能对未来几十年产生最大影响的技术已经到来。而且不是社交媒体。不是大数据。不是机器人学。甚至都不是 AI。你会惊讶地发现，这是比特币等数字货币的底层技术。它被称为区块链。— *唐·塔斯考特*

区块链的应用[超越了数字货币和资金转移等显而易见的事物。从电子投票、](http://www.economist.com/news/briefing/21677228-technology-behind-bitcoin-lets-people-who-do-not-know-or-trust-each-other-build-dependable)[智能合同](https://www.youtube.com/watch?v=FkeLDPZ-v8g)和数字记录的财产资产，到患者健康记录管理和数字内容的所有权证明。

区块链将深刻颠覆数百个依赖中介的行业，包括银行、金融、学术、房地产、保险、法律、医疗保健和公共部门等。这将导致失业和整个行业的彻底转变。但总体而言，取消中介带来的好处大多是正面的。例如，由于处理交易和监管要求需要时间，银行和政府经常阻碍业务的自由流动。区块链将使越来越多的人和企业能够更频繁、更高效地进行交易，从而极大地促进当地和国际贸易。区块链技术还将消除昂贵的中介费用，这些费用已经成为个人和企业的负担，特别是在汇款领域。

也许最深刻的是，区块链承诺民主化和扩大全球金融体系。让那些对全球经济了解有限的人能够更好地利用金融和支付系统，并更好地防范腐败和剥削。

> 地球上每一个拥有手机的人都将有平等的机会使用手机。将潜在市场总量扩大了 4 倍。——*布洛克·皮尔斯*

区块链技术对社会和全球经济的潜在影响是巨大的。随着越来越多的实际应用，区块链技术有望产生巨大的影响。这只是开始。

许多最令人兴奋的应用和平台甚至还没有被发明出来！

如果您想了解更多友好且易于获取的信息，请点击此处查看我们系列的其他内容:

*   [区块链是如何工作的？第 2 部分](https://www.sitepoint.com/how-does-the-blockchain-work-pt-2)–*你需要知道的 5 件大事*。
*   [区块链是如何工作的？第 3 部分](https://www.sitepoint.com/how-does-the-blockchain-work-pt-3)–*什么是共识，为什么它很重要？*
*   [区块链是如何工作的？第 4 部分](https://www.sitepoint.com/how-does-the-blockchain-work-pt-4)–*区块链的私立、公立和财团有什么区别？*

我一直很想见到区块链的创始人、学术研究人员和从事挑战性项目的技术人员，所以请随时通过 LinkedIn 联系我，或者发邮件到 collin@intrepid.ventures

*   寻找新的加密货币？查看对[元数据库](https://medium.com/blockchain-review/metabase-a-blockchain-platform-to-build-next-generation-businesses-d08d8445584c)的介绍。
*   想推出自己的 ICO？现在在[令牌卡](https://www.tokendeck.io/)申请。

## 分享这篇文章