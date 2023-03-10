# 我们从重新推出 SitePoint 中学到的 8 件事

> 原文：<https://www.sitepoint.com/8-things-learned-relaunching-sitepoint/>

> 鼠与人的最佳计划，斜斜地结合在一起

上周在 SitePoint，我们非常自豪地重新推出了全新的 SitePoint.com。

然而，那些在一周前浏览网站的人会注意到我们实际上在一周前启动了，只是在出现一些问题后回退了。是的，尽管对一个已经使用了一周多的功能完善的试运行和生产部署进行了严格的测试，我们最好的计划还是毫无进展。

我们想分享一些我们学到的或通过这次经历强化的经验教训，以帮助那些可能重新启动现有网站的人。

## 1.准备好状态页面

没有人希望一次发射失败。但有时确实如此，当事情变糟时，你希望能够快速切换到一个状态页面，让你的访问者看起来更舒服一点，而不是一个可怕的服务器错误消息。

当前的 [SitePoint 状态](https://status.sitepoint.com)页面作为 [GitHub 页面](http://pages.github.com)托管，这允许我们拥有一个外部托管的页面，不会受到任何主站点停机时间的影响。

测试您是否可以快速切换到状态页面。

## 2.总是能够回滚

回滚到以前的版本，虽然不可取，但应该始终是一种选择。由于我们的新设置运行在完全不同的基础设施上，我们可以通过更改几个 DNS 条目快速安全地回滚到我们的旧站点。如果需要在现有数据集上运行迁移，请确保在开始迁移之前拍摄了快照。

在我们决定需要花更多的时间来解决我们试图修复的文件系统错误之后，能够回滚一切并睡上一觉是非常重要的。

永远不要把自己放在一个唯一的选择是修复一个坏的设置的位置。

## 3.在多个页面上进行负载测试

负载测试非常重要，在我们第一次发布之前，我们使用了优秀的 [Loader.io](http://loader.io) 对当前站点和新设置进行了一些基准测试。这使我们能够发现一些缓存效率低下的问题并加以纠正，让新的 SitePoint 在不到两秒的时间内持续命中 DomContentLoaded，这是旧站点的三倍改进！

不幸的是，我们失败的一个地方是在多个页面上进行负载测试。您的所有访问不会到达同一个页面，因此您的负载测试也应该反映这一点。访问多个页面也会让你的技术堆栈的所有部分都受到考验。在我们的例子中，我们的堆栈中倒下的那部分没有在主页上使用，所以这里的负载测试永远不会显示出当我们按下 go live 按钮时很快发现的关键问题。

## 4.负载测试，直到您的站点失败

作为开发人员，我们为知道我们构建的东西可以承受各种压力和负载而感到自豪，并且不愿意想到我们的应用程序会崩溃——这是很自然的。但是，您知道您的应用程序在开始崩溃之前可以承受多少负载吗？您的应用程序的哪个部分会首先感受到它？

当我们重建技术堆栈的共享存储部分时，我们的部署遇到了巨大的流量，直到它崩溃。这让我们知道我们可以承受多少流量(远远超过我们正常流量的 10 倍)，在这种压力下堆栈的哪一部分崩溃了(负载平衡器)，我们必须做什么，以及需要多长时间才能让它再次工作(大约 15 分钟)。

这种洞察力使我们能够在部署中提出需要改进的地方，并且我们已经开始实施降低技术复杂性的计划。

## 5.永远不要在下午晚些时候或周五部署

这听起来可能是世界上最显而易见的建议，对吗？我是说，谁会在周五或者你要回家之前发布呢？对吗？对吗？

不幸的是，在我们的职业生涯中，几乎所有人都至少犯过一次这样的错误。我们日复一日地测试，像疯子一样工作，以便在截止日期前完成测试，不知不觉中，已经下午 4 点了。你的老板对你说，“我们准备好了吗？”，你用那种乐观的态度来回答，这种乐观的态度是多年的经历所带来的。“当然，我们准备好了！”

所以，你按下“上线”按钮，事情会嘎吱作响，看起来工作正常。祝贺分发到每一个人，每个人都回家了。几个小时过去了，然后，一切开始发生。

经过几天的测试，我们在墨尔本时间周三下午 4:30 左右按下了按钮。这比我们用户所在的大多数时区都要早，从比东南亚早几个小时到比旧金山早 17 个小时。

当人们第一次报告速度变慢和随机断开连接时，大约在晚上 7.30 左右出现了一些问题的第一个迹象。然后，断开变得不那么随机和更常见，在你知道它之前，整个网站没有反应。经过一些诊断后，发现我们运行 [DRBD](http://www.drbd.org) 的共享存储解决方案被锁定，导致任何访问其上文件的东西也被锁定。最终，这意味着所有的 Apache 线程都被锁定，不再有请求被处理。

我们花了几个小时解决这个问题，试图解锁文件系统，到了午夜左右，网站再次启动并运行了大约 10 分钟。其中一个 DRBD 节点有一个内核错误，阻止了任何进一步的保存，在凌晨 2 点 30 分左右，艰难的决定回滚到旧网站。

在周四和周五花了一个不同的解决方案来解决 WordPress 的共享存储难题后，我们有了另一个潜在的机会在周一下午发布网站。然而，不想犯同样的错误两次，决定在周二早上首先启动。这被证明是一个明智的举动，因为不可避免地会有一些小事情需要解决，而这在一整天之前比在发射后几个小时更容易完成。

## 6.确保您的服务器可以快速、轻松地启动

在这个从云服务如 [AWS](http://aws.amazon.com) 和 [RackSpace Cloud](http://www.rackspace.com/cloud/) 启动应用程序的时代，以绝对最小的努力启动新服务器至关重要。通常这意味着你要么已经烘焙了一个预构建的 ISO/AMI，和/或你使用了 Chef、Babushka、Puppet 等的一些组合。

对于我们的新部署，我们决定使用 [Salt](http://saltstack.com/) ,它允许我们在几分钟内启动新的应用/代理/搜索/数据库节点，并让它们尽可能轻松地滑入堆栈。

当我们重新测试我们的部署时，我们确保我们能够在系统处于压力测试下时销毁和产生新的实例。一旦网站上线，我们就不能要求所有的访问者在指定的时间内停止浏览它！

## 7.了解当您卸下系统的某些部件时会损坏什么

我们第一次尝试启动时最大的失败之一是不了解共享存储节点锁定的后果。虽然我们通过完全替换基础架构的这一部分来缓解这一问题，但我们还是竭尽全力来测试如果设置的其他部分丢失了会发生什么。

当然，如果您移除数据库服务器，一切都会很快崩溃！但是当 [Memcached](http://memcached.org/) 不在的时候会发生什么呢？还是 [ElasticSearch](http://www.elasticsearch.org/) 服务器消失？通过移除这些节点，我们确保了一定程度的弹性。没有 Memcached，性能会显著下降，但仍然存在，这意味着我们有时间让新服务器投入运行。如果没有 ElasticSearch，我们会退回到默认的 WordPress 搜索，虽然没有那么快或者好，但是仍然有效。

这种测试让您可以执行实际的开发-操作任务，比如启动新的应用程序节点和调整配置需求。可以考虑的一个模型是网飞推出的[混沌猴子](http://techblog.netflix.com/2011/07/netflix-simian-army.html)，通过随机禁用生产实例来测试系统弹性和崩溃响应时间。

## 8.接受错误，从中吸取教训，保持透明

不幸的是，生活中并不是所有的可能性都可以被解释，无论你做了多少计划，有些事情还是会出错。重要的是，如果真的发生了这种情况，团队可以团结起来，快速有效地解决问题，而不会互相指责。

SitePoint 在这方面非常出色，当问题开始出现时，包括以前的校友在内的一群随时准备和愿意工作的人来了，不知疲倦地帮助调试和设计一个不同的攻击计划，以便最终重新启动。

同样重要的是您与客户的互动。我们很幸运地拥有一个忠诚且善解人意的用户群，停机和重组期间的反馈几乎都是正面的，开发人员理解在大规模部署期间有时会发生的麻烦。话虽如此，我们也从未试图隐藏我们犯下的错误，并尽一切努力确保我们的第二次发射成功。

虽然这些课程的主旨可能看起来很基本——测试一切，不要在危险的时候部署——但如果您对自己的设置有信心，很容易忽略一些最明显的事情。作为开发人员，我们经常对我们认为可以实现的事情非常乐观，这可能会影响我们对基础设施设置的信心，导致忽略或搁置众所周知的指导方针。

## 分享这篇文章