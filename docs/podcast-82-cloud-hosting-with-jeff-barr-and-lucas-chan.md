# SitePoint 播客#82:杰夫·巴尔和卢卡斯·陈的云托管

> 原文：<https://www.sitepoint.com/podcast-82-cloud-hosting-with-jeff-barr-and-lucas-chan/>

*第 82 集 SitePoint 播客*现已推出！本周凯文·杨克( [@sentience](http://twitter.com/sentience) )与杰夫·巴尔([@杰夫巴尔](http://twitter.com/jeffbarr))和卢卡斯·陈( [@geekylucas](http://twitter.com/geekylucas) )就亚马逊网络服务的云托管进行了交谈。Jeff 是 SitePoint 的新书《在云中托管你的网站》的作者。卢卡斯是 [SitePoint](https://www.sitepoint.com/) 、 [99designs](http://99designs.com/) 和 [Flippa](http://flippa.com/) 的首席系统管理员。

## 在浏览器中收听

直接在你的浏览器里播放这一集！只需点击下方橙色的“播放”按钮:

<audio controls="" preload="metadata"><source src="https://s3.amazonaws.com/s3.sitepoint.com/audio/podcasts/sitepoint/sitepointpodcast082.mp3" type="audio/mpeg; codecs=&quot;mp3&quot;"></audio>

## 下载这一集

你也可以下载这一集作为一个独立的 MP3 文件。以下是链接:

*   [SitePoint 播客#82:杰夫·巴尔和卢卡斯·陈的云托管](https://s3.amazonaws.com/s3.sitepoint.com/audio/podcasts/sitepoint/sitepointpodcast082.mp3) (MP3，1:46:54，97.9MB)

## 采访记录

凯文:2010 年 10 月 8 日。两位云计算专家合作，揭开在云中托管网站的细节；我是凯文·杨克，这是由杰夫·巴尔和卢卡斯·陈主持的 SitePoint 播客#82:云托管！

大家好，欢迎来到另一个 SitePoint 播客。我不想花太多时间做这个介绍，因为我们还有很多事情要做。这个播客可能是我们之前在 SitePoint 发布的播客中最长的一个，几乎超过了一个小时。你将要听到的是一个在线研讨会的音频，我们举办这个研讨会是为了支持我们的新书《在云中托管你的网站》的发布，作者是杰夫·巴尔。这是一次非常成功的活动，如果你希望能够看到配有音频的幻灯片，我们确实有那个版本的演示文稿，我们在网站上有[这些幻灯片的完整录音](https://www.sitepoint.com/cloud-computing-webinar-full-recording/ "Cloud Computing Webinar: Full Recording")，我一定会在演示笔记中链接到那个，但我认为演示文稿作为音频也很好，因为幻灯片中提到的所有内容也会被演示者在音频中提到。所以，与其坐在你的电脑前读两个小时的幻灯片，我想，你知道，不如计划做些家务，出去做些园艺，甚至在你听这个的时候去散散步，并接受来自亚马逊的 Jeff Barr 和我们自己的 Lucas Chan 的两个小时的专业知识，他在 SitePoint，99designs 和 Flippa 运营服务器。我会把这些猫放在一起，在这里抛出问题，但大多数时候你会听到它们在说话，这真的是很好的东西。所以坐下来，也许计划在这个过程中休息一下，但我希望你喜欢它。

大家好，欢迎参加 sitepoint.com 与亚马逊合作举办的特别网络研讨会，庆祝我们的新书《在云端托管你的网站:亚马逊网络服务变得简单》的发布，我们非常荣幸地邀请到该书的作者杰夫·巴尔和我们在一起，你好，杰夫。

杰夫:你好。

**凯文:**杰夫于 2002 年加入亚马逊，当时他决定明智地押注于云计算的发展，亚马逊将成为一个大玩家，如果不是*成为该领域的*玩家的话。作为 Amazon Web Services 的高级布道者，Jeff 在世界各地的会议、用户组和网络研讨会上向开发人员发表演讲。我们还邀请到了 Lucas Chan，他是我们的一位，实际上是我们在 SitePoint 的首席系统管理员；嗨，卢卡斯。

卢卡斯:大家好。

**Kevin:** Lucas 于 2002 年加入 SitePoint，当时他是一名网站开发人员，但自那以后，他开始领导 SitePoint 向云的迁移。如今，他是 SitePoint 服务器基础设施的最大负责人，其中大部分虚拟托管在亚马逊网络服务上。因此，在接下来的一个小时里，我们要做的是谈论一下云托管以及这本书所涵盖的内容，但我们在这里最大的任务是给此刻坐在网上研讨会上的各位一个提问的机会，并获得关于云托管、云计算的所有问题的答案；我们已经得到了他本人在这里，我们想确保你们得到充分利用。所以，我们这里有大约半个小时的材料，我们将非正式地浏览这本书，并谈论它讨论的一些不同问题，但在网上研讨会中有一个问题功能，所以，一旦你有什么要问的，或者即使你带着一些你想得到答案的问题来参加网上研讨会，请随时提问。我们请到了我们在 SitePoint 的技术编辑之一 Louis Simoneau，他将处理所有这些问题并标记出来，这样我就可以向我们小组的两位专家提问了。

所以，事不宜迟，我想我们直接开始吧。我将把这本书的作者交给你，Jeff，让我们开始吧，你为什么不先给我们讲一点你写这本书的经历，以及这本书对你有什么意义。

Jeff: 好的，首先，感谢大家的到来，我很高兴能在这里和你们进行虚拟交流，希望这能让你们的时间有所值。那么，让我们看看，这本书对我意味着什么？所以，我认为我从来没有开始成为一名作家，当我更年轻的时候，实际上可以追溯到高中，我记得几位老师告诉我，我似乎是一个相当聪明的人，但我的写作技能实际上非常非常差，他们几次给我的建议是，如果你能学会如何写作，你可能在生活中做得相当好。所以我看着这本书，拿起它，说我认为我实际上已经明白了这一点，我实际上翻了几页，读了一遍，对文本的质量、内容的质量和整个流程都非常满意。

至于我是如何来到这里的，我在华盛顿的 Sammamish 工作，这基本上是微软的总部所在地 Redmond 的下一个城市，在我早期的职业生涯中，我在 80 年代和 90 年代在一些创业公司和小公司呆过一段时间。1997 年，我和我的家人从马里兰州搬到了华盛顿州，这样我就可以在微软工作，在那里的时候，我花了一些时间在 Visual Basic 6 版本上，这被认为是最后一个伟大的 Visual Basic 版本，然后我花了几年时间在 visualstudio.net。在那之后，我离开了微软，想做一些更令人兴奋的事情，我对那里非常非常长的产品周期感到有点沮丧，所以我联系了我的一个朋友，他在互联网热潮中赚了一些钱，他希望我帮助他调查和投资公司。因此，几年来，我开始帮助他评估公司，评估技术、市场和人员，并就他是否应该投资给他建议。所以，当时我们在 2000 年、2001 年考察的许多公司都在谈论 XML、SOAP 和 web 服务，如果你还记得 UDDI 之类最好被遗忘的东西，我们经常谈论这种能够通过互联网进行 web 服务调用并激活远程功能的想法，我们谈论了又谈论，老实说，当时观众有点怀疑；当我们对潜在投资者说，看，这是我们认为的未来，有能力构建应用程序，有效地利用可编程网站，并考虑网站本身将有 API，你可以构建程序来利用这些 API 的想法，人们会看到这一点，他们会相当怀疑地摇摇头，他们真的不太理解我想我可能早些时候看到的，只是 API 的力量和平台的力量。所以，当我看到亚马逊第一次发布的时候，我的账户边上有一个小纸条。有一天我登录，弹出一个小框说，“亚马逊现在有 XML 了，”我立刻觉得这很棒。我进行了调查，通过几个不同的步骤，我发现自己在 2002 年夏天从事亚马逊网络服务的工作。所以在过去的八年里，在那里度过了一段非常奇妙的旅程；我们已经从最初的服务，即产品目录访问，发展到更多的基础设施服务，这也是我在本书中重点关注的，并且成为这个组织的一部分，看着它从一些伟大的想法和一些伟大的人成长为一个拥有大量服务、伟大的客户和伟大的应用程序的组织。到目前为止，一切都很令人兴奋。

凯文:让我们直接看这本书。这本书有 11 章，我们将用它作为今天演示的大致指南。卢卡斯将在每一章中插话，就 SitePoint 正在为我们进行的相关事情给出一些实际的观点。因为我们对与亚马逊合作出版这本书感到最兴奋的原因之一是因为向云托管的转变对我们来说是一次变革性的经历。所以这本书叫做“在云中托管你的网站”，第一章是“欢迎使用云计算”云计算是我知道的术语之一，当我第一次遇到它时，我被吓住了，它看起来像一个非常计算机科学的东西…我必须为美国宇航局工作，才能让它适用于我。Jeff，你为什么不谈谈云计算以及云托管如何融入其中？

杰夫:当然可以。所以，我在书中真的试着从最开始开始，把人们带到斜坡上，这样即使你不知道它是什么，我也想确保人们真的理解这个概念， 基本上，你可以从你的桌面通过互联网连接到远程服务，这种想法实际上是从静态服务器和静态资源的模式，你每个月支付固定金额的钱，无论你有太多还是太少，到这种更加动态和灵活的模式，你可以根据需要扩大和缩小。 所以我在第一章中提到的一件事是我发明的一个短语，叫做“成功的灾难”所以，成功的灾难意味着你有了一个伟大的想法，你做了一个实现，你设置了主机，你把它放到了网上，你有点想——你有点画出了增长，认为你以后有足够的时间来处理成功和扩展，但你把它放到了网上，而不是几个朋友访问整个世界出现在你的前门， 一般来说，在当今这种注意力闪光经济中，你只有一两次机会留下好印象，如果第一天或第一周没有给人们带来高质量的体验，那么他们的注意力就会转移到下一个亮点上。 所以，这个成功的灾难就像你认为会很棒的一天，因为你得到了这么多优秀的用户，但他们实际上没有得到你想要提供给他们的服务质量。

我还想谈一谈可编程基础设施的想法，过去当你需要新的服务器和存储时，你会打电话给你的系统人员或你的托管公司，你会就交付时间和价格进行反复谈判，现在的新云模式是，你只需向云发出 web 服务呼叫，告诉云我需要另一台服务器，我需要一个磁盘驱动器， 我需要一些存储，而云会在几秒钟内自动配置并响应您所拥有的新资源的标识符。

凯文:对。那么，卢卡斯，这在我们决定迁移到云托管时起了多大作用；我们正在经历自己的成功灾难吗？

卢卡斯:是的，所有这些都很重要。特别是 Jeff 说要把人们拒之门外，在 2006/2007 年左右，我们会有机会出现在 Digg 或 Slashdot 或类似网站的首页。我们的服务器在负载下完全崩溃了，所以在那个时候，随着容量的耗尽，再加上 SitePoint 竞赛和 Marketplace 的大规模开发工作，云计算对我们来说变得非常有吸引力。尤其是我们可以用一些高度实验性的配置轻松快速地将这些服务器上线，是的，当我们发现亚马逊网络服务时，这个时机对我们来说真的很好。

Kevin: 杰夫，这些巨大的成功浪潮真的很——我的意思是，这是如今被认为是成功的网络发布的一部分，这有点像如果你得不到，你就不会得到许多即将上线的业务所需的临界质量。所以，如果你不为此做打算，你真的会让自己失败，不是吗？

杰夫:我同意。我认为经常发生的情况是，人们认为他们已经计划好了，他们已经做了一些建模，他们相信他们已经安排了足够的容量，但经常发生的情况是，网站可能会以意想不到的速度被使用，其中可能有些低效的功能，你认为可能对整个网站有点无关紧要，但实际上却是每个人都关注的事情。所以也许你做了这个小部分，然后你说，没人会用它，所以我不会索引表或者优化代码，然后突然发现这是每个人都喜欢的部分。然后，在旧模式中，它又回到了绘图板，重新架构，重新实施，而在云模式中，你会说，好吧，没关系，我只需调整一些更多的资源，我只需在很短的时间内支付稍微多一点的钱，就可以解决效率低下的问题，这将为我赢得一些时间；毫不夸张地说，你是在争取时间回去做一个更优化的实现。

凯文:好的。如果你准备好了，杰夫，我们可以进入第二章了。

Jeff: 好的，那么第二章就是我开始描述每一个不同的亚马逊网络服务的地方。所以我只是逐一介绍了一下，我非常小心地确保我不仅解决了技术问题，还解决了云的业务问题。当我出去与观众交谈时，我非常清楚地表明，云不仅仅是一项需要了解的新技术，而是需要关注业务方面，并且需要了解您是在按使用量付费的基础上为您的资源付费，因此我希望确保人们了解每种不同的资源，如处理、存储、带宽和消息传递， 每一个都有一个单位价格，你在使用它们时支付，也有不支付的，通常在开发期间，你不会使用大量的资源，所以你不会支付大量的费用，所以你可以在非常经济的基础上开发你的新应用程序或新网站，你甚至不需要扩展到大型服务器，或我们称之为的实例，直到你实际上启动并运行。 因此，我们最小的实例被称为微型，它甚至比书中记录的最小实例还要小，微型实例实际上每小时租金 2 美分，所以我认为大多数人在他们最喜欢的咖啡店或软饮料机上的花费比他们在这种价格的服务器上的花费更多。

**Kevin:** 我们谈到要为成功的浪潮做好准备，但与此同时，我认为最低服务器配置文件的低成本意味着您有很多机会等待成功的到来。

Jeff: 这完全正确，它要么是成功之前的跑道，要么是它还说你可以做更多的实验，你可以尝试一些东西，你不必担心万一你的实验不成功会浪费资源。所以一般来说，我们知道我们尝试的越多，成功的机会就越大。因此，基于云的模式确实给了你试验和测试的自由，而不必回到你的老板那里说，嗯，我们刚刚花了 100，000.00 美元购买新服务器，但事实证明没有人真正需要它们。

**Kevin:** Lucas，很长一段时间以来，我们维护着一种传统的服务器，我们在 DreamHost 之类的地方有帐户，我想我们现在仍然有，但对你来说，有没有任何实验太小而不麻烦地把它放在云托管上，或者这是你现在所有事情的默认解决方案？

卢卡斯:亚马逊前几天发布的新实例大小是微大小，对吗，杰夫？

杰夫:没错。

卢卡斯:是的，所以我的意思是，在此之前，我们可能会考虑将一个非常小的项目放到一个共享的托管环境或类似的东西上，一个非常不重要的网站，但我想说，亚马逊现在已经基本上覆盖了所有内容。这是一个非常好的情况，我们不必预测未来两三年的硬件需求，不必为这些需求进行合同谈判，也不必担心如果我们选择在某个地方进行托管，将如何维护和管理这些需求。

我确实记得上一次我们举行这样的大型会议，卢卡斯，那是在亚马逊网络服务的早期，我认为我们这些热衷于开发的人确实想向云技术飞跃，但我们必须做好尽职调查，并展示走另一条路需要多少成本。 我认为你和 Lachlan Donald 当时制定了一个庞大的服务器集群计划，可以处理合理的负载量，这些数字非常有说服力，不是吗？

卢卡斯:你知道吗，这些数字很有说服力，但实际结果和我们预测的完全不一样，如果你现在和 SitePoint 的管理层谈谈，他们会告诉你我们在主机上花的钱比我们当时预测的要多。但事实上，你必须考虑到这样一个事实，即我们已经向云环境推出了如此多的应用程序，超出了我们当时真正认为可能的数量，所以这确实给了我们更多快速增长的机会。

凯文:酷。那么，第三章，杰夫，就要开始了。所以你说的是什么样的工具呢？

**Jeff:** 第三章中的想法是，基本上我想确保人们已经安装了本地硬件，这样他们就有了自己的电脑，我需要确保他们安装了正确版本的 PHP，他们有 CloudFusion Toolkit，并且他们还知道可视化工具，各种各样的东西，如 AWS 管理控制台和他们可以使用的其他第三方工具。我还想确保他们知道如何去 AWS 网站，创建他们的帐户，并获得他们用来识别和签署请求的密钥，并理解所有这些有时只是一种减速带的事情，你看到书中的代码，你说这看起来很棒，我很想试一试，我想确保所有这些步骤都被仔细地拼写出来。我还想确保一些事情，比如理解当你构建 web 应用程序来控制一些 DNS 时，这真的很方便，当你想真正把东西放到网上并看看它们是如何工作的时候，有几种域名可供你用来做实验也很方便。

**Kevin:** 我知道当我们第一次开始使用 AWS 和 EC2 时，那是几年前的事了，那是一种命令行的、非常本质的体验。Lucas，你能谈谈我们当时是如何做到的吗？然后 Jeff，你可以谈谈情况是如何变化的。

卢卡斯:是的，当然。Kev，正如你提到的，最初亚马逊只是提供了一些命令行工具来访问他们的 API，我们认为我们可以做得更好。我们构建了一个小的 web 应用程序来管理运行我们站点的实例，但是有点困难；就像我们花了很多时间来搞清楚这一点，现在有了亚马逊提供的 AWS 控制台和其他一些第三方工具，如 ElasticFox，我们甚至使用一些第三方云管理服务，如 RightScale 和 CloudKick now。所以现在管理这些东西真的很容易，你知道我们在编写自己的工具时遇到了几次灾难，然后意外地关闭了实例和所有这类东西，所以下次还有战争故事。

Kevin: Jeff，是否可以这样说，亚马逊最初想靠边站，让第三方来控制工具的发展，他们只是想专注于构建真正坚固的管道？

杰夫:我不认为这是真正的想法。通常情况下，当我们构建新服务时，我们构建服务，我们在内部实现的基础上构建 API，然后我们构建命令行工具来运行这些 API，这是我们可以提供的第一个级别。然后，事实证明，将可视化工具放在一起通常会有更多的工作，当你开始说让我们构建服务的可视化表示，搞清楚用户界面，进行可用性测试等等，所以为了不延迟服务的发布，我们通常会首先发布服务和命令行工具，然后我们为可视化管理工具制定一个单独的时间表。但我们当然尊重、欣赏第三方管理工具，并做了大量工作来帮助第三方管理工具，但我认为这确实指出了当我们构建我们的工具时，我们是在第三方所做的相同 API 之上构建的。

**Kevin:** 我们有一个来自罗伯·莫洛的问题，他问“那么它到底值多少钱？”你大概了解了亚马逊向你收取的费用，但是我想你能给我们的听众一个大概的概念，至少运行一个单实例微服务器，如果你只是——你需要一个服务器来开发和试验，我们在这里谈论的成本是什么？

杰夫:哦，让我想想，我真的不擅长心算，所以微服务器每小时 2 美分，所以基本上每天 50 美分，所以我们说的是全职访问每月 15 美元。

凯文:是的。

**Jeff:** 现在，带宽的进出是分开收费的，目前是每千兆字节 15 美分，所以只要有一台服务器启动并运行，然后有一些单独的存储，我们可能会看到每月不到 20 美元的最小服务器。现在，如果您打算长时间使用这些资源，或者如果您可以预测您的使用情况，我们有一个名为保留实例的模型，您可以预先支付基本的保留容量，然后一旦您这样做，您的小时费率就会降低，当您计算出 100%使用或 50%使用的成本时，净价甚至会更低。

凯文:好的。罗伯，谢谢你的问题。如果您有任何更具体的成本问题，请随时提出后续问题。

**Jeff:** 我们在网上也有一个计算器，如果人们在谷歌上搜索“AWS calculator ”,有一个很好的 JavaScript 计算器，你可以运行它，你基本上可以模拟不同的场景，有多少服务器，你消耗多少存储和带宽。

**Kevin:** 在这本书的这一点上，我们进入了一些实际的具体服务，以及它们可以为您做什么，您看到的第一个是 S3，简单的存储服务。

杰夫:对。S3 是我们早期的服务之一，我认为当我们推出这个服务时，它真的引起了人们的注意，他们说，嗯，这根本不是我们希望亚马逊做的事情，所以我们真的有两个服务，就像手套中的手一样，我们有 S3，这是对象的存储，我们有 CloudFront， 这就是内容分发，它在世界各地有许多节点，基本上是为了让这些节点离您的用户非常非常近，以便他们可以以低延迟非常快地获得内容。 所以在这里，我基本上解释了 S3 和 CloudFront，我浏览了所有的基础知识:创建一个称为 bucket 的集合，向您展示如何列出您的 bucket 的内容，将文件上传到 bucket，做一些有趣的事情，如使用一些简单的图形处理从 bucket 下拉图形，创建缩略图大小的图像并存储它们，然后使用 CloudFront 进行分发。作为一名开发人员，我发现非常有用的一点是，当我有一个主要任务要完成时，我肯定会朝着那个任务努力，但我不害怕向旁边迈出一步，同时探索其他有趣的事情。所以当我在为 S3 编程的时候，我说让我学一点关于图形处理的知识，这也是我进入图钉的原因。

凯文:卢卡斯，我们用 S3 做了哪些事情？

**Lucas:** 使用 S3 的一个很好的例子是 so [99designs](http://99designs.com/ "Logo Design, Web Design, Design Contests | 99designs.com") ，这是正式的 SitePoint 竞赛，我们存储竞赛提交的作品，因此由设计社区提交的徽标和网页设计，我们将它们存储在 S3，然后我们另外存储这些提交作品的缩略图和大小调整版本。因此，截至今天上午，我们大约有 590 万个对象存储在 99designs S3 帐户中，所以我们当然是在谈论大规模和真正可靠的后端存储。话虽如此，我们倾向于不让我们的用户直接从 S3 获取对象，尽管这肯定是可能的，只是因为我们喜欢对如何缓存和诸如此类的事情有一些更细粒度的控制。

凯文:S3 让我着迷的是，它几乎回到了 web 基础设施的起源，当 web 第一次被构思出来，当像 Apache 这样的服务器第一次被编写出来时，它们主要是为静态资源、HTML 文件、某些情况下的图像文件而设计的，这些东西只是，你知道，web 浏览器会要求一个 URL，Web 服务器会去获取文件并将其发送给浏览器，这并不需要比这更复杂。这些年来，我觉得 web 服务器已经发展成了这种完整的脚本、编程、动态生成页面类型的东西，但 S3 似乎在说，等等，为静态对象提供服务仍然很重要，这些系统为动态生成页面设计得越多，就越不理想，你花在为这些静态东西提供服务上的钱就越多，所以为什么不设计一些专门为静态设计的东西呢。

杰夫:没错。此外，随着您开始接触越来越多的对象和大量的数据，您必须自己花费大量精力来构建存储基础架构、备份基础架构和内置冗余等，这将很快变得非常复杂，当您开始谈论近 600 万个对象时，我甚至不知道这代表了多少数据， 但是，当您突然意识到单个磁盘无法容纳这些数据时，我的备份会变得非常非常困难，在快速增长的网站上，您会突然遇到这种非常非常不舒服的情况，因为数据的流入速度超出了您的想象。 因此，能够将它发送到云，而不必担心磁盘已满错误和诸如此类的事情，这确实令人欣慰，并简化了整个应用程序构建过程。

**Kevin:**Giovanni Castillo 向我们提出了一个关于 S3 的有趣问题，他说，“我有一个受用户名和密码保护的系统，”他将文档上传到 S3，然后其他用户可以从系统中查看或下载这些文档，但他不想将这些文档作为公共文档。我认为这是一个谈论 S3 灵活性的好机会。你怎么能在不完全公开的情况下提供来自 S3 的东西呢？

杰夫:当然可以。因此，S3 有一个完整的 ACL 或访问控制列表模型，并且有多种方法可以保护您的内容。你可以做的一件事是，实际上你可以设置系统，这样每次你为特定用户呈现一个页面时，你会生成一个唯一的有时间限制的 URL，它只在非常非常短的时间内有效，所以当你向用户交付内容时，即使他们复制 URL 并交给其他人，它也会过期，在一定时间后它将不再有效。我们还有一个相对较新的服务，它实际上比我在书中能够涵盖的还要新，称为 IAM，即身份访问管理，通过身份访问管理，您还可以非常非常精细地控制对对象的访问，对单个 S3 存储桶和 S3 对象集合的访问，甚至对单个 S3 API 的访问。你可以设置保护，比如说，这些特定用户可以调用一些 API，其他用户只能使用这些 API 的不同子集，因此 S3 提供了许多细粒度的控制。

凯文:酷。接下来让我们谈谈 EC2，对我来说，EC2 是亚马逊网络服务的核心。杰夫，你觉得是这样吗？

杰夫:我想是的。因此，尽管有所有这些存储数据、将数据移入移出和传递消息的出色服务，您仍需要做一些处理，因此您几乎总是需要一些 EC2 作为核心。因此，在这一章中，我将讨论可编程数据中心，即通过 API 进行资源分配的想法。我讲述了 EC2 的许多重要方面，然后我尝试快速实践，向您展示如何使用 AWS 管理控制台启动一个实例，向您展示如何使用 Putty 实际 SSH 到您的实例中，然后一旦我们有了这个基本实例，我们就可以做一些事情，例如分配一个弹性 IP 地址，这样它就有了一个永久 IP，您可以在扩展或缩减时重新映射到不同的实例， 弹性块存储，基本上是您在云中分配的虚拟磁盘卷，然后连接到您的实例，创建自定义的机器映像，然后使用 API 来执行一些我认为非常令人兴奋的动态处理。

凯文:太好了。Lucas，是 EC2 真正吸引了我们去 Amazon Web Services，然后我们发现了从那里出去的所有东西吗？

卢卡斯:是的，可以这么说。我记得我们真正想做的事情之一是实验，或者真正快速地部署 PHP5 代码库，让我们的客户作为 SitePoint 竞赛的测试版来测试，我们真的能够用 EC2 非常快速地做到这一点。如今，我们使用小型、大型和高 CPU 实例的组合，这对我们来说非常好，因为我们可以针对各种应用程序使用不同的实例大小，无论它们是否是 CPU 密集型的。Jeff 谈到了创建自定义 ami，即机器映像，我们在 SitePoint 上不太会这么做，我们只是发现在机器启动时使用用户数据自定义这些机器会方便得多，一旦机器完成启动过程，用户数据就会传递到机器中。另一件事是——我在谈到 S3 时提到过——我们倾向于不直接从 S3 提供太多数据，我们基本上直接从我们的 EC2 实例提供大部分数据，他们在这方面做得很好。是的，我认为这基本涵盖了 EC2 的使用。

凯文:好的。我们有几个问题，是关于这里出现的一些首字母缩写词，EC2 代表弹性计算云。也许 Jeff 可以快速后退一步，描述一下什么是 EC2，什么是 EC2 服务？

杰夫:当然可以。因此，EC2 本质上是每小时获取运行 Linux 或 Windows 或 Solaris 的服务器的能力。因此，您从云中分配这些服务器，当您发出请求时，您指定一些不同的内容，如实例类型，实际上有 10 种不同的实例类型，它们的内存量各不相同，从微型实例到内存高达 70gb 的非常非常大的实例。您可以指定实例类型，无论您想要 32 位还是 64 位机器，您可以指定您想要在世界上的哪个地方运行，我们目前在世界上的四个不同地方有区域或数据中心集群，以及一些其他参数。所以你启动这些服务器，在启动请求的同时，你指向一个叫做 AMI 的东西，一个 Amazon 机器映像。所以 AMI 实际上和你的 C: drive 或者你的 Linux 系统的根盘是一样的，它有你的操作系统，它有你预先构建的应用程序，无论你想在那里包含什么静态数据。我们给你一个长长的预先构建的 ami 列表，我也在书中向你展示如何构建你自己的。因此，一旦您启动 EC2 实例，我之所以说实例，是因为我们有一些客户为他们的应用程序启动了数百甚至数千个实例。您的使用是按小时计费的，所以我们实际上是按时钟时间计费，所以如果您启动并使用了 24 小时，您将为该实例支付 24 倍的每小时价格。

凯文:酷。好的，我认为我们可以从 EC2 继续，我相信我们会有更多的话要说，我们即将讨论的许多主题都与它有关，但让我们转到第六章，这是关于 SQS 的。那么 SQS 是什么？

**Jeff:** 好的，SQS 是简单队列服务的缩写，当我们在亚马逊描述一些“简单”的东西时，我们并不意味着它只是小婴儿应用程序或玩具应用程序，我们的意思是编程模型、思维模型和界面被设计得非常干净、非常简单，你可以非常、非常快速地理解、吸收并开始使用。在这一章中，我将介绍使用 SQS 的几种不同方式，SQS 是一个先进先出的消息队列，您可以使用它们来构建一个非常灵活的处理系统，就像您可以构建一个应用程序管道，从进程 1 到进程 2 再到进程 3，您可以在每个处理步骤之间使用一个队列将这些不同的步骤粘合在一起。因此，如果一些步骤非常简单，只需要很短的时间，而一些后续的管道步骤非常复杂，可能需要很长或可变的时间，那么您可以使用消息队列作为不同管道步骤之间的缓冲区， 您还可以构建您的应用程序，这样，如果某个管道步骤有点滞后，而队列正在备份，您只需启动另一个服务实例，然后您就有了两个应用程序，两个相同的应用程序从队列中提取数据进行处理，然后沿途发送结果。

所以，我实际上写这一章很有趣，我犹豫要不要选一个最喜欢的章节，但如果必须选的话，我可能会说是第六章，我建立了一个非常简洁的处理管道，在那里有一个小队列，我只需将一些 URL 放入那个队列中的网页，然后我会通过几个步骤获取这些 URL，在那里我会获取页面的 HTML， 我会解析它，我会识别页面中的图像 URL，获取每个 URL，收集单个页面的所有图像，将它们转换成小缩略图，然后制作一个具有代表性的页面，该页面有多达 16 个缩略图。 因此，一步一步地构建这个应用程序真的很好，我确定了我想要做的一般事情，然后我一次一个管道阶段地构建它，我会加载一个队列，运行应用程序，测试它，将输出放入下一个队列，然后沿着处理管道设计和构建下一个步骤。

凯文:是的，我必须承认 SQS 是一个当它被宣布的人，我想作为一个普通的网络开发者，这是我看着它的服务之一，嗯，我真的看不出这对我有什么用，他们做出来很好，但这是我需要在 NASA 工作的一部分，以便让我受益。但是，卢卡斯，你们 99 设计公司的人很快就发现了 SQS 的用处，不是吗？

卢卡斯:是的，我们喜欢消息队列；我们总是在谈论他们。采用队列的最大好处是我们不再需要让用户等待。所以，你知道，以调整图像大小为例，当用户上传图像到我们的网站时，我们不会让他们等待 PHP 脚本调整图像大小，然后将他们重定向回确认或成功页面。我们所做的只是将任务转储到消息队列中，用户可以离开并继续在我们的站点中导航，在后台，我们有一些 EC2 实例监视这些队列并执行这些任务，如图像大小调整或日志记录和维护操作等，所以真正的关键是我们不喜欢让用户等待，只要有可能，我们会将任务发送到队列中，让我们的一个工作机来处理它。所以，我们使用队列的其他例子是 cron 任务、事务处理和其他数据处理和再处理操作，这些都是我们不希望应用服务器处理的。因此，如果我们遇到队列变得非常繁忙或正在备份的情况，我们需要做的就是启动一个新的 EC2 实例，该实例被编程为监控这些队列并执行我们交给它们的任务。

凯文:所以在我看来，一旦你把你的网站转移到云托管平台上，SQS 是第二个值得关注的地方。如果你现在在一个共享的主机环境中建立网站，这可能对你来说有点像另一个世界，但是一旦你把东西转移到云主机上，你就会问，好吧，也许我节省了一点钱，或者也许我花了同样的钱，但是得到了更多的灵活性，但是还有什么，我还有什么其他的好处。如果你看看 SQS，这是你在云托管基础设施上得到的东西之一，你不一定在更传统的基础设施上得到，可以真正影响你给你的用户的用户体验。如果你说，哦，对了，在我把下一个页面发送给他们之前，我不需要做所有来自特定 web 浏览器请求的处理，这是什么意思，这将如何改变和改善用户体验，特别是对于那些使用 Ajax 技巧来感觉更像应用程序的网站；这种灵活性变得非常重要。

杰夫:没错。这是其中之一。我想我在书中用了几次“思维云”这个短语。对我来说，思考云是一种渐进的变化和思考过程，在这种过程中，你会从拥有固定数量资源且主要从同步处理角度思考的传统模式，转变为基于云的模式，在这种模式中，你会从可变资源和异步处理角度思考，并且能够变得更加动态、更具适应性。因此，在这种情况下，我们可以适应工作负载，我们可以适应这样一个事实，即有时可能需要比其他时间更长的时间来进行页面获取或渲染，我们的网站不会因为其中一些过程花费太长时间而停滞或过载，因为这些可变影响过程中的任何一个都只是在队列中，队列将扩展到任何必要的大小，工作将以任何速度进行。

凯文:我想快速进入下一章，但是我们有一个来自马特·s 的问题，他问道:“SQS 的信息持久吗？”为了我们听众的利益，你可能应该在回答这个问题之前详细说明它的意思。

**Jeff:** 好的，是的，所以消息是持久的，当你向 SQS 发布消息时，它会被冗余地存储，然后在那里保存一段可配置的时间。基本上，你把它放在那里，它可以使用几天，直到它从队列中过期。现在，当您从队列中取出一条消息进行处理时，会发生一个非常非常有趣的过程。当您获取该消息并准备开始使用它时，它实际上并没有从队列中删除，而是暂时对任何其他可能试图从队列中提取的应用程序不可见，这样就给了您一个时间窗口，在此期间您可以实际处理该消息，然后将它传递给接下来应该发生的任何事情。如果您要从队列中取出该消息，并且由于某种原因，您的应用程序由于某种随机原因倒下并死亡，在该消息的时间限制到期后，该消息将重新出现在队列中，并且另一个应用程序可以捡起它并继续。因此，这也是在系统中建立可靠性的另一个要素，这样您就不会在处理消息的过程中意外地让应用程序死机，而让消息丢失。

凯文:好的。让我们转到第七章，这是关于监控、伸缩和弹性负载平衡的。对我来说，这是我对进入 Amazon Web Services 感到非常兴奋的东西，因为它解决了许多在更传统的基础设施上工作的问题。

杰夫:没错。因此，在这一章中，我实际上是从单个服务器、单个主机的概念发展到本质上进行扩展的概念，这样您就可以有几个并行服务器都运行相同的代码，这样当您有 web 流量进入时，您可以使用负载平衡器将流量分散到任何一个可用的服务器。然后，您可以查看所有服务器，通过使用我们名为 CloudWatch 的服务，您可以使用自动扩展功能来判断正在运行的服务器集，这些服务器上的负载过高，我只需启动另一台服务器，将其放入负载平衡器，然后在这些服务器之间进一步分配流量。因此，当你开始考虑我如何处理大量的流量，我如何建立一个网站，实际上可以扩大和缩小，以便我只使用足够数量的资源，而不是使用太多，你开始考虑自动扩展和负载平衡来实现这一点。在传统的情况下，传统的托管，这是非常非常复杂的，你可能需要让外人来帮助你设置和配置它，但有了 EC2，你只需从命令行或你去控制台，点击几下，你就可以设置自动扩展和负载平衡，所以它更容易做到，它更简单。我在本章中做的另一件事是，我想实际演示如何通过测试来确保某些东西可以扩展，所以我使用了一个叫做 Apache JMeter 的开源应用程序，设置我的应用程序进行自动扩展，并使用 JMeter 向该系统发送可变负载，然后观察自动扩展是否如我所愿。

凯文:酷。Lucas，你想展示一下我们在 SitePoint 和 99designs 所做的监控工作吗，以及它是如何为我们工作的？

卢卡斯:是的，你们现在可以看到我的屏幕了吧？

凯文:是的。

卢卡斯:好的。我们目前看到的只是运行 99designs 的应用服务器上的负载的可视化，这是过去 24 小时的情况，所以现在是上午近 10 点，我进入办公室后通常会在这里加载这个界面，这是在 RightScale 上运行的，并检查应用服务器昨晚在做什么。

**Kevin:** 对于不熟悉 RightScale 的人来说，这是一项第三方服务，用于监控和自动扩展一组 EC2 服务器。

**Lucas:** 是的，没错，亚马逊的 AWS 控制台也是这么做的。RightScale 的另一个功能是它让我们能够构建，它们被称为服务器模板，它们基本上是服务器启动时运行的脚本或指令的集合，因此它们有一个非常好的界面来管理基本的服务器配置和服务器启动过程，并且是受版本控制的。我今天不会展示这一点，但我真的想展示的是，这是我们可以获得的监控类型，我们可以回顾过去一年、一个月或一个季度的情况。如果我们看一下，我将切换到另一个选项卡，我可能要做的事情之一是查看 99designs 的数据库层，这是上个月的情况，我们可以在这里看到顶部的图表是我们的数据库主服务器的 CPU 使用情况。您可以看到，CPU 使用率在过去几周一直在增长，因此我在这里可能会与开发人员一起查看，您知道，可能会查看他们的源代码控制提交日志，并查看大约在第 36 周发布了哪些内容，从而开始了 CPU 使用率的增长。因此，拥有这样的工具真的非常非常方便，正如我所说，亚马逊控制台确实提供了这一点；我们目前主要使用 RightScale，我们还尝试了另一项名为 CloudKick 的服务。这大概就是我们所使用的监控功能。

凯文:好的。我们有一个问题，我想你可以回答，卢卡斯；"这种自动伸缩如何与数据库一起工作？"“例如，如果有很多用户评估一个数据库，并像在论坛上一样添加帖子，那么数据库会成为瓶颈吗？”这是贾斯汀·埃雷拉写的。

**卢卡斯:**嗯，这是个好问题。根据数据库的类型和使用情况，答案通常会有很大不同。举例来说，一个论坛通常会被大量阅读或者被大量查询，所以，你知道， 横向扩展该层的选项是使用主服务器和从服务器配置，将读取查询发送到从服务器，有效地保护或屏蔽主服务器，让主服务器处理`INSERT`和`UPDATE`。接下来，您可以转到有多个从服务器的配置，因此基本上有多个从服务器连接到主数据库服务器，从主数据库服务器进行复制，然后在这些从服务器之间平衡`SELECT`查询的负载。 我想确实会有这样一种情况，您需要处理主数据库上的负载，您知道有几种方法可以解决这一问题，您可以垂直扩展并切换到高 CPU 主数据库，或者增加 I/O 容量，然后您可能会考虑对主数据库进行分区，这样您就可以将数据库表拆分到两个主数据库上，并从那里开始。

凯文:嗯，对于 SitePoint 和 99designs 这种规模的网站来说，卢卡斯，数据库读取有时会成为瓶颈，我们不得不进行扩展，但数据库写入对我们来说还没有达到这一点，这样说公平吗？

卢卡斯:是的，我认为这基本上是正确的。例如，flippa.com 的读取量非常大，最近我们针对数据库从服务器进行了垂直扩展，因此我们没有再添加一个数据库读取器，而是选择了多读取器配置或多从服务器配置，而是选择了垂直扩展。然而，有时候，我想，Kev，你可能还记得处理会话更新的时候

凯文:哦，耶！

**Lucas:**—发送给一个 DB master，我们可以使用 MySQL 内存表绕过它。但是有时候你会意外地看到大量的`INSERT`和`UPDATE`查询进来，并且有各种各样的方法来处理，不过我要说的是，这真的是一个案例一个案例地来。

凯文:酷。Matt S 又问了我们一个问题。进来，这边是关于缩放的问题。他问道，“是否可以将内容/设置从主节点自动复制到服务器场？杰夫，你想粗略地谈谈这类事情的策略吗？

杰夫:当然可以。对于这个问题，我可以用半本书的篇幅来讲述你可以采取的不同方法。其中一种方法是使用 Amazon Simple DB，我们接下来会谈到它，主要用于存储配置和设置。另一种方法是，在设置 EC2 实例时，您还可以创建这些虚拟磁盘卷，称为 EBS，即弹性块存储卷。创建这些快照后，您可以转到控制台，只需单击即可将它们转换成快照。然后，您可以在每次启动新实例时，也从这些特定的快照创建一些磁盘卷。因此，如果您有参考数据或只读数据，您可以在每次启动其他实例时创建虚拟磁盘的新副本。

凯文:是的。这是一个非常灵活的系统。我想，对于这种事情，你能给出的唯一明确的答案是，你有很多选择，而且有很多人愿意为此给你建议。

杰夫:听起来差不多。

凯文:所以让我们继续第八章，亚马逊简单数据库，我必须承认我没有任何这种特殊服务的经验。我不知道我们是否在 SitePoint 的任何地方使用它。卢卡斯？

卢卡斯:你知道，是的，我们在这方面没有太多经验，但是当我们试图写一个谷歌分析的复制品时，我们确实非常乐观地使用了它。这是一个疯狂的想法，很有趣，我们不再使用它了，但从我们的角度来看，简单的 DB 无疑是这类事情的正确选择。

凯文:好的，让我们来看看简单的数据库杰夫？

**杰夫:**因此，Simple DB 被设计成一种非常干净、非常简单的方式，让你可以存储大量数据，而不必担心很多细节。您只需创建一个存储结构，我们称之为域，然后开始在该域中存储一组属性-值对的每一行。Simple DB 的优点是，在数据库的每一行中，您不需要有相同的属性名，因此，随着应用程序的变化，您可以非常灵活地更改数据结构。就像当您有一个传统的关系数据库时，您需要花一些时间预先设计您的模式，如果您用几十万或几百万行填充您的表，然后您说您有一个好主意，您说，好的，现在让我们更新数据库以向它添加一个新列，这实际上会使您的数据库离线几个小时。因此，简单数据库模型通过让您拥有这种灵活的存储结构来避免这种情况，它是动态的，更能适应您的需求。现在，它被称为 NOSQL 或非关系数据库，所以你总是专注于单个域，它有一个很好的`SELECT`模型，但它没有连接模型，所以你总是从单个域中提取数据。在本书中，我给出了一个处理 RSS 提要的例子，并且我展示了——我利用了灵活的模式，因为不同的 RSS 提要在标题信息中有不同数量的细节，对于没有包含额外信息的提要，我根本不需要存储任何内容。

Kevin: 我知道 SitePoint 的开发人员对 NOSQL 有些激动——我想你几乎可以称之为一场运动。卢卡斯，你对此有什么看法？

卢卡斯:实际上，基本上，这就是适合工作的工具。

Kevin: 因此，在某些特定类型的应用中，放弃数据库连接可以为您带来性能、灵活性；你得到了什么回报？

卢卡斯:是的，性能、灵活性，还有基本上处理大量数据的能力。是的，基本上有一个点，当你谈论千兆字节和兆兆字节的数据时，关系数据库将开始崩溃。

凯文:酷。关于 Simple DB，你还有什么想说的吗，Jeff？

**Jeff:** 我真的很喜欢它，因为它消除了一些简单应用程序的规划，您只需创建域并开始将数据放入其中。你当然要给组织一点点想法，但不是太多的想法，你不必担心被锁在一个结构里，然后想，嗯，我需要回头修改或改变这个结构；你有这种运行时的灵活性，可以根据需要进行更改，所以它可以解除你的思维限制，让你在创新和扩展应用程序的方法上更加灵活。

**Kevin:** 对我来说有趣的是，这些服务中的每一个都有不同的种类，我想在传统世界中，你的系统管理员所做的托管和服务器工作与你的开发人员所做的开发人员工作之间有着非常清晰的界限。许多这类服务确实融合了这一点，并在黑与白的世界之间制造了灰色地带。这种感觉就像一种中间状态，一方面，它可能会真正简化您的系统管理员的工作，因为他们不必维护，比如说，可以处理海量数据的 MySQL 数据库服务器，但在编程方面，它也会真正影响您正在做的事情；如果您打算使用 Simple DB 这样的数据库而不是 MySQL 这样的数据库，这将改变您编写应用程序的方式。

杰夫:没错。对我来说，这反映了我在初创公司和较小组织中一直看到的情况，即在较大的组织中，你有开发人员，你有运营人员，你有数据库专家，你有所有这些不同的专业，每个人都可以贡献他们独特的知识来构建应用程序。在较小的组织中，尤其是在初创公司中，你根本没有那些不同的人在场；通常，一个人必须能够成为所有行业的万事通，并且必须能够构建、设计、部署、操作、监控和维护。因此，我们肯定混淆了开发和运营，我认为现在有一个短语叫做“开发运营”，它实际上概括了开发人员通常是运营人员的想法。

凯文:是的，我们刚刚收到马特·s 的一个问题，问你是否需要成为一名网络或服务器技术人员来使用这项服务，这种云托管的东西。是的，我认为你是对的，这是开发者会感兴趣的那种主机。它把你的主机变成了一个有趣的开发工具，而不是你需要雇人来照顾的东西，这样你的开发者就不用担心了。

杰夫:我认为这非常准确。一旦您开始考虑您的基础架构本身是可编程的这一事实，并且您认为我可以进行函数调用来创建虚拟磁盘，或者我可以进行函数调用来创建一个全新的服务器，这确实会影响您的想法，即我可以让我的应用程序根据需要影响、更改和改进我的基础架构，而不是让我的应用程序适应我的基础架构。

凯文:所以第九章，RDS。

杰夫:好的。因此，RDS 是最新的服务之一，这是关系数据库服务的简称。这里的想法是，我们采用了开源的 MySQL 数据库，如果你愿意，我们在它的下面和上面放了一些包装器，来处理实际运行关系数据库的许多更复杂的问题。所以你不用担心买硬件，安装一个 OS，安装在 MySQL，处理 MySQL 补丁，处理备份，处理伸缩，处理用完磁盘空间；所有这些都简单地内置在服务中，只需在控制台上点击几下，就可以创建一个完整的完全可操作的 MySQL 数据库。一旦启动并运行后，您可以轻松地再次点击并进行快照备份，您可以增加或减少您的处理能力，您可以添加额外的存储，您可以从您的快照备份中创建新的数据库实例，因此，运行 rational 数据库所需的所有不好玩的细节和令人头疼的事情都得到了真正的简化，只需在控制台中点击几次。

Kevin: 因此，这不是简单数据库和 RDS 之间的非此即彼的选择，在给定的应用程序中，您可能希望对一种类型的数据使用简单数据库，而对另一种类型的数据使用 RDS，并且您可能会同时使用这两种服务。

杰夫:没错。对我来说，AWS 的一个强大的属性就是你有所有这些选择的事实。我们今天已经讨论了一大堆不同的服务，但是您可能会选择只使用现有的应用程序，只使用 EC2，查看所有其他的应用程序，然后说我现在不需要这些，但是然后可能会启动并运行您的应用程序，然后当您考虑如何发展它、扩展它并向前推进它时，您可以说，好吧，我会一次一步，一次一个服务地开始采用它的不同方面。

**Kevin:**Lucas 之前说过，按照我们的方式，将 MySQL 作为 EC2 服务上的实例集合进行扩展是相对简单的。如果您只需要为`SELECT`查询提供更多容量，那么扩展只读查询相对容易，但是一旦您需要将写入端扩展到`INSERT`、`UPDATE`、`DELETE`等查询级别，事情就会变得更加复杂。那么，当你遇到这种事情时，这是一个自然的选择吗？好的，我们将停止管理我们自己的 MySQL 实例，我们现在将关注 RDS 之类的东西。

**Jeff:** 等到你处于恐慌的情况下可能不是一个好主意，因为人们会(笑)——我总是喜欢这样——我更像是一个计划者，每当我对一些事情感到惊讶时，我总是在精神上打自己一巴掌，说一些我应该预料到的事情。但是使用 RDS，您实际上有五种不同大小的实例可供选择，从 1.7 GB 的 ram 到 68 GB 的 ram。因此，如果您从小型设备或比小型设备稍大一点的设备开始，您就有了很大的扩展空间，可以随着需求的变化增加额外的处理能力。当你开始说，嗯，我们必须提供的最大的一个，你可能会看到达到它的极限，你需要真正开始考虑添加缓存，也许你需要开始考虑以各种方式绘制数据库，这些都是深入的架构决策，这些不是你想在晚上突然变得繁忙时不得不做的事情。

凯文:令我兴奋的是，这本书确实进入了这个层次，这些选择不是你在云托管冒险的第一天就要做出的，而是可能在一年后，甚至可能在两年后，这些都是你将会看到的东西，然而这本书涵盖了它，它为你提供了一个完整的冒险画面，如果你现在正考虑潜入云托管，你可能会继续下去。

杰夫:这就是我的想法。我想让人们很容易开始，但希望在未来几年里可以作为参考。

凯文:酷。为了我们听众的利益，是的，我们跑得有点长，现在是十点过五分。我们计划去一个小时，但我们在这里玩得很开心，没有人真的要离开，所以请不要离开我们；我想说我们还有 15 到 20 分钟，我们还有大约两章要读完，我想确定我们有时间回答你们所有的问题。说到这里，我们确实有几个关于数据库层的问题。我们已经有点拐弯抹角地回答了其中的一些问题，但是我想确保我们得到直接的答案。Peter Gotkindt 问道:“简单数据库比 MySQL 快吗？”

杰夫:啊，所以我们总是喜欢听到这样的回答，“视情况而定”，当然这真的，真的取决于这种特定的情况。因为 Simple DB 自动为您做的事情之一是，当您存储数据时，您不必像在关系数据库中那样选择您想要有效地索引哪些列。简单数据库只是对所有列进行索引，在编写时实际做这些会有一点成本，我说的是处理成本，实际做这些额外的索引，但你不必担心当你做查询时，你不必考虑我是否记得索引这个特定的字段。因此，Simple DB 在许多不同的情况下都被调整得非常非常灵敏。在关系数据库方面，这对于任何关系数据库来说都是一个特性和问题，您可以对数据库运行的各种参数进行精细控制，因此您可以选择要索引的内容和索引类型，您通常可以选择表类型，您可以为缓冲区大小以及不同的缓存、刷新和索引选项选择许多参数。因此，我认为简单的 DB 将为您提供直接的性能，并且不需要太多的调整。RDS 将为您提供开箱即用的良好性能，我想当您在云中时确实没有开箱即用的性能，但当您启动时会有良好的性能，如果您的工作负载在某些特定领域非常非常特殊，您可以进入 RDS 参数组，并开始微调内存分配等。

凯文:酷。Jason Mays 问道，“那么这是否意味着我们必须重写我们的网络应用程序来使用几个数据库，或者亚马逊是否支持类似 XERound 的东西？”我对 XERound 不熟悉；卢卡斯你知道这件事吗？

卢卡斯:我对它也不熟悉，但我认为他的意思是，我认为该产品有点像 MySQLProxy 或类似的东西，它分析你的查询并确定它们应该被发送到哪个服务器。

凯文:好的，也许你可以谈谈当我们第一次进入亚马逊时，我们需要在应用程序中做些什么来访问数据库，我们需要做些什么改变？

**Lucas:** 是的，所以我的意思是，当时它真的迫使我们思考许多不同类型的故障场景，并修改我们的应用程序来应对这些情况。具体到数据库层，我们必须修改我们的数据库库，以便将`INSERT`查询— `INSERT`、`UPDATE`和`DELETE`发送到主服务器，如果我们想要这样做，则将读取发送到从服务器。我不认为这是一个巨大的变化，虽然，就像我们的应用程序的具体变化，所以有选择；如果你在运行一些开源的东西，比如 vBulletin 或 WordPress 之类的，我想他们有一些插件和其他各种修改，你可以很容易地处理这些东西。是的，我想这就够了。

凯文:酷。杰夫，你想详细解释一下这个答案吗？

杰夫:当然可以。还有一点需要考虑的是，当您启动 EC2 实例时，那些只是普通的旧 Windows 或 Linux 服务器，如果您不想运行 MySQL，如果有另一个开源或商用数据库，您可以直接在 EC2 实例上安装并运行它。我们实际上与许多商业数据库供应商有关系，我们有 DB2，我们有 Postgres，我们刚刚宣布了与 Oracle 的关系，他们实际上允许您在云中启动 Oracle 数据库实例，许多不同类型的东西，您可以使用 RDS 并为您完全管理一切，或者您可以简单地将现有代码带到云中并在那里运行。

**Kevin:** 好的，这算是回答了 Giovanni Castillo 的最后一个问题，“但是我知道除了 RDS 之外，我还可以通过其他方式在我的应用程序中访问 MySQL 服务器，我可以/应该这样做吗？”这又是一个“视情况而定”的问题，但是您刚才说，Jeff，如果您想简化向云的迁移，那么在大多数情况下，您可以在云中复制您在传统基础架构中使用的任何东西，然后一旦启动并运行，您就可以开始考虑是否要重新设计，以利用您的云托管带来的一些独特优势。

杰夫:我当然会这么做。我非常支持一次改变一件事。做出一个改变，确保你对它感到满意，确保你完全理解它是如何工作的，它的特点是什么，然后第二天或下一周做出第二个改变。但是，当你有太多的变量时，你会变得不知所措，因为你试图一次掌握太多不同的新事物。

凯文:好的，太好了。因此，我认为我们已经准备好进入第十章，即高级 AWS。我喜欢一个很好的高级章节，但就我自己而言，如果你现在托管的是一个共享的托管账户，那么我们已经讨论过的很多东西就感觉很高级了。那么什么比我们已经看到的更先进呢？

**Jeff:** 所以，这本书的另一个标题应该是“这是我希望在之前的几章中介绍的内容，但我没有足够的空间来介绍它，但它太酷了，不能从书中漏掉”，所以这不符合这一行(笑声)。因此，许多人总是问我，如何才能更详细地了解我是如何使用 AWS 资源的，我举了一个例子来说明如何下载我们所有服务使用情况的 CSV 记录，将它们存储在简单的数据库中，然后我还会向您展示如何使用简单的数据库查询以不同的方式处理这些数据。我向读者展示了如何使用弹性块存储；为了更大的卷或额外的吞吐量，在 EBS 之上实际创建 RAID 卷。有一个非常有趣的想法，叫做实例元数据，当您启动 EC2 实例时，您可以将数据传递给它们，您还可以询问您的环境，以了解您的服务器在哪里运行，它的 IP 地址是什么，我将向您展示如何访问它。然后，我做的最后一件事，我承认这很有趣，是我想再次谈论整个可编程基础架构模型，这一次不是创建基础架构，而是实际查询云，并问“我到底在运行什么？”我向读者展示了如何构建一个小的图表应用程序，它调用云并告诉我一个 EC2 实例的列表，第二个调用告诉我一个所有 EBS 虚拟磁盘卷的列表，然后第三个调用给我一个我从这些卷中获取的所有快照或备份的列表，然后用一些非常， 非常简单的图形操作和 PHP 的 GD 库我只是画出一个图表，上面写着这是我的服务器，这是连接到每个服务器的磁盘卷，然后这是我们对每个卷的所有快照备份。 这是一种非常简单但我认为非常非常酷的库存应用程序，它只是简单地说，我们可以向云询问我的资源是什么，我可以找到我需要了解的所有资源。

凯文:是的，很好。我想玩这个东西很有趣，然后想，哦，我能让它做什么，你知道；哦，看，有一份报告给了我这个，我能用它做什么。

杰夫:我承认我们一起写这本书的时候有很多乐趣。

凯文:卢卡斯，你对那件事有什么要说的吗？你们有没有什么有趣的周末或深夜实验想告诉我们，你们在那里做过的？

**Lucas:** 嗯，在谈到这个东西时，Jeff 谈到了我们正在使用或已经广泛使用的实例元数据。我之前提到过，我们倾向于不太使用定制的机器映像，而是倾向于将数据传递到实例中，这样当它启动时，它可以将自己配置为应用服务器或工作服务器或代理服务器或类似的东西。这实际上是一些很酷的工作。我们非常喜欢 EBS，或者弹性块存储，只是因为它给了我们如此大的灵活性，允许我们基本上克隆一个数据库服务器，而完全不干扰它的操作。是的，看，对于这一章，我真的没有太多要说的，真正让我兴奋的是——当我看着所有的 CPU 使用图表和负载图表等类似的东西时，我有点厌倦了，我绝对同意 Jeff 所说的一次做一个改变，因为看到细微的改变可以产生的差异是非常令人惊讶的，特别是当你有一个每天向服务器发布代码的开发团队时，事情有时肯定会变得令人毛骨悚然。

凯文:酷。我们还剩下一章，但我想提醒我们的小组成员，路易斯告诉我，我们有几个问题，他一直留着，这样我们可以在最后解决它们。所以我们不会在这最后一章花太多时间，请大家做好准备，迎接即将到来的一些通配符问题。对于我们的听众来说，是的，提问时间就要到了，所以如果你有一个你一直坚持的问题，现在将是一个发送它的好时机，这样我们可以把它放在我们的小组成员的队列中。所以我们来谈谈第十一章，也就是 CloudList。

**杰夫:**好的，所以这一章我真的想有一个完整的应用程序，向读者展示如何一起使用几种不同的服务来构建一个应用程序。在美国，Craigslist 网站非常非常受欢迎，所以我说我能不能做一个非常非常简单的分类广告系统，我可以在一个章节中记录并完整地解释它，结果是因为我有 EC2、S3 和简单的数据库，我用大约 500 行 PHP 代码做了一个简单但功能齐全的分类广告系统， 因此，我能够完成这一点，就像这一点的其余部分一样，这只是一种乐趣，将所有这些放在一起，设计我想要的数据外观，并设置一些命令行工具来创建新的州和新的城市，然后我有命令行工具来加载数据； 所以在我做任何网络工作之前，我就有了一套命令行工具，让我具备编辑和检查数据的基本能力。尽管我的网页设计技能非常非常差，但是在上面放一个网页前端还是非常简单的，而且浮动的背景颜色是我所知道的最花哨的颜色。

凯文:酷。让我们…这是你组装的一个 PHP 应用程序。我想解决这样一个问题:如果你不是 PHP 开发人员，如果你习惯于用 Python 或 Ruby 甚至 ASP.NET 这样的语言工作，你能从这本书中学到什么？

杰夫:所以我认为 PHP 一直吸引我的一点是它是一种可读性很强的语言。事情是被拼写出来的，不像其他一些语言，比如当我试图阅读 Perl 时，那么多的语义都被隐藏在表面之下，被看起来很有趣的标点符号调用，或者暗示你应该知道规则是什么，我认为 PHP 更明显，更简单， 因此，我确实认为，习惯于任何其他脚本语言的开发人员在阅读和理解 PHP 方面应该不会有太多的困难，而且我怀疑，即使他们一开始不懂 PHP，他们也可以在几章内开始编写一些应用程序。

凯文:是的，酷，我同意。好吧，让我们深入一些问题。我要去问杰夫和卢卡斯，请不要太客气，如果你有什么要说的，就在这里说吧。所以，第一个问题是从一开始，杰森·梅斯问道，“你能以某种方式限制你的每月预算吗？”"如果我喝醉了，我可不想收到 100 万英镑的账单."

杰夫:啊，所以目前我们还没有那个功能。它已经被请求了几次，我在那方面确实有一些不同的想法。首先，你可以随时登录你的 AWS 账户，你可以清楚地看到你每小时花了多少钱；你可以随时看到你的账单到目前为止是多少。第二，如果你的网站上有一些真正的内容，人们会来看，那么如果你真的把这个网站作为一个企业来运营，那么你大概就有一个基于页面浏览量和人们进入该页面后的行为的商业模式。所以我认为，云实际上是在告诉你，确保这种商业模式存在，确保你了解发布一个页面的实际成本，确保你有效地了解当你平均分配给大量访问者时，发布该页面将获得的收入，然后确保你的收入超过你的成本。现在这可能看起来有点过于简单，但当你真正考虑网络业务时，我们真正考虑的不仅仅是技术的扩展，我们还考虑业务的扩展。因此，你需要确保每个单独的页面视图是有效地自我维持的，平均来说，你将从那个页面视图中获得足够的业务来支付它所消耗的资源。

卢卡斯:是的，我认为把人拒之门外是一个很大的错误。

Kevin: 我们在 SitePoint 学到的一点是，可扩展性和性能可以为你带来流量。我们真的不知道在 SitePoint 有多少人被我们的旧服务器基础设施拒之门外，我们只知道有时我们的服务器有点慢，但当时我们没有真正简单的方法来解决这个问题。在迁移到我们可以扩展的云之后，我们学到的是，哇，在我们现在扩展的时候，我们的服务器曾经变得有点慢，但我们获得了更多的流量，因为我们的网站仍然保持快速，而在我之前认为在某些情况下我们可以赚最多钱的时候，我们的服务器却拖了我们的后腿，不仅拖了我们的后腿，而且对我们隐瞒了我们正在拒绝这些人的事实。

杰夫:那真的非常非常有趣。我想我们都知道，现在的网络访问者非常不耐烦，他们只是想出现，他们想在很短的几毫秒内获得他们的页面，如果他们不这样做，他们就会打开另一个标签，也许他们会回到原来的网站，但响应能力是一个有效的功能，你需要有一个快速响应的网站，以获得高质量的业务。

凯文:好的，我有一个非常具体的问题。这是来自大卫·卡斯特罗的问题，“我为一个小部件开发者提供咨询，他的工具包中有一个服务器组件，产品有点贵，所以我建议他们有一个付费的 AMI，亚马逊机器映像，按小时支付他们的产品；但是，如果他们需要在实例中托管的服务器上加载产品密钥，他们将如何防止客户将该文件复制到他们自己的服务器并盗用许可证？”

Jeff: 好吧，这是个深奥的问题，所以我应该解释一下付费 AMI 的概念。

所以，我们今天谈了一点关于 AMI 的想法，Amazon 机器映像，任何人都可以建立，他们可以在里面打包他们自己的软件，然后存储起来供重复使用，这样你就可以创建额外的服务器。现在，在您创建了其中一个 AMI 后，您可以选择广泛开放，以便任何人都可以找到它来启动副本，或者您可以单独向其添加用户，并有效地声明这是在此 AMI 上拥有启动权限的一组用户。现在，我们对此模型进行了扩展，我们称之为付费 AMI 模型，通过付费 AMI 模型，作为开发人员的您可以创建自己的机器映像，您可以在其中放置一些自己的专有应用程序或增值软件，然后您可以为机器映像设置自己的每小时价格。您可以说，您不需要支付每小时 2 美分或 8 美分的基本费用，或者您的用户使用的实例大小的任何费用，您可以说他们需要 4 美分、16 美分，无论您想要收取什么价格，然后我们会负责所有的会计和计费，以便当您的客户找到 AMI 并启动它时，他们会被收取您确定的价格，我们会向您支付我们的常规成本与您设置的价格之间的差额。这就是所谓的付费 AMI。

凯文:这很酷。我想接下来的问题是关于版权保护；一旦有人访问了你的付费 AMI，是什么阻止他们复制所有的软件并在免费的机器上运行呢？

**Jeff:** 当然，所以当你设置这个 AMI 时，你有——你设置了一个完整的机器，你可以决定我是否要给这些运行它的用户提供登录的能力。你可能会说它只是一个软件即服务应用程序，他们拥有的唯一权利是启动它的能力；您不必给他们一个 Unix 用户帐户和密码来允许他们登录，因此他们甚至可能无法访问文件系统，您只需简单地说运行这个二进制软件的副本，您就无法进入机器并以任何方式查看它。

凯文:酷。好的，我们有—这是一个来自 Egimonu 的更普遍的问题，他说，“运行日常业务服务的小公司能从云托管中受益吗？基本上，他们使用这些服务是否划算？”所以我相信这是你每天都会被问到的一个问题，有没有一家企业对于云托管来说太小了？

**Jeff:** 实际上我不认为有，特别是在我们新的微实例中，我们看到人们在微实例上运行微网站，微网站的想法是你有一个由单个页面和底部的行动号召组成的网站。对我来说，这就像一个终极的小企业，你只需要把所有的东西都放在一个页面上，你描述你的产品，你描述它的好处，你说这是如何实际购买它的。我认为微型网站模式真的很好，因为你可能不会靠一个微型网站谋生，但你有能力以非常非常低的成本产生许多不同的微型网站，所以这有点像终极小企业，你可能在谈论每小时几分之一便士来运营这些微型网站和微型企业。我认为，小企业总是会关心经济，所以我认为实例本身的价格非常非常有竞争力，但事实是，当你不需要它们时，你可以关闭它们。因此，也许您正在使用一些非常非常强大的服务器来进行一些分析或某种数字运算，但您可以在早上进入办公室时启动服务器，整天进行数字运算，在一天结束时，您可以暂停它们，然后在第二天早上从您停止的地方继续。

凯文:酷。这是 Matt S 提出的一个很好的问题，他今天发来了几个问题。他说，“从我的应用程序的角度来看，这些 API 是如何工作的？”有没有几种框架和语言的接口，比如 PHP、.网等。，或者我会提交 raw 或 REST 来访问这些内容吗？"

**Jeff:** 好的，Matt，我们确实有适用于多种语言的工具包，我想我们有适用于您提到的所有语言的工具包，所以一般来说，您会调用一个框架或一个工具包，除了我们已经构建的和我们自己提供的工具包之外，还有许多第三方开发人员也为不同的语言开发了工具包，只是我们还没有把它们放在优先列表的首位。如果你真的，真的想的话，你可以进行原始的 REST 或者原始的 SOAP 调用，我们会告诉你怎么做；这有一点复杂，因为你必须做一件叫做签名的事情，你需要使用我们在你创建帐户时给你的公钥，你需要用那个密钥签署你的请求，这样我们可以确保它确实来自你，而不是来自假装是你的人。

**Kevin:** David Bier 问道:“微实例非常有用，我正在考虑将我的电子邮件服务器放在微实例上，由于负载较低，在微实例上运行是不是一个理想的选择？”

杰夫:我想这应该没问题。当然，我讨厌一直说这要视情况而定，但这确实取决于你的具体情况。微服务器非常适合那些大部分时间处于空闲状态，然后偶尔会处理突发活动的服务。因此，微处理器实际上可以，它们有一种稳定状态的 CPU 分配，然后它们可以在短时间内突然达到更高的速度。所以我认为电子邮件服务器可能——

凯文:酷，所以这取决于你收到多少封电子邮件，大卫。

杰夫:是的，也许还有他处理多少垃圾邮件之类的事情。

凯文:这里有几个相互关联的问题。Ron P .问道:“有没有一个概念是拥有‘我自己的服务器’来防止这样的情况，如果我在一个与非常繁忙的站点共享的服务器上，那么他们会降低我的站点的速度？”

杰夫:好吧，这个问题有两种不同的答案。因此，我们建立了 AWS，以便当您共享资源时，我们总是会向您提供我们承诺的 CPU 能力和内存量。这与 VPS 模型非常非常不同，在 VPS 模型中，它基本上是将一台机器细分，直到您的用户抱怨一堆，然后稍微后退一点。AWS 模型在 CPU 能力、内存等方面为您提供了承诺的能力。现在，如果您出于其他原因只是说我真的只想拥有自己的物理机，您可以通过简单地分配最大的实例类型来有效地做到这一点。

凯文:酷。所以最大的大致相当于最大的基础设施。

杰夫:是的，这些实际上就像是一台独立的服务器。我们拥有的最大的一个是，它有一个很长的名字，叫做高内存四倍超大。它有八个虚拟内核，68g 内存，1.7 本地磁盘，所有这些每小时只需 2.40 美元。

凯文:酷。卢卡斯，我想听听你对此的回答。“如果我设置了一个 EC2 实例，并安装了 Apache 和 PHP 来为我的网站提供服务，那么我是否需要担心安装软件更新以保持最新的 Apache 版本、操作系统补丁等。?"我们的策略是什么？

Lucas: 是的，这就像在一个专用的服务器环境中运行一个服务器一样，总会有一些安全问题，所以我强烈建议你避开这些问题。我很确定 Jeff 会在书中谈一点你可以利用的分布式防火墙和安全组。但是，是的，看，这就像在一个常规的托管环境中运行任何服务器一样，所以这绝对应该是你最关心的问题。

**Kevin:** 我知道我们使用 RightScale 来管理和监控我们的服务器。他们还提供了现成的现成 Apache 服务器、现成的 MySQL 服务器，因此在某种程度上，您可以信任第三方服务来维护这些相对最新且已知可用的映像，任何时候他们发布新映像，您都可以使用新映像启动新服务器，并关闭旧映像上的旧服务器。所以我想只要你关注第三方提供商的博客，你就可以让他们在某种程度上担心这个问题。

卢卡斯:是的，我绝对同意。

Kevin:Jeff，你有什么建议，你会对虚拟服务器实例应用操作系统更新吗，或者有更好的策略吗？

**Jeff:** 所以有两种不同的思想流派，其中一种认为，你实际上从不更新正在运行的服务器，相反，你总是简单地用最新的补丁创建新的黄金 ami，你简单地杀死你的旧实例，并从新 ami 启动新实例。这样的话，你基本上可以使用一个脚本或使用一个食谱工具，如木偶或厨师，来建立你的主人，你总是知道他们是可复制的，你可以完全控制那里有什么。

凯文:好的。David Bier 问道，“SNS 是一项新的服务，我真的可以看到它被大量使用，它有什么突出的功能，有可能将简单的信息传递给一条消息吗？”我想他可能在说 SQS。

杰夫:不，实际上我们有一个全新的服务，叫做社交网络，比我在书里提到的还要新。写一本书的困难之处在于，你要拍摄一个固定时间点的快照，作为一个在 AWS 工作的人，我总是意识到有一些全新的东西，当你知道你一写完“结尾”就会有一些全新的东西冒出来，你会说，伙计，我当然希望我们能包括这些，但你必须划清界限，并说，好吧，这是我们能做到的最大限度。

因此，SNS 是简单通知服务的缩写，它基本上是一个基于主题的发布和订阅模型。因此，您可以以编程方式创建主题，然后让应用程序或电子邮件地址订阅这些主题，然后发布到这些主题，当您发布到某个主题时，所有收件人都会收到电子邮件或 HTTP 通知，告知他们有新主题或新消息发布。

凯文:这有点像推 SQS，对吗？

**Jeff:** 因此，SQS 是一个基于队列的模型，其中实际上有存储，而社交网络是即时交付；一旦你发布了，传递将会在相对较少的几毫秒内发生。

抱歉，我打断你了，卢卡斯。

Lucas: 是的，SNS 是我们考虑过的东西，我们在 99designs 时就考虑过，我想这是一种在实例上线时更可靠地配置集群的方法。举例来说，当一个实例，比如一个 web 应用程序实例上线时，它可以向所有其他 web 应用程序节点发送通知，表明它是活动的，并且它是存在的，这将允许我们重新配置 Memcache 或 Beanstalk 之类的东西，其中我们已经有了可以相互通信的应用程序服务器。

杰夫:听起来不错。

**Kevin:**Peter Gotkindt 向我们提出了一个很好的问题，他问道:“如何对服务器的关闭进行最佳计时，以便尽可能多地使用一个小时的使用时间，同时避免为新的一个小时的 30 秒付费？”计费真的以小时为单位吗？

**Jeff:** 计费肯定是以小时为单位的，实际上我自己从来没有尝试过这样做，尽管我确实有一些个人 AWS 资源，而且我确实像其他用户一样每月为它们付费。我从来没有真正尝试过精确计时，以确保我只有一个小时的使用时间。我想你可以做的是从实例中调用 EC2 API，得到启动时间，然后四舍五入到一个小时，然后算出最佳关闭时间。

凯文:酷。让我快速浏览一下问题，因为我们已经回答了大部分问题，我看到了几个很好的总结问题，但我想确认一下；杰夫，给你来几个速射的。有没有为 Solaris 提供 Sparc 架构的计划？

杰夫:据我所知没有。我认为这个问题最多每年出现一次。我与许多用户交谈过，我会说每年可能有一个用户要求 Sparc，所以这将是 2010 年的要求，我会焦急地等待 2011 年的要求(笑声)。

凯文:所以在你无事可做的那一年，你就会去做？

**Jeff:** 说真的，如果有人真的需要，请随时给我发电子邮件，提供更多关于您需要的信息，我很乐意将这些信息传递给开发团队。让我把我的电子邮件地址写在这张幻灯片上，以防有人想找到我。

凯文:好的。Matt S .问道:“业界对云中的数据所有权有一些担忧，SitePoint 和 Jeff 对亚马逊虚拟服务器上托管的数据是否真正拥有所有权有什么想法或评论？”

杰夫:是的，这个问题经常出现，是那种转移注意力的问题。事实是，这是你的数据，你完全拥有你的数据，我们断言你的数据没有任何所有权。你可以在任何时候、任何地点将它存储在云中，你可以决定什么时候删除它，你不仅可以完全控制你存储的内容，还可以控制它的存储位置；你可以选择存储在美国、亚洲或欧洲，这是你的数据，完全由你掌控。我必须非常非常强调这一点，因为这是那些云神话之一，似乎只是不时地流传，云不知何故就像这个巨大的怪物，吃掉你的数据，并拒绝还给你。绝对不是这样。

凯文:卢卡斯我知道有时候我们有一些策略，最坏的情况是亚马逊在一夜之间消失，我们仍然有一些策略，我们的数据仍然可以访问，你想谈谈吗？

卢卡斯:是的，当然。我想说的是，如果您担心您的数据以某种方式不受您的控制，您可以在存储之前对其进行加密。因此，我们做的一些事情，例如，我们的 MySQL 服务，我们倾向于将 MySQL 复制到 EC2 环境之外的一个位置，通常是我们的办公室，所以如果 Amazon，上帝保佑，完全消失了，我们显然会在本地有我们的代码库的副本，我们会有我们的 MySQL 数据库的最新副本，如果有什么事情发生，我们将能够在其他地方启动。我们也有各种策略，如果你有数据存储在 S3，你也许还可以将数据从亚马逊环境复制到其他地方，这样如果你需要，你可以在其他地方启动。但总的来说，从我们的角度来看，所有权并不是什么大问题。

凯文:好的。Adam Burns 说:“我从事业务运营，我不是技术人员，我们正在向 AWS 过渡。今天对我来说有点难。对于非技术人员来说，这本书是一个好的起点还是有更好的选择？”

杰夫:嗯，让我看看。我希望这本书的前几章能让你开始，如果没有，我希望收到你的来信，也许我需要写第零章或第一章来开始。但就像亚马逊的其他部分一样，我喜欢听我的用户和我的读者和客户的意见，所以任何对我过得太快的事情的反馈都很好，只是为了确保我们确保我们从一开始就让人们达到他们真正富有成效的地步。

凯文:是的，我会说你可以买下这本书，读完前几章，然后把书交给你的开发者，或许可以从那里拿走。是的，我们希望听到一些反馈。我看看，只是想确认一下我们把这里的东西整理好了。让我们来看看罗恩·p。他说，“这是一个总结性的问题:一个普通的旧网站在云中，这是云计算的一种，似乎术语不应该是云计算，而是动态基础设施托管或类似的东西，除了动态基础设施之外，我唯一见过的是 SQS 和简单的数据库；听起来对吗？”

**Jeff:** 所以，我不认为我们真的发明了“云计算”这个术语，它似乎是业界已经采用的术语。你当然可以质疑这个名字的定义和选择，但是这个行业选择说这是我们喜欢的称呼。

凯文:但是从根本上来说，把你的网站放在你租用的物理计算机上和放在虚拟服务器上的区别在于，你可以根据你的业务需求来扩大或缩小、复制或休眠，我想这都可以归结为不同的技术风格，但是在灵活性方面有着根本的区别。

**Jeff:** 我同意，所以我们可以说云让您可以选择在以后获得灵活性。因此，在这个行业中经常发生的一件事就是这种被称为“云清洗”的现象，在云术语出现之前，许多事情都做得非常好，人们已经采取了这些云标签，只是将“云”贴在可能并不真正需要重新标记为云的东西上。但它确实告诉你营销的力量，以及人们认为这是未来的浪潮的力量，如果一切都需要云这云那，以获得一些关注。

凯文:好了，我想我们就到此为止吧。也许卢卡斯和杰夫有些离别的想法。

杰夫:让我想想，我真的很感谢大家坚持了近两个小时，这太棒了。我真的很喜欢写这本书，当我把它放在一起时，我有很多乐趣，我希望阅读和写作一样有趣，但我只是有很多乐趣，有点强烈的乐趣，但尽管如此，因为我自己必须了解 AWS 的不同部分，然后思考如何以最有趣和最有成效的方式向观众展示这本书。

卢卡斯:听着，我想我真正想说的只是感谢大家的到来，我总是很高兴谈论这些东西，所以如果你们想问更多的问题，可以给我发电子邮件，地址是 sitepoint.com 的卢卡斯；我总是很乐意和你聊聊这些事情。

Jeff: 是的，我也是，我很高兴能收到更多的邮件和后续信息等等。

凯文:关于这次会议，目前[在 sitepoint.com 主页上有一个帖子。我想我们 SitePoint 的所有人，因为这是我们举办的第一次在线研讨会，我们希望听到您对研讨会进展的看法，如果您有任何挥之不去的问题，您也可以将它们张贴在那里，只需前往 SitePoint，点击首页上关于本次网络研讨会的报道，并给我们留下评论，让我们知道您是否希望我们再次举办此类研讨会，甚至是您希望了解我们的一些主题——呃，听听我们关于。(笑)](https://www.sitepoint.com/cloud-computing-webinar-full-recording/ "Cloud Computing Webinar: Full Recording")

有几个问题我们没有回答，但我将在接下来的几分钟里公布这些问题的答案，但除此之外，我想说谢谢你；我想对每一位正在收听的人表示感谢，我是凯文·杨克，在 SitePoint 总部为您报道，感谢您的加入，我们下次再见。再见再见。

感谢您收听 SitePoint 播客。如果你对今天的面试有任何想法或问题，请联系我们。你可以在 Twitter 上关注 SitePoint[@ sitepointdotcom](http://twitter.com/sitepointdotcom)，你也可以在 Twitter 上找到我 [@sentience](http://twitter.com/sentience) 。访问 sitepoint.com/podcast对该节目发表评论，并自动订阅获取每一期节目。下周我们将带着我们通常的专家小组的另一个新闻和评论节目回来。

SitePoint 播客的这一集是由 Karn Broad 制作的，我是 Kevin Yank。再见了。

迈克·梅拉的主题音乐。

感谢收听！请随时让我们知道我们做得怎么样，或者使用下面的评论栏继续讨论。

## 分享这篇文章