# SitePoint 播客#131:我们不需要讨厌的徽章

> 原文：<https://www.sitepoint.com/podcast-131-we-dont-need-no-stinkin-badges/>

SitePoint 播客第 131 集现已推出！本周，这个小组由路易斯·西蒙诺([@ RSS addic](http://twitter.com/rssaddict))、布拉德·威廉姆斯([@威廉斯巴](http://twitter.com/williamsba))、斯蒂芬·塞格拉夫([@塞格拉夫](http://twitter.com/ssegraves)和帕特里克·奥基夫([@伊罗格](http://twitter.com/ifroggy))组成。

## 下载这一集

你可以下载这一集作为独立的 MP3 文件。以下是链接:

*   SitePoint 播客#131:我们不需要讨厌的徽章

## 剧集摘要

以下是本期节目涉及的主要话题:

*   [Google+发布其 API](http://googleplusplatform.blogspot.com/2011/09/getting-started-on-google-api.html)
*   [Mozilla 启动其开放徽章基础设施项目](http://blog.mozilla.com/blog/2011/09/15/openbadges/)
*   [甲骨文宣布 MySQL 商业扩展](http://www.h-online.com/open/news/item/Oracle-adds-commercial-extensions-to-MySQL-1344611.html)
*   [脸书举办年度 F8 开发者大会](http://www.facebook.com/f8)

在[http://delicious.com/sitepointpodcast/131](http://delicious.com/sitepointpodcast/131)浏览展览中引用的链接的完整列表。

## 主聚光灯

*   马赫迪·优素福的编程语言键盘热图
*   帕特里克:[GarbageHorror.com 低成本电影评论](http://garbagehorror.com/)
*   路易:[未来友好](http://futurefriend.ly/)
*   斯蒂芬:[Slipsum.com-塞缪尔·L·杰克逊占位符文本生成器](http://slipsum.com/)

## 面试成绩单

大家好，欢迎来到 SitePoint 播客的又一集，我在这里和常规小组一起谈论过去一周的网络新闻，大家好！

斯蒂芬:你好，你好。

布拉德:你好，你好。

帕特里克:嘿！

路易斯:这周过得怎么样？

帕特里克:对你来说太大声了吗？不，我只是在开玩笑(笑声)。我做得很好，事实上，我正处于网络托管噩梦日，但除此之外，我很优秀。

哦，虚拟主机噩梦日，就像程序员节一样吗？

帕特里克:是的，没错，这是一个虚构的节日。

布拉德:我觉得没有程序员节好玩。

Patrick: 不要认为这很有趣，但有时这是由程序员造成的。不，开玩笑的，总有一天。

**路易:**嗯，我们都有过。

Patrick: 是的，希望你不会丢失 10 天的内容。

路易:哦，好痛！

帕特里克:是的，是的，谢谢你(笑声)。

布莱德:今天的节目以一个亮点开始。

帕特里克:是的。这其实是我一天的亮点。我不知道这是可悲还是好事，但我的意思是——我不知道。

斯蒂芬:所以今天开车去上班的人就像是被叫醒了一样，要仔细检查他们的备份，你是这个意思吗？

帕特里克:对。嗯，不，我有备份，但你知道，我没有备份到秒，我每两天备份一次，有一个备份系统，是的，也许我会失去五天，我不知道，但无论如何，失去内容，是的，从来都不是好事。

**Louis:** 服务器刚刚死了吗？

帕特里克:这是一个复杂的问题。不，服务器没有死，事实上，可能是服务器太多了，切错了，所以，是的，今天一整天都很有趣，昨晚的睡眠质量肯定很好。

路易斯:好吧，让我们试着通过播报新闻来转移一下你的情绪。有人想先来吗？让我们先不要做斯蒂芬的故事，因为那不会改善任何人的情绪。

斯蒂芬:真是令人沮丧。

布莱德:我先来。我不知道 Patrick 作为一个非开发人员会有多兴奋，但是，如果你是一个开发人员，有一个新的 API 在那里，它是大约两周前发布的，但它是在我们上次直播节目后几天发布的，所以我认为有必要提出它，这是 Google+ API 已经发布的第一个版本，显然它是一个非常有限的 API，但他们最终推出了它，它是一个 restful API 设计，所以它基本上使用标准的 http 方法来检索和操作数据。目前，API 只允许你访问公共信息，所以如果你在 Google+上发布了一些东西，你可以通过 API 访问这些信息，但是，如果你通过 API 向特定的圈子发布了一些东西，这些信息是不可用的。所以我认为，看看开发者如何利用这一点，看看他们可以开始用它做什么，如果人们真的坚持使用 Google+，特别是在他们习惯于在其他社交网络上看到的一些应用程序开始出现在 Google+上之后，这可能会非常有趣。

Stephan: 我衡量成功的方式是拼字游戏或 Farmville 出现的时间。

布拉德:是游戏；一切都是为了游戏，对吗？

路易斯:他们都在首次发布两天后就有了游戏。

布拉德:除非我同意，否则你必须推出一些游戏，否则没人会出现。它看起来很酷，它基本上返回 JSON，所以有几个有趣的小花絮，它只返回 JSON，所以没有 XML 选项，它启动了对身份验证的 OAuth2 支持，这显然是当你在这一点上进行身份验证时唯一能做的事情是获得好的用户名，而不是使用他们的 Google ID。

路易:这是一个非常有限的测试版。

**Brad:** 非常有限，但是考虑到他们发布的速度非常快，我的意思是，我想他们说了三个月，人们好像在敲门，为什么没有 API，我认为这有点荒谬，发布 API 是非常快的。

**路易:**对不起；我要加入人们说为什么没有 API 的合唱。看，基础设施在那里，你有你的数据库模式，你有——你的 web 版本只是一个 API，对吧，你只是把数据拉出来，对于这样的东西，只需要返回 JSON，这并不难；这看起来像是你想得到早期采用者，早期采用者包括许多开发者，他们想试着玩玩这个，看看他们能在这个基础上构建什么，我不认为他们有任何理由不能从第一天就推出一个可用的 API。

帕特里克:我希望他们因为这个现在就把 API 拿走。

Brad: 我认为你是对的，他们本来可以推出它，但是读了一些关于它发布的文章，听起来好像谷歌想要推出 Google+,让人们使用它，看看他们实际上会如何使用它，因为他们没有——他们对人们会如何使用它有很好的想法，但是你永远不会真正知道，直到数百万人出现在你的网站上，并开始使用它。因此，我有一种感觉，这可能有助于塑造 API 的未来，尽管他们现在发布的内容非常有限。我相信他们正在努力开发下一个版本，我相信看到这几千万人，我想他们现在在 Google+上已经达到了 3000 万左右，他们如何使用这项服务将真正有助于塑造 API 的发展。

Stephan: 嗯，也许这不是关于他们如何使用这项服务，但如果他们打算使用这项服务(笑声)，这才是真正的问题。

Louis: 是的，我的意思是从积极的方面来说，我必须说，你知道看到 API 如此干净地设计是很棒的，显然还没有大量的方法可用，但拥有一个返回 JSON 并与 OAuth 一起工作的好的 easy rest API 是很棒的，多年来，我们不得不与一些不太优雅的 API 一起工作，我想我们作为开发人员都同意这是令人耳目一新的。

斯蒂芬:这引出了我的问题；比起 XML，你们更喜欢 JSON 方法吗？

路易:当然，没问题。

斯蒂芬:好的，知道了。

**Brad:** 我的意思是我与两者都合作，特别是像许多 CMS“真的支持两者，开源 CMS”，所以对我来说，我可以走任何一条路，这并不是说我需要一个而不是另一个，但我认为现在一切都有点倾向于 JSON，所以我想这就是我最好开始习惯它的地方。

斯蒂芬:是的。那只是我的一个问题。

**Louis:** 我喜欢 JSON 的一点是，它是一种自我文档化的东西，你知道有很多 API，如果是来自一个没有发布完整服务的小公司，他们可以给你一个 JSON 返回内容的例子，我发现它的可读性更好，所以它很容易看，就像，哦，我得到了一个用户名和密码之类的东西，XML 太粗糙了，人类很难读懂

是的，我同意。

**Brad:** 你知道回到我们谈论的，他们如何花了一点时间来发布它，我想是另一件事，我们已经在播客上讨论过了，这已经有一段时间了，可能是六个月或八个月前，但我们谈到了开发人员如何对这些每六个月发布一次然后又更改的 API 感到非常沮丧；脸书因此臭名昭著。自从脸书 API 发布以来，它已经改变了大量的次数，如果开发人员不得不不断地回头改变他们正在使用的方法，改变你可能不希望被锁定的代码，而不是你必须每六个月改变的代码，这可能会让他们非常沮丧。所以，正如你所说，他们慢慢来，使用 OAuth 和 JSON 等现成的东西，推出了良好的标准，这令人欣慰，我知道这是非常基本的，我不会很快看到这种情况发生变化。

Louis: 是的，我很期待看到它能做些什么。Google+在很多方面让我想起了 FriendFeed，它在评论流、公共和私人帖子方面的工作方式，我还记得 FriendFeed 推出 API 时有很多创新，所以我真的很高兴看到，这显然有更多的观众，包括许多开发人员，所以看到人们用它做什么真的很酷。

Patrick: 是的，你们在 Google+上并不活跃，我只是调出你们的个人资料来看看。布拉德 8 月 8 日起更新一次，路易斯 9 月份更新一次，8 月 26 日起更新一次，斯蒂芬当然是所有人里最差的，总体上更新两次，7 月 4 日和 8 月 6 日，所以不是因为没有 API 为什么你们没有花太多时间在那里，我猜是布拉德吧？

**Brad:** 对我来说最大的问题可能是它不支持 Google apps，我整天都登录 Google apps，所以对我来说，我基本上要在一天的中间切换帐户才能上 Google+，所以这有点-有点不方便，切换帐户并没有那么难， 但这是一个很大的不便，如果我上了它，它通常就像晚上在笔记本电脑上一样，我想我只是没有——我真的没有——它没有成为我的习惯，而脸书和 Twitter 在这一点上对我来说是一种习惯，它们是我一天中检查多次的网站和服务，Google+只是还没有到达那里。

Patrick: 是的，你说得很有道理，因为——对我来说正好相反，因为我的 YouTube 使用率下降了，因为我把它与错误的账户联系在一起，现在第一世界的问题，白酒，不管你想叫它什么，我不想换，我只是懒得换账户。我的一个朋友，今日剽窃的乔纳森·贝利，他还没有上 Google+,因为谷歌应用程序的事情，因为他合并了他的帐户，他还不能使用它，有趣的是，他们现在正在提示我合并我的帐户或分离它，他就像“不要这样做，永远不要这样做！”因为他们已经锁定了使用谷歌企业应用套件电子邮件地址的人，而他现在只是运气不好，这似乎是一个如此奇怪的问题，我不知道他们为什么要这样做。

路易斯:情况越来越好了。听着，我知道他们之前在谷歌应用程序的问题上有很大的问题，但是以前，你知道，就像我的一个朋友在 Twitter 上发布了一些关于玩谷歌应用程序轮盘赌的东西，每当你去文档时，你会被登录到一些随机的帐户，就像你会点击电子邮件中的链接一样—

布拉德: Docs 是最差的。

**Louis:**——在你的 Gmail 中，然后当你到达谷歌文档时，你会突然登录到一个不同的账户，当我刚刚点击发给我的电子邮件中的一个链接时，它会说你无权查看这份文件。

帕特里克:是的。

路易:真的很随机。它变得更好了，我在 Gmail 中使用新的帐户切换器，我登录了我的三个帐户，你知道，两个 Google Apps 帐户和我的个人 Gmail 帐户，它工作得很好，只有一些小的调整，它变得更好了。是的，对于我来说，为什么我没有更多地使用 Google+,有几件事，一是我发现人们在其他网络上发布的东西没有那么有趣。我不知道这可能只是我关注的人，但似乎人们正试图发布更多的博客内容，并试图不只是随便喜欢，嘿，这就是我在做的事情；这感觉不像是一个随意的社交环境，这感觉很像是我试图让人们对我的东西发表评论，也许这只是因为早期采用者是非常喜欢写博客和关注技术的人。

帕特里克:是的。

路易斯:另一件困扰我的事情是，就像我说的，它让我想起了 FriendFeed，但它缺少了一些我真正喜欢的功能，所以它总是感觉有点尴尬，因为我觉得我在那个环境中，但突然我不在了。举例来说，你没有朋友的朋友，所以如果我关注的人喜欢或加 1 别人发布的东西，我看不到，所以我只看到我关注的人直接发布的东西，这样就没那么意外了，就像我不会随机看到新人的东西并发现新人，其他东西不会随着被喜欢或被评论而浮到顶部，它都是按发布的时间顺序排列的；对我来说，这种互动感觉不太好，就像我知道 FriendFeed 已经死了，我知道没人再用它了，但它仍然是我最喜欢的。

帕特里克:你就像在街角慷慨激昂；把我的 FriendFeed 扎克伯格还给我！(笑声)

路易:你知道，看，这就是我的感觉。

帕特里克:是的。而我看到的布拉德刚刚更新了他的 Google+，谢谢你，布拉德。

布拉德:你叫我出来，这是我这个月的状态更新(笑声)。

帕特里克:斯蒂芬，你呢？

斯蒂芬:我就是没时间(笑)。

帕特里克:好吧，侮辱我们其余的人。我太忙了。

Stephan: 如果它有一个 iPhone 应用程序，我可能会，是的，因为我可以快速下载所有内容，然后稍后阅读。

布拉德:有一个 iPhone 应用程序。

Stephan: 哦，有？

布莱德:也很不错。

斯蒂芬:那我得去查一下。好吧，也许我现在会上瘾。

布莱德:是啊，看看这个。它并不完美，但考虑到 Google+有多新，我认为它已经走得很远了。

斯蒂芬:那我得去看看了。我还没看它，所以我会做的。

Patrick: 是的，Brad，你有点像 Zuckerberg，因为他没有更新，而他的圈子里有 100 万粉丝和人，你有 10 个和 500 个，所以这相当于每 50 个关注你的人就有一个更新。

布莱德:我应该多更新。我希望他们不要期望太多。

**Louis:** 我有一个非常奇怪的故事，我不太确定我是否 100%理解它，但我会把它扔在那里，因为理论上它听起来很酷，但听起来也有点乱。这是 Mozilla 博客上的，我想是一周前，他们启动了一个叫做开放徽章基础设施项目的项目。因此，他们将其描述为一种努力，使得在网络上发布和分享数字学习徽章变得容易。

Patrick: 是金星 API！

路易:你说什么？

帕特里克:这是金星 API。

Louis: 这是一个真正的金星 API。现在，我就说到这里，稍后我可以更详细地介绍这背后的一些技术细节，但我只想知道你们对这个想法的即时反应。

Patrick: 我认为你可以在这里插入“我们不需要讨厌的徽章”这句强制性的话。

路易:我有点期待。

**Brad:** 因为我想我可能不太明白，所以你会得到——徽章是用来做什么的，就像——看起来像是在网上学习语言，学习不同的东西——我的意思是有点像是一种证书，这就是我们在这里讨论的，是徽章而不是证书吗？

路易:我想是吧。比方说，我有一套在线教程，或者我在网上研讨会上讲授一些东西，然后当人们参加我的网上研讨会或者一旦他们完成了我的课程，我可以给他们一个徽章。它提供了一个 API 来对它进行签名，所以它所做的是将一些 JSON 元数据嵌入到我发给他们的 PNG 文件中，然后你可以在 openbadges.org 用 Mozilla 的 API 来验证它，并将其添加到徽章背包中，并在其他网站上显示，它是通过这个 API 认证的。所以这个想法是，你已经有了这种一致的标准来发放徽章，并在你的网站上展示它们。

Stephan: 这是我见过的最愚蠢的事情(笑声)。

布莱德:我在等人说这句话。

Stephan: 更愚蠢的是，我在这篇文章的底部读到，他们得到了 100 万美元的资助来做这件事，Mozilla 也是这样做的，来开发这个基础设施。

是的，来自麦克阿瑟基金会。

斯蒂芬:是的，来自麦克阿瑟基金会。

帕特里克:钱就是钱。

斯蒂芬:耶！我会拿出一百万美元的徽章。

Patrick: 你看，当我得到一个徽章时，我总是下载它并上传到我的服务器上，这样我就不会占用他们的带宽，对吧，但这完全颠覆了我的想法。

路易:不，不，不，确实是这样。

帕特里克:好的。

斯蒂芬:(笑)

帕特里克:所以我还得下载东西？

**Louis:** 作为一个徽章的发行人，你只需要——你用这个 JSON 编码在这个东西上签名，然后你可以通过电子邮件或其他方式把它发送给那个人，他们可以在他们的网站或其他地方显示它，但是它是用这个 API 签名的，所以如果你有一个 WordPress 的插件，例如，你可以向 API 报告，获得元数据，并获得这个徽章从哪里发行的链接，以及来自 PNG 的所有链接。

Patrick: 我知道 Mozilla 可能对 Firefox 以外的其他项目感兴趣，但这是在 Mozilla Firefox 上，我的意思是在页面的左上角有一个 Mozilla Firefox 的标志，所以我将它与 Firefox 联系起来，并说这是不是应该阻止 Firefox 持续的市场份额损失，即开放徽章？

Louis: 嗯，我不认为，公平地说，我不认为这与 Firefox 有什么特别的关系，因为没有与浏览器的隐式或显式链接，你不需要 Firefox 来使用它，很抱歉在那里向你开枪。

帕特里克:不，我明白，但我只是想知道…

**Brad:** 对我来说，这就像很多你听说过的或人们放在 LinkedIn 上的任意认证，你就像那个认证是什么，你知道它不是——它来自一些没人听说过的奇怪网站。

斯蒂芬:嗯，这就是它的妙处，布拉德，这就是它的妙处；现在你会知道，现在你会真正知道，你可以点击它(笑)。

帕特里克:这是标准化的，布拉德。

布拉德:我保证，如果有人递给我一份上面有徽章的简历(笑声)我可能会笑一笑，但我怀疑他们会不会得到这个职位。

**Louis:** 我将宣读麦克阿瑟基金会新闻稿中的一段话，这段话来自麦克阿瑟基金会美国项目副总裁 Julia Stash:“数字技术正在帮助重新想象学习，徽章正在成为一种新的方式，既鼓励又展示在正式和非正式环境中获取各种知识和技能。徽章简单、容易，如果做得好，可以更细致地展示一个人知道什么和能做什么，还有更多需要学习，我们预计这个比赛将继续发展一个徽章系统，可以改变人们分享自己信息的方式，企业做出招聘决定，组织支持获得对其使命或更大社会重要的技能。”

Stephan: 所以，我们知道那一百万美元中的 50.00 美元去了哪里。

路易:(笑)

帕特里克:这就是文案的价值，哇。

斯蒂芬:我想是的，是的，花了大约一个小时。所以，我就是不明白。这就像互联网的使命召唤徽章(笑声)。

帕特里克:哦，伙计，但如果你能在你的面前闪现，对，就像 HTML5 解锁！那不是很棒吗？

斯蒂芬:我真的很期待把这一点写进我的简历。

**Patrick:** 他们在 openbadges.org[有一个流程图](http://openbadges.org/)，这是这项工作的网站，它在底部，所以你必须一直滚动到底部，但是他们有像我猜测的你的徽章的演变，所以徽章发行者他们显示课后计划，在线学习，工作培训，然后到徽章，然后学习者有一个徽章背包，然后把这些徽章嵌入网站，社交网络， 博客和简历，我猜是在线简历和就业网站，然后他们从中获得工作、教育机会，并获得新的特权。

**路易:**不知道这东西是谁画的。

Stephan: 这将是《使命召唤》中你的枪的新附件(笑声)。

路易斯:是的，伙计，听着，当我看到这个的时候，我确实有一种感觉，“什么，真的吗？”

帕特里克:他们的网站上有一个完整的常见问题解答，我不知道，我不知道它是否有帮助，但是。

路易斯:我不认为这真的有帮助。

Patrick: 我所得到的是，他们将不得不收紧可以发放这些徽章的人，对，我的意思是，这不会成为你可以每一次——你不会去 Brad，你知道，Webdev Studios 并说，Brad，我们需要开发这个开放的徽章！

嘿，给我一百万美元，我会为你做一个徽章系统。

路易斯:但我认为就是这样，我认为这个想法是不受控制的，对，如果我是任何类型的网站，我可以向会员发放徽章或什么的。

帕特里克:天啊。

斯蒂芬:你会有一群看起来像门萨基金会的黑客(笑声)。

Brad: 我想找一份所有徽章的清单，没有吗？

**Louis:** 看到没，这只是基础设施，不像——他们没有徽章，只是一个任何网站都可以用的 API。

布莱德:我只是想看看徽章！

Patrick: 是的，有趣的是，如果你点击他们网站上的用户故事，它会带你到一个博客，实际上有今年 3 月的帖子，我应该说是今年 3 月的帖子，“现实世界中的徽章”，它说，“我们如何向初学者解释 Mozilla 的开放徽章项目，”它从那里谈到不同的人，学生，以及徽章如何使他们受益。这是三月份的，所以我想这已经开发了一段时间了。

**Louis:** 是的，这还处于非常早期的阶段，如果你看到他们已经有了一个与 GitHub 上的 API 进行交互的代码版本，但文档有些不完整，例如，他说他希望最终能为徽章签名提供某种公钥/私钥，但这还没有完成，所以你所做的只是将元数据直接嵌入其中，而不是嵌入私钥，所以我想这离他们想要的还有很长的路要走。我肯定会从这个博客上阅读这篇关于现实世界中的徽章的文章，只是为了阅读这些用户故事，我的意思是，如果人们在徽章的帮助下有令人信服的学习和教学经验，那么我想你会更有力量。但是，是的，叫我马上怀疑，这是我的立场。

**Patrick:** 如果你是徽章持有者，请在 sitepoint.com/podcast[发表评论](https://www.sitepoint.com/podcast/)让我们知道。

路易:对，绝对没错。听着，如果人们想给我们发电子邮件，告诉我们任何类型的故事，我会公平地说，如果没有成就和徽章，我会花更长的时间玩电脑游戏，你知道，就像我已经完成了，我已经享受了我的乐趣，我完成了游戏，所以我会继续前进，但它有点吸引我回来，哦，看，我可以获得成就，如果我这样做或什么的。你知道，我可以看到，也许你可以将同样的心理动机应用到比使命召唤更有成效的其他环境中，是的，我的意思是，看，如果它有效的话。

帕特里克:我不想解锁新的成就，我想解锁更高的薪酬等级(笑声)。

**路易:**是啊，嗯，看不出来；我很难想象这种情况会适用于这种背景下，在简历的背景下或在解锁新特权的背景下，这是他们所说的，这很奇怪，但我很难想象未来雇主会看着在线简历说，哦，看，这是网络学校的徽章什么的，你知道。

帕特里克:是的。

路易斯:我真的不——我不明白。

嘿，如果你在申请 Mozilla 的工作，那么也许。

路易斯:嗯，也许吧，是的，那就全靠徽章了。

**Stephan:** 那么，让我们继续走这条令人沮丧的道路，今天有一个故事出来了，Oracle 宣布——它实际上是在 9 月 16 日，Oracle 宣布 MySQL 数据库的商业扩展的可用性。这些新的扩展只被添加到企业版，并将进一步区分商业版和社区版。所以，这是结束的开始，我想，或者说是结束的一部分。

我想每个人在甲骨文接手孙的时候都有点担心这样的事情会发生。

Stephan: 和 Monty，Michael Widenius，这个最初发明并创立 MySQL 的家伙，在他的博客上有一篇很好的博文，讲述了这种方式是如何结束的开始，以及为什么他们正在采用的开放核心模型是一种错误的方式。这是一本非常好的读物，它讲述了你的项目现在依赖于一个供应商，你自己真的不能修复任何错误，除了最初的供应商之外，你真的不能与任何人签约为你做这些事情，诸如此类，所以如果人们对这在未来意味着什么感兴趣，这是一本非常好的读物，所以请查看它，我们会链接到它。你们觉得怎么样？

Brad: 我想知道这对实际的 MySQL 许可有什么影响，因为 MySQL 是在 GPL 上发布的，显然你可以在 GPL 下销售软件，但如果它不是免费提供的，那么这实质上就违反了许可。

Patrick: 呃，哦，我们当中的 WordPress 开发者，大家快跑！

**Brad:** (笑)我的意思是，每当你谈论开源，总会有关于它背后的软件许可和它到底意味着什么的争论，这就是。但最终，如果它是在 GPL 下发布的，那么引入一些实际上并不开放的收费内容，如果他们愿意，他们可以收费一整天，这不会使 GPL 无效，但如果它是加密代码，那么就会无效。这些文章中没有具体说明，所以我很好奇他们是否提到了这一点，或者 Enterprise 是否属于不同的许可证。

**Louis:** 是的，我想它的核心可能仍然是开放的，因为它是 GPL，正如你提到的，但我想这些扩展可能只是不同许可证下的独立代码库，类似于 Oracle 的插件。

**Brad:** 你可能会有另外一个论点，这是不是——这是任何一个开源项目，如果任何一个听过的人都知道，尤其是在许可方面，当你说什么是——你知道像 WordPress 中的插件时，它会变得非常棘手， 这是否被视为 WordPress 的一部分，因为它必须有 WordPress 才能运行，如果它是 WordPress 的衍生作品，因为它是 WordPress 的插件，我会认为 MySQL 也是如此。如果它有点像 MySQL 的附加组件，你必须有 MySQL 才能工作，如果你只是单独运行它，它就不会工作。 我可能完全疯了。我对这些插件或他们开始销售的商业扩展一无所知。

**Stephan:** 他们在这里做的一件有趣的事情是，通过阅读 Oracle 的新闻稿，他们加入了一些 Oracle 的功能，这些功能现在已经投入商业使用，比如他们已经为 Oracle 提供的虚拟机模板，以及他们已经为 Oracle 提供的 Windows 集群。所以，我在想，也许他们正试图将两者稍微融合，并试图让他们的一些 MySQL 客户转向 Oracle，或者，我不知道，我不知道未来会怎样。

Patrick: 我们现在同意 MySQL，就像你说的，MySQL 和 MySQL，只是为了有一天谷歌可以索引音频，我们会得到这两个(笑声)。这就像天才的预见。但是，也让我们说 MySQL 已经死了，每个人都讨厌它，我们想摆脱它；人们现在做什么，了解它，你知道，在许多低价主机帐户上广泛使用，让我们说，人们转向什么，主机公司明天转向什么？

**Stephan:** MariaDB？

**路易:** PostgrSQL。

帕特里克:这是一个平稳的过渡吗？

路易:大概不会。

Brad: 嗯，我的意思是有各种各样的开源数据库平台，MySQL 显然是最受欢迎的基于 web 的平台，Postgress 或 PostgrSQL，我永远也说不准。我对它不熟悉，但我知道它也非常受欢迎。

路易:我以前和它合作过。有趣的是，我当时在不支持 PostgreSQL 的 Drupal5 中工作，我不知道在 Drupal 的当前版本中是否仍然是这种情况，所以我不得不在核心中做了一点修改，以使该功能正常工作，因为 PostgreSQL 和 MySQL 之间有一些细微的语法差异。我不知道；我认为 WordPress 确实有对 PostgrSQL 的现成支持，不是吗？

Brad: 它基本上有一个数据库 API，所以你可以连接，我知道有一些插件基本上会覆盖数据库类，所以你可以把它连接到 PostgrSQL，你可以把它连接到 SQL server，所以即使是商业数据库平台你也可以把它连接到。我认为很多 CMS 都有相同的设置，本质上是一个数据库类或 API，可以接入并挂钩到他们想要的任何系统。

帕特里克:所以我想我要说的是，我想我们现在在某种程度上依赖于它。我知道蒙蒂有你提到的 MariaDB 5.5，斯蒂芬，我不知道；你到底有没有处理过？

Stephan: 没有，但据我所知，这几乎是一种替代，所以你可以加入 MariaDB，它会工作得很好。

Patrick: 所以他现在占据了一个有利的位置来吸引一些用户。

路易斯:是啊，很有意思。所以，我想可能有必要简要讨论一下这些扩展是什么，因为如果有大多数人不需要的东西，那么它就不是真正的超级相关，对吧，如果社区版仍然完全符合你的目的，那么就没有必要担心这一切，你仍然可以获得社区版，它是开源的，你可以在你的数据库上免费使用。因此，我们这里得到的是一个线程池，它在 16 核或更大的系统上提供了改进的性能，所以我想这主要是对超大型服务器的改进，所以他们在新闻稿中提供了一个图表，其中显示了带有新线程池的 MySQL 企业版和没有线程池的社区版之间的性能图，只有在大约 500 个并发数据库连接时，性能才会真正下降，低于这个数字的线实际上是一条在另一条之上。所以我想这真的取决于你的 MySQL 用例，如果你运行一个相当小的网站，甚至是一个相当大的网站，但在商用服务器基础设施上运行，那么这真的不是一个问题。但我猜它确实为最终向 MySQL 添加其他只在商业版中可用的特性打开了大门；我猜这就是人们所关心的。

Stephan: 是的，我的意思是如果你正在使用大规模的 MySQL 实现，那么我认为其中的一些特性，比如线程池，会非常好，因为你可以拥有一个 16 核或更大的系统，一个盒子；我看到了吸引力，但我不知道这是否足以让人们转向企业，除非你在做高端的东西。

9 月 22 日，脸书举行了年度 F8 开发者大会，主讲人不是别人，正是马克·扎克伯格。如果你有机会，你可以看看安迪·萨姆伯格为他主持 SNL 开幕式并假扮马克·扎克伯格的视频，它在脸书官方频道的 YouTube 上

路易:看起来确实有点像他，不是吗？

**帕特里克:**(笑)他甚至演得更像他，就发型和穿着拉链连帽衫之类的东西而言，他让自己看起来更像他，是的，我所做的一切都是由 DJ Khaled 赢得的，这是一个非常好的剪辑，所以检查一下。但他们确实发布了一些有意义的公告，尤其是对脸书用户，我想我应该谈谈其中的几个。对我来说，最大的一个是时间线的引入，它还没有对每个人开放，但如果你做一点点黑客工作，它很快就会出现，可以说，你可以从开发者的角度来看它，并实际访问脸书时间线，这是脸书个人资料的新格式。你们做过吗？

**Louis:** 我开始的时候试着浏览这个开发者的变通办法，但我在中途失去了兴趣，但几天后他们说，嘿，你想看看时间线吗？是的，我已经看过了。

帕特里克:好的，你觉得怎么样？

嗯，我不知道，我已经有四年对脸书做的任何事情都不感兴趣了。

帕特里克:你还在 FriendFeed 上。

路易斯:基本上，自从他们收购了 FriendFeed，就一直—

布莱德:他很痛苦。

**路易:**都结束了(笑声)。

帕特里克:斯蒂芬，布拉德，你们有没有摆弄过它？

布拉德:我没看过。我看到很多人都在上传截图，很明显新闻和技术网站上都有这些截图，但我实际上并没有接触过，没有。

帕特里克:酷。所以，我的意思是他们看待它的方式是展示你多年来吸收的内容的一种更好的方式，更容易回顾你多年前做的事情，并把它们拉回来，以这种时间轴格式来组织你添加到脸书的内容，然后按月等等，甚至更早，例如，包括你的出生日期，如果你包括它的话。所以，每个人都需要马上去庆祝我的生日，如果你知道的话，比如我出生的事实，或者其他。所以，是的，我发现这很有趣，这是个人资料页面的一种有趣的演变，我看到了很多关于脸书过去变化的抱怨，你知道，每次他们改变。

路易:好吧，我就问一个问题。所以，它最初是通过这个脸书开发商的链接提供的。

帕特里克:是的。

路易:也许他在 F8 是这样卖的；对开发人员的推销是什么，比如我的公司有一个脸书页面，或者我把脸书整合到我的网站上，Timeline 给了我什么我还没有的东西？

Brad: 是的，对我来说，它并不是一个真正的开发者特性，除非在开放的图表中有一些你可以使用的新的挂钩。

Patrick: 是的，在他们看来，这是一种更好的方式来展示内容，让人们更容易深入了解个人资料页面。Matt Hicks 曾在脸书担任社区经理，我最近在一次会议上见过他，他谈到一些人如何在脸书的新闻订阅上花费如此多的时间，这有可能改变我想可以说是不平衡的状况。 你会看到人们花更多的时间在个人资料页面上，只是浏览历史，查看不同的东西，因为内容更容易访问，它在那里，有更多的内容，它不只是一个更新，一个更新，一个更新，你知道，它不是更新，更新，然后像这样，像这样，像这样，像这样，像这样，评论； 它更干净，也许更容易使用。我不知道这会对开发者产生什么影响，随着人们使用它，这种影响可能会变得更加明显。但说到开发者，他们确实推出了一些新的行动，人们可以采取超越喜欢的行动。通常被引用的有听的，看的，读的；扎克伯格说，“你不必喜欢一本书你可以只是读一本书，你不必喜欢一部电影你可以只是看一部电影，你可以吃一顿饭，你可以徒步旅行，你可以听一首歌，你可以以任何方式连接到任何东西。”因此，他们将打破互联网上每一页上喜欢和喜欢一切的标准化措辞，似乎你可以喜欢的地方会有一些适用于特定类型产品或网络视频或电子书的语言和上下文，或者人们可以谈论他们如何使用的那种用法，或者只是说他们消费了一种产品，而不是说我喜欢这种产品。我可能还不够聪明，不知道开发者会如何利用它，但已经有一些了——我想有一个图书社交网络；我现在想不起它的名字了，它已经内置了“读取”功能。所以，也许有更好的机会分享内容。

Brad: 我在 Spotify 上看到过，所以如果你听 Spotify，它会链接到你正在听的内容，网飞现在也在上面，所以如果你在看电影或节目，它会告诉你你在看什么。

路易斯:哦，谢谢你带来了这些很酷的美国独有的服务。(笑声)

布拉德:哦，对不起。但我的意思是，这只是表明他们已经稍微扩展到这些服务正在进入的地方。现在，我不知道这些是否对公众开放，但我知道如果不开放，它们肯定会出现。但也许，我不知道，也许我是一个嬉皮士，但它似乎变得太多了，它在一个地方有太多的信息，它几乎变得势不可挡。

帕特里克:你没有嬉皮士的发型。

**Brad:** 当人们发布他们正在做的事情和你喜欢的东西或其他东西时，这是一种简单的简单方式，但现在它就像是在为所有东西提供信息，过去几年一直是这样，你把所有东西都放在一个地方，所以就像是你的朋友传入脸书的数据量很荒谬，你甚至几乎跟不上，因为 Susie 在听这个，Brad 在读那个，对我来说太多了。

帕特里克:谁是苏西，布拉德？April 知道 Susie 的事吗？(笑声)

布莱德:我想我不认识苏茜。

**Patrick:** 我要说的是美国，有一件事，这是一个小小的，非常小的安慰，但他们也宣布了许多大的合作伙伴关系，其中一个是网飞，他最近一直在大打出手，但他们已经与它集成，你可以很容易地甚至自动地分享你在网飞看到的东西，它将在 45 个国家中的 44 个国家工作，唯一的例外是美国的一项法案，某种法律禁止披露你的视频租赁信息。所以，我想就网飞而言，我们是唯一一个现在不能和脸书分享我们的观看习惯的国家。所以你有，路易。

布莱德:太奇怪了。

路易:这太奇怪了。好吧，我不想谈这个。不过，我只想说，有几件事，首先我确实看到了这个替代动词的东西，因为当这种获取时间线的小黑客变通方法涉及到设置一个新应用程序时，当你在脸书开发者界面中设置一个新应用程序时，它会要求你为你的应用程序创建一个动词，所以要么“读一本书”要么“看一部电影”或其他什么，我看了一下界面，我觉得这其实很有趣，我可以把它联系起来。我想提到的另一件事是，我真的很喜欢这个，没有双关语的意思，我真的很喜欢这个，因为我相信每个人都有这种情况，有人会发布这篇文章或呼吁捐款，例如，一个人道主义悲剧或像这个可怕的新闻故事，你在脸书的唯一选择是对此表示支持，我总是发现不，我不喜欢非洲之角发生饥荒的事实，我想指出，是的，我支持这个慈善机构或任何东西，对吧。

帕特里克:对。

路易斯:我认为这将是——至少在某种程度上解决了当你点击你实际上不喜欢的东西的喜欢按钮时，你总是会感觉到的认知失调。

帕特里克:肯定的。我想快速提及的另一件事是在 F8 之前我们已经接触过的滚动条，右边的滚动条是向下流动的东西，从页面和用户配置文件中可以看到很多很多活动。所以，Spotify 是一个很好的例子；我注意到，自从他们宣布了这个消息后，我的股票已经显示了很多来自 Spotify 的收听，xyz 现在正在 Spotify 上听这首歌。我确信我能关闭它。我想有人说过，如果你隐藏了足够多的 Spotify 故事，它们就不会再出现了，但是，这种按时间顺序排列的方式有很大的力量，因为新闻订阅是脸书根据你的使用习惯决定你想看什么，这就是新闻订阅。对于公司、页面、应用程序以及你发布到新闻订阅源的任何其他东西，他们根据 edge rank 来确定，edge rank 是一种半神秘的计算或算法，其中他们考虑到你和页面或用户之间的关系，然后考虑你们之间的互动量，然后考虑时间的减少；时间越长。所以，在右边，这个滚动条本质上是内容，因为它发生了，所以当人们使用你的应用程序，当人们喜欢你的页面或喜欢你发布的东西时，它就会出现在这个滚动条上，显示给他们在脸书的朋友。我不知道；我，我自己，喜欢它，我发现自己随机发现了比以前更多的东西，而不是仅仅去脸书的策划新闻，但我可以看到一些人可能会觉得这有点太多了，增加了已经繁忙的空间。

是的，每当我用手机上的脸书应用程序时，我都会有这种感觉。我不知道为什么，但不知何故它是不同的，就像我在电话上得到的是实时的，而不是策划或什么的，因为我看到了一堆新闻故事，这些故事来自我忘记了我跟踪的页面，当我去网站时，我从来没有在我的新闻订阅中看到过。

帕特里克:是的。

路易:所以可能是类似的东西。但很多时候，我只是想，哦，哇，这都是噪音，我只是想看看我朋友的东西，但是，嗯，好吧，无论如何。我猜对于拥有脸书页面的人来说，这是有价值的。

帕特里克:对，对。有人告诉我，因为我问了这个问题，我就想，如果你不想看到这个呢？有人告诉我，我不确定它有多真实，你可以点击该项目，然后点击小下拉菜单，然后点击隐藏故事或报告故事或垃圾邮件，如果你做得足够多，它会从你点击的内容中学习，这是我被告知的，我不知道这是否 100%肯定，但它会有意义。

**路易:**牛逼。我想我们的节目快结束了。你们想跳到聚光灯下吗？

帕特里克:是的！我先来。上周左右，我和 DJ Jazzy Jeff 在 Turntable.fm 上做 DJ，那不是我的焦点，我只是把它扔在那里；我想过让它成为我的焦点，但是，是的，我把它加入了我在 Google+个人资料上吹嘘的权利。

布莱德:你拿到徽章了吗？

Patrick: 不，转盘上没有徽章，但他们必须与开放徽章 API 挂钩才能实现。我想成为一名专业的 DJ。

布拉德:这可能是你应该写进简历的一条(笑声)。

当然，当我在 Webdevstudios.com 的 T2 申请工作时，我一定会带上那个徽章。是的，但是我真正的焦点是一个叫垃圾恐怖的网站。现在，你们谁是恐怖电影的粉丝？

布莱德:我是。

路易:算是吧。这么说吧，在我搬去和葛丽特一起住之前。

**帕特里克:**(笑)那 B 影迷呢？

布莱德:我喜欢家庭电影。

**Patrick:** 好的，那么你会喜欢这个网站的，[garbagehorror.com](http://garbagehorror.com/)是一个视频节目，我不知道他们的发布时间表，我以为是每周一次，但是他们会发布更多低成本恐怖电影的视频评论。所以，这是一个有趣的节目，由我的朋友乔纳森·贝利和他的另一半克里斯托·拉米共同主持，正如我所说，他们每集都评论一部不同的低成本恐怖电影。最新的是裸体吸血鬼，Birdemic，Vampaggedon，这是我的党，我会死，如果我想，狂犬病奶奶。

路易斯:我特别喜欢 Birdemic，因为它看起来和鸟一模一样。

帕特里克:对，对。

Brad: 令人惊讶的是，我看到的这些比我想承认的还要多(笑声)。

路易斯:患狂犬病的老奶奶是另一个—

布拉德:我见过患狂犬病的老奶奶；我见过男孩吃女孩。

帕特里克:哇，我很惊讶！所以，是的，你应该和乔纳森一起看这个节目，因为他们从粉丝的角度看这个节目，他们从这个节目中获得了很多乐趣，是的，所以如果你是一个恐怖片粉丝或 B 级电影粉丝，或者两者都是，一定要看看这个节目，这是一个很好的节目。

布莱德:是的，我喜欢。好吧，我去。所以我的重点是一位名叫 Mahdi Yusuf 的先生的博客文章，Mahdi 实际上在一致的基础上用许多不同的语言编程，有许多非常常见的，一些奇怪的，但你知道 JavaScript，Java，C，C++，Ruby，他很好奇他对每种语言敲得最多的键是什么， 因此，他实际上把他当天要写的一些代码放到了这个键盘热图网站上，它实际上显示在网站上，在博客帖子中，他有热图的截图，这些热图是他为这些语言编写的基本代码，你可以看到它们有什么不同，我认为这有点有趣。 这里有 PHP，其中一些语言和我想象的有点不同，所以你们可以，嗯，你们可能正在看帖子，但是你们怎么想？

帕特里克:是的，我们在作弊。

**Stephan:** 我还没看。

路易斯:为什么有这么多 E？

布莱德:除了 Lisp，E 在任何事情上都是第一名。

**Stephan:** 我还没看过，不过我会说 PHP 里的花括号，那是大的吗？

布拉德:它没有你想象的那么大。

啊，该死的！

布莱德:但是它在里面。我假设 e 和 PHP 是因为回声。看起来 E 和 T 是第二个数字，R 和 S 是第三和第四个。

**斯蒂芬:**有意思。

路易斯:我想这些只是单词中常见的字母，所以你需要做一些数学计算，试着推断出常见的关键字，因为基本上所有的变量名，比如购物车，或者其他什么，或者你使用的任何变量名都有英语中常见的字母，所以 E，R，T，S 可能只是他用英语写代码的副产品。

布拉德:是的，实际上如果你看了帖子，他确实提到了这一点。变量名，制表符和空格显然没有考虑进去，但这可能是第一个，空格显然。是的，但实际上在帖子的底部有一个链接，我们会确保把它放在展示笔记中，这个家伙的热图 JavaScript 文件建立了一个网站，你可以粘贴任何你想要的代码，它会为你绘制热图，所以如果你好奇，你可以拿一个代码片段或任何东西，任何类型的文本， 把它放在这里，它实际上会在键盘上实时绘制热图，这样你就可以看到哪些字母实际上被点击得最多，你甚至可以交换键盘布局，这样如果你是任何疯狂的 dvorak 粉丝或者甚至更疯狂的 Colemak 粉丝，你就可以在这个东西上获得各种乐趣。

Louis: 我有一个在大学编程的朋友，他用德沃夏克键盘，花了两个月的时间什么都打不出来(笑声)。

布拉德:事实上，我认识五六个德沃夏克的程序员，他们信誓旦旦地说，我只是没有足够的耐心去做。但它非常简洁，所以你可以在这里粘贴任何东西，显然他绘制了 JS 的 JavaScript 框架，这很酷，我可能会玩它，这是一个有趣的网站，但浏览不同的编程语言并查看按下了什么键也很简洁。

Patrick: 是的，你的聚光灯让我很好奇，在我猜语言中，对字母频率有一个大致的理解吗？我在找打字的，最常见的打字字母，但我找到的最快的东西是维基百科的字母频率页面，它引用了一项研究，我想是在 2000 年由 Wiley 发表的，从我在脚注中看到的，只是字母，在英语中最常见的字母是 e。

斯蒂芬:R

帕特里克:你为什么会猜？

布拉德:有道理，这是第一名。

斯蒂芬:我为什么会这么想？因为 R S T L N E，我的意思是来吧这是幸运之轮(笑)。

帕特里克:不，那很好。但 E 是唯一一个两位数百分比的，12.702%的英语是 E 后面跟着 T，A，然后它变得有点模糊，O 和 I。所以，这些是最常见的字母，我猜是键盘的 gibe，除了 Lisp 是数字，9 0，是的。

路易斯:这些不会是 9 和 0，而是 9 和 0 上面的括号。

帕特里克:你是个聪明人。

路易:(笑)

布拉德:是的，两个括号都是数字 1 和 2。

帕特里克:我唯一听说过的口齿不清就是口语。

Stephan: 我的这个星期不适合儿童，如果你对刺耳的语言敏感的话也可能不适合(笑声)。

帕特里克:但是你没听到我们之前做的吗，这是一个家庭友好节目。

斯蒂芬:塞缪尔·L·杰克逊引用的是 SL Ipsum 和 Ipsum 文本。

帕特里克:哦，天哪。

斯蒂芬:所以你可以想象这将会如何发展。不过，这真的很棒。

**路易:**看起来多半是低俗小说对白？

斯蒂芬:是的，大部分是低俗小说。

Brad: 我不认为我会很快在客户网站上使用它(笑声)。

斯蒂芬:你不这么认为？你觉得他们不会欣赏，我不知道为什么。

布拉德:我一直用占位符小猫图片，但这是，呃——

Patrick: 这是开发人员应该对他们的客户说的话；你想要一个“哔”的标题标签吗？是的，我要一个“哔”H1。

布拉德:这很有趣。

斯蒂芬:这很有趣。

路易斯:是啊，很有趣。我喜欢所有这些事实——我的意思是，这似乎是一种使用各种替代语言或方言的 Lorem Ipsum 生成器的趋势，所以看到新的生成器非常有趣。这是特别好的，因为它是连贯的，看起来像是他们拿走了整段文字，而不是—

Stephan: 只是碎片。

**Louis:** 而不仅仅是随机生成单词，所以它实际上是可读的，这很酷，因为你会比通常使用 Lorem Ipsum 更多地被内容吸引。

Patrick: 你会注意到默认发送给你的是 Slipsum Classic，不适合工作，他拿着一把枪，但如果你点击 Classic Ipsum，他拿着一个——哦，天哪，像卷纸、卷轴，如果你点击 Slipsum Lite，他拿着一束花，一个不那么令人讨厌的占位符文本生成器。

**Louis:**slip sum Lite 是什么？我只想看看 Slipsum Lite 是什么。

布拉德:它说这是一个不那么令人不快的占位符文本，所以也许这是一个有趣的网站。

帕特里克:是啊，没那么冒犯人了。我得到的是，“我像心脏病发作一样严重，现在我们知道你是谁，我知道我是谁，我不是一个错误，”所以，是的，这就是它的开始。

路易:这些也是塞缪尔·L·杰克逊的名言，但可能来自其他不那么经典的电影。

Patrick: 除了经典的，经典的只是你在其他生成器上得到的普通类型的 Lorem Ipsum 文本。

路易:对，只是普通的。

帕特里克:学者塞缪尔·l

路易:但是如果你不想要那些低俗小说，去这个网站真的没有意义。

Patrick: 我认为每个网站的顶部都应该有一个过滤器，要么是塞缪尔·L·杰克逊化的，要么是简约的，要么是经典的，这样就可以过滤整个网站。抱歉，没有人喜欢这个主意。

(笑)好吧，我会让你负责生成 WordPress 插件，把我们的网站翻译成塞缪尔·L·杰克逊语，如果你这么做了

帕特里克:我将担任创意总监，布拉德将负责开发。

布莱德:这是我的首要任务。

帕特里克:我们会处理的。

路易斯:酷。我本周的焦点是一个名为 [FutureFriend.ly](http://FutureFriend.ly/) 的网站，我想这是一个开放网络的宣言，我知道对很多人来说这是一个令人厌倦的话题，但如果有人本周一直关注网站开发者/设计师 Twitter，过去一周在 Joe Hewitt 和其他人之间爆发了一场大辩论。读写网上有一个关于它的故事，布鲁斯·劳森的网站上有一个关于它的故事，说的是一场大辩论，基本上乔·休伊特说了一些关于如何—

帕特里克:他说网络糟透了。

Louis: 他说，“HTML 和公司需要其他平台所拥有的东西，一个单一的源代码库和一个好的所有者来驱动它。一个标准的主体并不适合扮演这个角色，浏览器厂商正在一些领域进行创新，但他们被标准的过程所阻碍。”所以他对标准过程进行了猛烈的抨击，很多人，布鲁斯·劳森，约翰·奥尔索普和其他人都站出来为这一点辩护，但我不确定这是否是对这场辩论的回应，或者是在人们进行这场辩论的同时出现的。所以这是 futurefriend.ly，它只是一封公开信或宣言，我想这是关于标准的过程以及这种东西是如何产生的，设备的变化和访问模式的变化，我们一直在走的路是一个开放标准的开放网络仍然是一个好的解决方案，仍然是最好的解决方案，它有一个资源页面，包括许多很酷的文本和想法以及其他关于如何接近现代网络的文章，多种屏幕尺寸等等。所以这是一个设计非常简单的网站，我想很多设计师和开发者都会喜欢它。

帕特里克:我要求他们允许你或凯文签署这份文件；我真的不在乎是哪个，只要是上过播客的人。我只是在开玩笑。为什么它要求我允许存储数据供离线使用？

路易:我不知道。

帕特里克:它要我签名吗？我将单击“允许”。

路易:我的没有那样做。

**Patrick:** 不知道，顶部提示，允许存储离线使用，我点了允许，真的什么都没发生。

路易斯:你用的是什么浏览器？

帕特里克:火狐，最新版本。

路易斯:也许 Chrome 不会提示你这一点；也许 Chrome 只是接受离线存储。

帕特里克:这就是谷歌之道(笑声)。

路易斯:嗯，我不知道，现在很多网站都使用离线存储。它有一份缓存清单。打完电话后，因为我不希望你现在切断网络连接，因为那样我们就会失去你，但是打完电话后，你可以切断网络连接，试着在页面之间来回浏览，然后我猜它仍然可以工作。

帕特里克:优秀。

Louis: 他正在使用 HTML5 的缓存清单和一些离线存储的东西。

Patrick: 它的功能很友好，所以当我们生活在互联网被切断的日子里，它仍然可以工作(笑声)。

**路易:**正是，僵尸启示录之后。

帕特里克:完全未来友好。

路易斯:这就是未来友好的真正含义，你的网站会在僵尸末日中幸存下来吗，这就是未来友好。好了，我们已经跑了一会儿了，让我们结束吧。

布拉德:我是布拉德·威廉姆斯，网络开发工作室，你可以在推特上找到我[@威廉斯巴](http://twitter.com/williamsba)。

我是 iFroggy 网络的帕特里克·奥基夫，我在 managingcommunities.com 的[写博客](http://managingcommunities.com/)，在推特上写博客 [@ifroggy](http://twitter.com/ifroggy) ，i-f-r-o-g-g-y

斯蒂芬:我是斯蒂芬·塞格拉夫斯；你可以在 badice.com 的[上找到我，我在推特](http://badice.com/) [@ssegraves](http://twitter.com/ssegraves) 上。

**Louis:** 你可以在 Twitter 上关注 SitePoint[@ sitepointdotcom](http://twitter.com/sitepointdotcom)，那是 sitepointdotcom 你可以在推特上关注我 [@rssaddict](http://twitter.com/rssaddict) 。我们很想听听你对所有这些故事的想法，所以你可以在 podcast@sitepoint.com 给我们发电子邮件，或者你可以去[sitepoint.com/podcast](https://www.sitepoint.com/podcast/)对这个节目发表评论，所以我们很想听听当 MySQL 商业化时你对脸书页面的体验的替代数据库。感谢您的倾听。

迈克·梅拉的主题音乐。

感谢收听！请随时让我们知道我们做得怎么样，或者使用下面的评论栏继续讨论。

## 分享这篇文章