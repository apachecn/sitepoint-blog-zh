# SitePoint 播客#156:重访付费墙

> 原文：<https://www.sitepoint.com/podcast-156-paywalls-revisited/>

SitePoint 播客第 156 集现已推出！本周，这个小组由路易斯·西蒙诺([@ RSS addic](http://twitter.com/rssaddict))、凯文·迪斯([@凯文迪斯](http://twitter.com/kevindees))和帕特里克·奥基夫([@伊罗格](http://twitter.com/ifroggy))组成。

## 下载这一集

你可以下载这一集作为独立的 MP3 文件。以下是链接:

*   SitePoint 播客#156:重访付费墙

## 剧集摘要

以下是本期节目涉及的主要话题:

*   [内容折叠| CSS-Tricks](http://css-tricks.com/content-folding/)
*   NYT 的支付墙嗡嗡作响
*   [纽约时报付费墙每月用户接近 50 万](http://www.readwriteweb.com/archives/ny_times_paywall_nears_half_a_million_monthly_subs.php)
*   [呼吁渐进式改进|斯蒂芬妮·里格尔](http://stephanierieger.com/a-plea-for-progressive-enhancement/)
*   [为什么开发者应该担心 Google Play](http://www.zdnet.com/blog/violetblue/why-developers-should-worry-about-google-play/1148)

在[http://delicious.com/sitepointpodcast/156](http://delicious.com/sitepointpodcast/156)浏览展览中引用的链接的完整列表。

## 主聚光灯

*   帕特里克:[书籍有着广阔的未来——Readmill](http://readmill.com)
*   Kevin:[Rainbow–Javascript 代码语法高亮显示](http://craig.is/making/rainbows)
*   Louis: [替换-9999px hack(新图像替换)–Jeffrey Zeldman 提交每日报告](http://www.zeldman.com/2012/03/01/replacing-the-9999px-hack-new-image-replacement/)

## 采访记录

路易斯:大家好，欢迎来到 SitePoint 播客的另一集。我回来了；我已经离开了几个星期，和我哥哥一起在澳大利亚旅游，他要来看我。我们带着展板回来了。不幸的是斯蒂芬今天不能来，但你好帕特里克和凯文。

凯文:你好，你好。

帕特里克:嘿，伙计们。

凯文:最近怎么样？

路易:进展顺利。感谢你参加这些访谈节目，凯文，非常感谢。

凯文:哦，没问题，很有趣，一群非常好的客人和有见识的人。

路易斯:不错。

帕特里克:这听起来有点太圆滑了(笑声)，有点太圆滑了，“一群好客人和有见识的人，”眨眼！

凯文:没错。

帕特里克:嗯，已经整整一个月了，自从我们见面并做了一次集体表演已经整整一个月了。

路易:对。

Patrick: 我去了 SXSW，凯文就是在那里接受采访的，我在那里和他打了个招呼，是的，只是旅行、生病、感冒等等，但是回来真好。

凯文:但是你现在好多了，对吗？

帕特里克:我是。我几乎是 100%。

凯文:干得好，耶。

帕特里克:是的。这不像前几天晚上我忍不住咳嗽的那个糟糕的播客；我不得不每隔 30 秒就关掉麦克风。

凯文:是的。

帕特里克:路易斯，你哥哥住在哪里？

路易斯:住在蒙特利尔。

帕特里克:好的，哇，那真是一次不寻常的旅行。

路易斯:是啊，一次不寻常的旅行，相当有意义。

帕特里克:我哥哥住的地方离这里只有四个小时的路程，所以我很感激(笑声)，没那么长。

路易:这更容易管理一点。这周谁想先讲一个故事？

凯文:我先来。我有一篇来自 Chris Coyer 的关于内容折叠的文章，他几天前发表了这篇文章，它有点相关，但同时它有点未来友好，不一定是现在友好，如果这有意义的话。但基本上，他是在谈论你用响应式网页设计流动内容的方式，以及 W3C 正在做的与此相应的事情——我认为是 Regents 模块。基本上，他做了一个由 Adobe 捐赠的有趣技术的小教程，关于如何根据屏幕的宽度将内容从一个分区发送到另一个分区。因此，如果你能想象像大多数网站一样，你有一个侧边栏和一个内容区，然后当你降低屏幕尺寸时，侧边栏往往会一直跳到底部，所以如果你有六七个项目，它就不是很有用，特别是如果那些是广告。因此，它允许你在主要内容区域的文章内或文章之间创建所谓的 div，当屏幕尺寸下降而不是将所有广告拍摄到底部时，你可以在适当的时候将它们放在文章之间。这是一种有趣的方法，不过，我确实想问你这个问题，Louis，因为在这个例子中，这里有一些空的 div，我不知道，我的意思是你可以随时将它添加到字段 div 中，但我觉得空的 div 会成为一种标准的东西，我不知道这背后的语义是什么。

Louis: 嗯，有几件事，第一，语义上，至少根据 W3C，语义上 div 是中性的，它没有语义意义，所以如果你要放一个空的东西，它也可以是一个 div，因为它在语义上没有任何意义，对吗？如果你有一篇语义混乱的文章。

凯文:对。

Louis: 但是理论上的 div 是语义中性的。现在，很明显，它会使你的标记变得混乱，很多人是这类事情的纯粹主义者。

凯文:对。

路易斯:但是，是的，我的意思是这显然是一件重要的事情。我认为，很多响应式设计的尝试都有过这样的时刻，你会想，嗯，我真的不知道把所有这些东西放在哪里，我不能只使用 CSS 来智能地集成它们，所以我要把它们都放在底部；在很多情况下，这是一种逃避，对，有时你在侧边栏中的东西是有用的，我的意思是，有时你会在侧边栏中沿着顶部有导航，然后可能会有一些类别链接或博客的月度档案列表，例如，如果在底部，那就是 find，但正如你提到的，将所有广告放在底部不会让你的广告商非常高兴。

凯文:对。没错。你知道，这让我想起了人们使用 span 标签来创建许多东西的前后效果的日子，比如圆角，那里只有一个空元素，然后你会给它一些宽度和高度来添加圆角，然后他们推出了 Dan Cedarholm 谈到的牢不可破的盒子模型， 所以看到这种事情再次出现是很有趣的，然后我看到它将如何在未来收回，至少希望是这样的。

Louis: 是的，我的意思是我的直觉是那些空白的 div 不应该出现在那里，但如果你想要一个真正自适应的布局，事情可以非常灵活地移动，并且你不想使用 JavaScript 来实现，这可能是最好的解决方案。 虽然可能有人会认为使用 JavaScript 来做这件事是完全合法的，因为你的目标移动设备是 iPhones 和 Android 设备，几乎都将拥有非常强大的 JavaScript 引擎，而只是使用 JavaScript 来移动这些内容可能是一个好主意。 或者，你知道我在上次访谈节目中与卢克·乌鲁布莱夫斯基交谈过，他的移动优先的方法是看待这个问题的一种有趣的方式；如果你的广告实际上包含在原始标记的内容中，当屏幕足够宽时，你将它们移到一边，这可能是一种不同的方式。

凯文:是啊，是啊。老实说，我其实更喜欢这种方式。但我不知道，因为如果你要使用这种方法，我不想太过技术化，但如果你要将这种方法从桌面携带应用到移动设备，移动设备在未来会对这些功能提供更好的支持吗？是啊，有很多东西要考虑。

**Louis:** 是的，事情变得有些棘手，但我绝对认为这是人们应该考虑的一种方法，我认为人们在接近响应式设计时不要只是把边栏里的东西放在底部，这一点很重要。

凯文:是的。

**Louis:** 因为这并没有真正把移动作为自己的平台，对，你所做的就是，嗯，我的空间更小了，我有所有这些东西，我会把不适合的东西放在顶部，我会把它放在底部，当你这样做的时候，你根本没有真正考虑移动用户体验。因此，我认为考虑这一点很重要，并开始使用这些类型的技术来简化这一点，因此，在这种情况下，这是在 CSS 中完成的，他使用某种浏览器检测进行回退。你知道，我认为这很棒，一旦它得到很好的支持，每个人都可以这样做，事实上，当你建立一个新的网站时，这将更加令人兴奋。

凯文:是的，我同意你的观点，我认为随着我们看到整个空间进一步向前发展，这将成为一种持续的标准，你随时都可以去——网站上有演示和文件，你可以下载亲自观看。

Patrick: 我不得不说，这是一次很棒的谈话，一次我完全没有资格参与的谈话(笑声)，所以我很乐意倾听。但我只是——我的想法是，你可以从一个 div 获取内容，并让它出现在另一个 div 中，我的意思是，我的想法是围绕这个概念。所以我就把它踢到一个我比较有资格讲的故事上。

所以我们经常在这个节目中谈论付费墙，我会说，我在我们的档案中快速搜索了一下，看到我们在第 73 集与英国的《泰晤士报》谈论了它，以及他们正在做的实验，我们在第 130 集与《波士顿环球报》谈论了它，然后，路易斯，你在第 132 集采访了伊桑·马科特，并简单地提到了它， 所以我发现了一个有趣的故事，链接到读写网，实际上是由哥伦比亚新闻评论的审计博客报道的，基本上是关于纽约时报数字订户的成功，换句话说，他们是付费墙。 从数字上可以看出，2011 年第二季度，他们有 281，000 个数字用户，2011 年第三季度增加到 324，000 个，第二季度增加到 390，000 个，2012 年第一季度增加到 454，000 个。因此，就付费数字用户访问付费墙后面的《纽约时报》内容而言，一些令人印象深刻的增长，这些用户每月支付 15 至 35 美元；15 美元，你可以在智能手机上访问网站，20 美元，你可以在平板电脑上访问网站，35 美元，你可以获得所有内容，当然，他们的印刷版订户，在家阅读《纽约时报》的人，他们通过付费印刷版订阅获得这些内容，他们不考虑这些数字，严格来说，这是为数字订阅付费的人。

因此，从这个角度来看,《纽约时报》至少在付费墙模式上看起来相当成功，我不知道其他出版物是否会复制这种模式，但至少在美国报纸的情况下，其他一些大公司已经宣布计划转向更多基于付费墙的货币化模式。据读写网报道，美国另一家大型出版商 Gannett 宣布，他们计划建立自己的付费墙，而经营中西部报纸的小型出版商 Lee Enterprises 也宣布了类似的计划。

那么，这些数字改变了你们中任何一个人对付费墙的想法吗，我的意思是这改变了什么吗？我知道我之前提到的第一集是在路易斯还没有出演这部剧之前，我不认为——我认为是凯文，凯文不太赞成付费墙的想法，但是这些数字说明他们可以成功吗？

路易斯:嗯，这当然说明了他们能够成功，因为在这个案例中他们是成功的，对吧。

帕特里克:是的，绝对是。

**Louis:** 现在，我想澄清一下,《纽约时报》确实是一个不完整的付费墙，对吧，它不是——如果你不付费，在一定限度内，内容仍然是可以访问的，一旦你在网站上试图查看的故事达到一定数量，而你不是订阅者，它就会说嘿，你经常使用这个，你似乎喜欢我们的内容，你可能想要付费，对吗？

帕特里克:对。没错，现在的数字是每月 20 篇文章，到了 4 月份，他们会把这个数字降到每月 10 篇。

**路易:**对。所以这是一件事，它不会破坏你在网上分享故事给那些可能对阅读《纽约时报》所有内容不感兴趣的人，或者那些可能通过分享故事而获得兴趣的人的能力，对吧；我们都有这样的经历，如果有人在 Twitter 上发布了一些东西，你点击进入，因为你不是订户，你要么被带到这个网站的主页，要么只是一个登录页面。

帕特里克:当然可以。

路易斯:这甚至让我没有机会看到，如果我发现自己经常使用它，我会愿意为它付费吗？这是一面墙，对吧。

帕特里克:是的。

**Louis:** 此外，另一件事是《纽约时报》是一份标志性的出版物，拥有如此多的高质量内容，以及如此多的高质量报道和编辑内容

帕特里克:建立信誉。

路易:对。不仅仅是建立了信誉，他们已经有了信誉，而且他们还在做报道，可能还在做——我的意思是他们仍然有比许多其他小报纸更好的作家和记者，对吧。所以，愿意为《纽约时报》付费，还是愿意为你的本地报纸或不太知名的全国性报纸付费，我想还有待观察，我们会看到在这方面的其他尝试有多成功；看起来，如果有人想要成功，那么这样做是最好的选择。如果成功了，那么我们必须尝试其他不太可能成功的事情，但是谁知道呢。

Patrick: 是的，你提出了几个很好的观点来指出它与不仅仅是付费或免费的区别，实际上我在《纽约时报》的新闻稿中读到了几个观点，他们将把它减少到每月 10 篇文章，但在智能手机和平板电脑应用程序上，头条新闻部分已经是免费的，并将继续免费，所以他们已经允许人们在智能手机和平板电脑应用程序上访问最受欢迎的新闻故事。此外，根据新闻稿，通过电子邮件、搜索、博客和社交媒体链接的读者将继续能够访问这些个人文章，即使他们已经达到了阅读限制；对于某些搜索，用户每天最多只能获得五篇时报文章的免费链接。所以我想，如果你愿意的话，会有这种后门，如果他们的文章来自不同的来源，他们允许人们阅读他们的文章，并且仍然允许时报利用社交媒体的力量，如果你愿意，这是一种过度使用的短语，但如果人们通过社交媒体分享时报文章的链接，那么这些仍然是可以访问的。因此，这是一种有趣的混合方法，不是简单的要么全部要么什么都没有，例如，他们仍然从 Twitter 上的头条新闻中受益，同时仍然保持对最活跃的读者的一些控制，这些人确实消化了他们的大量内容。

是的，我不得不说我更喜欢这种方式，而不是免费的方式，但是报纸在社交媒体分享方面走得太远了，比如脸书的被动阅读分享应用，我想是《卫报》和《华盛顿邮报》。

帕特里克:对，是的。

路易斯:这真的很烦人，就像不添加应用程序就不能点击链接一样。

帕特里克:对。

路易斯:如果另一种选择是，你知道，你要么强迫每个人被动地分享你的东西，只是为了能够看到它，要么你允许他们分享，如果他们想分享，但如果他们经常使用它，你就让他们付费，我更喜欢部分付费墙，而不是其他出版物已经采用的全面社交媒体媒体。

Patrick: 你提到这一点很有趣，因为我点击了几个链接，就像你说的，被动分享或其他什么的，它要求我添加应用程序，我并不喜欢在这一点上激进，但我仍然没有添加应用程序。

路易斯:不，因为在你读它之前，它就已经开始分享了。你会注意到，通过《华盛顿邮报》和《卫报》应用程序分享的大多数东西都是非常非常轰动的头条，因为人们喜欢这些东西，哦，我想看看那是什么，这不是你在阅读后会分享的东西，而是你希望能够阅读它。你知道，我通常只是——我会用谷歌搜索这篇文章，单独找到它，而不添加应用程序，因为——

帕特里克:准确地说。

路易:对。

帕特里克:(笑)当然。

路易斯:总之，我很高兴听到这些出版物可以自己谋生，或者在互联网上为自己开辟一个利基市场，而且互联网并没有给传统报纸和印刷出版物带来厄运。

帕特里克:绝对是。是的，对《纽约时报》来说，我的意思是，就像你说的，他们仍然很庞大，但这是一笔可观的钱，即使你假设所有的订户都是每月 15 美元的计划；454，000 个用户乘以 15 美元就是 680 万美元，在一年的时间里，这个数字膨胀到 8170 万。所以，即使对像《泰晤士报》这样的大公司来说，我认为这仍然是一笔可观的收入。

路易:对，我不会拒绝。

帕特里克:(笑)SitePoint 也不会。

路易斯:好了，继续，这周我有一个小故事，回到一些书呆子身上，因为那是我们在这里做的。

帕特里克:当然可以。

路易:对不起帕特里克。

帕特里克:我现在要安静了(笑声)。我去角落里戴上我的傻帽。

路易斯:这是斯蒂芬妮·里格尔写的博客，我想我没读错。这是一段时间前，它是在一月份发布的，但我只是在几周前看到它，我想我应该把它传下去，以防有人错过它；这是一种对渐进式改进的恳求。所以，她想说的是，很多人似乎已经赶上了响应式网页设计的潮流，但却没有注意到传统的响应式网页设计的最佳实践技术，而只是让网站在尽可能多的设备和尽可能多的背景下都可以访问。所以，她注意到巴拉克·奥巴马即将到来的美国总统大选的新竞选网站，这是一个响应式的设计，如果你在移动设备上查看，它会折叠起来，它有这种滑出式菜单，你点击菜单按钮，它会打开一个大菜单，这样你就可以浏览网站。然后她在 iPhone 4 上完全失败了，于是她去做了一点测试，想知道这个菜单实际上在什么设备上工作，发现她唯一能让它工作的设备是升级了 IOS 5 的 iPhone 4 或最新的 Android 手机 Galaxy Nexus。它在 iPod Touch 上失败了，在没有升级的 iPhone 4 上失败了，在之前的顶级谷歌设备 Nexus One 上失败了，在全新的 Windows Phone 7 设备上失败了，还有这个庞大的列表，Kindle Fire，她测试的所有 Android 平板电脑，只是，你知道，它没有优雅地降级。

所以她写了这篇非常引人注目的文章，整篇文章试图促使 Web 设计师和开发人员不要忘记那些传统的渐进增强技术，真正专注于提供可以在任何设备上工作的东西，即使它没有你期望的屏幕大小，或者它没有你期望的 JavaScript 性能或行为，你真的必须有一个工作良好且可用的基线， 然后你可以在最新一代的设备或浏览器上满足你的幻想，但你真的必须让东西变得可访问和可用。 响应式设计本身并不是解决这个问题的灵丹妙药；它没有解决让你的网站降级的问题。

凯文:是的，感觉就像整个前缀的事情又来了一遍，除了在响应方面，在我看来，开发者只是变得有点懒惰，只是做好工作，你知道我的意思。

**Louis:** 是的，在这种情况下，显然在移动设备上，我的意思是，我们作为开发人员很长一段时间都很难做浏览器测试，对，如果你想在你的机器上安装两个或三个版本的 Internet Explorer，加上 Chrome、Safari 和 Firefox，这可能会很棘手，如果你使用 Mac 或 Linux，你必须在某种虚拟机或双启动场景下安装 Internet Explorer，然后获得多个版本的 Internet Explorer 就更棘手了。这很棘手，但在移动设备上就更难了，因为你必须拥有这些物理设备，所以，你知道，这种测试并不容易，但我认为如果你是一个专注的机构，特别是我想象巴拉克·奥巴马总统竞选网站的预算就网页设计而言有点偏高，所以承担这种项目的机构会希望能够做一些测试。

Patrick: 他们的志愿者数量庞大，他们一定都有不同类型的手机，他们可以打电话进来。

路易:没错，绝对没错。我将从这篇文章的结尾引用一点，在那里她差不多把她的观点带回家。“奥巴马网站的失败不在于使用了像反应式设计这样的新技术；人们忘记了旧的原则和技术在建立一个更好的网络中仍然扮演着重要的角色。如果说有什么不同的话，那就是它们比以往任何时候都更重要。没有渐进式改进，响应式设计只是一个当你调整桌面浏览器大小时看起来很漂亮的网站，而有了渐进式改进，移动网络真正成为一个能够接触和连接我们所有人的工具。”

所以，举例来说，至少你的菜单可以优雅地失败。这是一个 JavaScript 菜单，所以如果你使用 JavaScript 来显示某个东西，在网络上的传统智慧或渐进增强，是让它默认打开，然后在页面加载后用 JavaScript 隐藏它，这样如果你的 JavaScript 失败了，菜单仍然是可访问的，对吧，在这种情况下没有这样做。

凯文:如果其他方法都失败了，至少你知道巴拉克使用这两款手机中的一款，Nexus 或 iPhone(笑声)。

帕特里克:他要么用最新的 iPhone，要么用最新的 Android，所以。

路易:对。还指出它在一些黑莓设备上失败了，所以不令人印象深刻。

Patrick: 对于美国的开发者来说，这是一个足以改变你投票的大问题，所以一定要看看他网站上的代码，看看它是如何基于标准的，看看它看起来如何，然后在此基础上投票。

路易斯:我认为你必须比较所有候选人的移动网站，你可能会发现各方面都有相当大的失败；我不知道是否有人有很棒的手机。

凯文:也许这意味着他们会给我们免费的手机，这样我们就可以访问网站了，这很好，我很喜欢。

路易斯:好吧，我认为这是一个致命的错误。

凯文:我还有一篇文章要说，是关于 Google Play 的。现在，当然，已经有一段时间了，但我相信从我读到的内容来看，Google Play 于 3 月 6 日发布，从那以后，它在社区中引起了一点骚动，特别是在 Android 中，他们基本上改变了应用程序背后的政策。现在，有一个到 ZDNet 的链接，他们在那里讨论这个问题，所以我想找几个地方，因为它已经发布了一段时间了。但基本上 Google Play 已经改变了他们的部分政策，Reddit 很有趣，这是一个 Android 应用程序，在政策计划上遇到了一些问题。如果你不知道什么是 Google Play，那就去 play.google.com 看看，你可以从那里找到答案，现在我自己还没怎么用过它，事实上我今天第一次看了它，基本上它是谷歌产品的集成，有点像亚马逊 Prime，你可以在那里看到电影、音乐、书籍，现在安卓市场都被拉进来了。因此，据我所知，在较新版本的 Android 上，你现在使用 Google Play 来购买应用程序，而不一定是旧的 Android market。

路易斯:是的，实际上我在旧版本上也有更新——我有一个运行 2.3.4 版本的旧 Android 设备，所以有点过时了，但我仍然有更新，可以切换到游戏。

帕特里克:好吧，那是什么，融化的冰淇淋三明治？(笑声)

**路易斯:** Aw，帕特里克。

凯文:很好，非常好。

帕特里克:我要说的是，作为一个拥有廉价预付费手机的人，我没有智能手机，所以，你知道，我不能再谦虚了(笑声)。但是，是的，Google Play 现在是 Android 市场，我的意思是它已经被重新命名；Android 市场已经不复存在，Google Play 就是它。

凯文:好的。是的，我用的是 iPhone，再说一次，我今天刚刚看了这个，所以我在上面的信息并不完美，但是，你知道，从这个像他们的顶级应用程序之一，Reddit 很有趣，正在遇到这个问题。

根据这篇文章，这是一款拥有 30 万活跃用户的应用，它是 Android 市场上最受欢迎的 Reddit 应用，但最近因违反政策被禁止并从 Google Play 中删除。给开发者的邮件说这违反了内容政策中的色情内容条款。因此，基本上它将用户链接到 Reddit 首页和 Reddit 主题，我想如果这些链接反过来链接到，比如说工作内容不安全，谷歌认为可以从市场上阻止它。

帕特里克:对。现在，就我个人而言，在所有条件相同的情况下，我不反对谷歌在他们的市场上这么做，但这里的问题是，当我说不应该是这样的时候，我觉得自己像乔恩·斯图尔特，谷歌，(笑声)；这应该是一个明确的选择，对吧，这没有好坏之分，我不认为这两种方法有好坏之分，但是苹果的，显然是苹果的，iTunes 和 app store，市场就在那里，他们对这些有严格的限制，他们管理得非常紧密。Android marketplace，以及 Android 作为一个平台，或多或少，应该更加开放。现在，我认为这里最大的问题是，我认为人们对谷歌的期望来自谷歌自己的行动，以及他们对 Android 的行动，现在感觉好像他们在成人内容上对他们有所不满，也许感觉有点被背叛了。

凯文:是的，我真的不太理解谷歌在这个问题上的立场，因为我的意思是，如果你在这里有一个 Twitter 账户，有人给你发了一条明确的推文，比如，谷歌现在会禁止 Twitter 吗？

**Louis:**Twitter 客户端，是的，这是一个非常好的点，它变得非常模糊，对，是一个从内容到互联网上某些东西的链接。

凯文:对，没错。

帕特里克:当然可以。是的，我想到了——当你大声朗读时，我想到了论坛阅读应用程序，对，所以某些论坛有应用程序，也有阅读论坛的应用程序，当然有些论坛可能会有这种内容的主题，甚至可能是一次性的某种非典型内容，然后就导致了这个问题。我的意思是，这可能是一个极端的例子，但我想这就是斜率。

路易:对。

凯文:对。

**Louis:** 不过，公平地说，回到你从 ZDNet 链接到的原始帖子，它指出了一些来自运行独立 Android 市场的开发人员的报价，所以 Android 有趣的地方在于，因为安装应用程序所需的只是获得应用程序安装程序文件并在手机上运行，所以你基本上可以创建自己的市场。所以，他们从竞争对手那里得到一些报价，说，你知道，很多受到影响的开发者联系了他们，他们试图推进这一点，所以这并不意味着不可能在手机上获得这些类型的应用程序，但在 Reddit 的情况下，我认为正如我们所说的，这不是一个真正的成人应用程序，或者它不是一个明确的应用程序， 它只是-潜在地链接到可能链接到明确内容的东西，这真的很难证明禁令的合理性。

帕特里克:那里有很多潜力。

路易斯:嗯，是的，这就是问题的核心。

帕特里克:是啊，是啊。不过，你说得很好，就 Android 的开放性而言，你可以有其他的应用程序商店，对，亚马逊已经创建了一个，还有其他的可供人们使用。在这种情况下，我不会求助于亚马逊，因为我认为他们也不想要你的应用程序，但是，我会说它创造了一个可能的业务，对吧，因为如果——这只是我的企业家观点，但是如果他们不允许成人应用程序，那么你就可以创建一个成人应用程序商店。

**Louis:** 是的，这是从这里链接到的，我不会删除链接，因为文章中已经警告过，网站文章对工作不安全，所以我不会删除链接。

帕特里克:你可以谷歌一下！讽刺的是，你可以谷歌一下(笑声)。嗯，但是，是的，我的意思是，这就是企业家精神，你可以创建一个成人应用程序商店，不要称它为应用程序商店，因为苹果公司不这样做——他们正在追赶亚马逊使用这个术语，称它为其他东西，称它为成人 Android 应用程序市场，或任何东西，但是，开始你自己的应用程序商店，并占领这个市场。

凯文:是的，无论如何，Google Play 让我们来看看，我相信 Google Play 在某种程度上是谷歌对亚马逊应用商店的回应，或者基本上是 Kindle Fire 与电影、书籍和应用程序的集成。

路易斯:是的，我的意思是，显然 iTunes 一直以来都有这种综合商店，对吧，它从来就不是一个独立的应用商店，或者说不是你可以购买音乐和书籍的地方。

帕特里克:是的，他们在《时间 1》问世之前就有了，所以。

路易斯:哇！那是一个参考。(笑声)现在我只觉得难过。

帕特里克:这种悲伤是聚光灯的完美延续(笑声)。

**凯文:**难过的时候。

**Patrick:** 我先来，因为我们现在在 app store，app talk 上，所以我的焦点是一款阅读应用，完全专注于苹果，这是一款名为 Read Mill 的苹果电子阅读器，我在 SXSW 见到了创始人之一，他的名字叫 David，他向我展示了这款应用，尽管我没有苹果设备，所以我无法亲自体验。 但我在他的设备上玩了一下，他给我看了一下，他们有一个网络界面，他们还允许你从你的 Kindle Fire 或 Kindle library 导入一点东西。 但基本上它是一个——所以它是一个电子阅读器，但更多的是，你知道，他们称自己为一个好奇的读者社区，突出和分享他们喜欢的书籍，所以它非常基于突出书籍的摘录，分享有趣的想法，对它们进行评论，社区中有作者突出小的摘录，然后为那些想法提供额外的背景。它真的非常巧妙，我觉得如果你在苹果设备上，在 iPad 或 iPhone 上做大量阅读，并且你喜欢和正在读书的人交谈，或者分享你正在读的书，它看起来就像一个非常非常巧妙，非常非常好的应用程序和网站，我自己已经开始玩它了。因此，如果你是一个铁杆读者，并且想在苹果设备上阅读，一定要看看它，那就是 readmill.com 的[。](http://readmill.com/)

凯文:非常酷。但是我有一个聚光灯，这是一个非常有趣的网址，所以这就是为什么聚光灯很棒，所以它是 craig.is/making/rainbows 的，我认为它很棒。

帕特里克:是的。

Kevin: 这是一个 JavaScript 库，类似于一个库，你可以安装和运行多个脚本，但基本上它是网页中代码的语法高亮显示。我不得不说，我以前为 WordPress 和 Drupal 使用过几个类似的代码语法高亮插件，还有像这样的标准插件，这一个肯定是采用了所有的实现方式。就语法而言，它使用了一些不错的 HTML5 材料，你可以创建自己的表达式；它使用正则表达式来生成内容，或者解析内容，并确定您想要复制什么类型的代码包，我不知道还能怎么说，或者代码块，并且，您知道，可扩展，它非常非常好，并且您只包含您想要的包。所以，如果你想要 Python，那么你可以只包含一个脚本，我在这里笑了一下，因为这太酷了(笑声)。

帕特里克:我笑是因为这太酷了。

凯文:天气很凉爽。很牛逼；它有很棒的文档，伙计们看看吧。

帕特里克:这是 1.2 千字节的彩虹惊奇。

凯文:没错。我知道你在想，这是双彩虹。

**路易:**正是；确实是。

凯文:几乎是三重彩虹。

路易:要是我知道这一切意味着什么就好了。好的，我们成功通过了凯文的聚光灯，我们成功了。酷毙了。

帕特里克:路易，你的重点是什么？

**Louis:** 我的关注点又回到了之前，因为就像我说的，我有一点点被切断了，但这是独一无二的 Jeffery Zeldman 几周前在他的博客上写的一篇文章，他指出了一个叫 Scott Kellum 的人开发的一种新的图像替换技术。基本上他的观点是，我们一直在使用的传统的 FARK 图像替换，这是一种减去一些淫秽像素的文本缩进，以便将文本从屏幕上删除，并用背景图像替换它，这是一种伟大的技术，我们一直都在使用它，只要我们一直在做 CSS。然而，如果你在 CSS 中制作动画，它确实有一些缺点，因为浏览器实际上会绘制一个巨大的框，这个框延伸到负文本缩进的范围，当你试图在一个可能没有它应有的速度或功能的设备上制作动画时，会导致一些性能问题。有人刚刚开发了一种替代技术，将文本缩进 100%并隐藏溢出，这种技术只是强制文本出来，然后隐藏溢出。所以，如果你还没看过的话，可以看一下，我知道对很多人来说，很难打破像负 9999 像素这样的习惯，因为它在我们做 CSS 的方式中是如此根深蒂固，但是有替代方案，而且是新的替代方案，在某些情况下可以表现得更好。

凯文:这很酷，实际上我很喜欢。这是一个很好的链接。

凯文，你和 SXSW 的泽尔德曼先生谈过吗？

凯文:我没有；我没有。我想给他一个拥抱，但这可能有点过了(笑声)。

帕特里克:尴尬的沉默。(笑声)我很喜欢，可爱的，好的聚光灯，绝对的。

**Louis:** 我们收到了 Zach Wong 的一封电子邮件，我将把它读出来，“嗨，SitePoint 播客，我想说我是你们播客的忠实听众，感谢你们发表的有见地的评论。”

**帕特里克:**甜！谢谢你。

**Louis:** “我也刚刚听了 2011 年的最后一个专题节目，我只是想给你一些关于广告的想法，就我个人而言，我在访问时并不关心广告，我认为作为网络观众，我们已经习惯了它们，现在我们只是忽略它们。我要点击它们吗？如果他们让我感兴趣，就像你们说的。我会买东西吗？不。但我喜欢调查它，这就是为什么我不介意谷歌跟踪我搜索的内容，我发的电子邮件等等，来产生广告，因为他们产生有用的，有趣的广告，这就是我如何了解 99designs.comT2 的；我欣赏有用的广告，哪怕只是短暂的休息时间。”这是对我们关于广告和跟踪的谈话的一点补充，我不太记得我们是在什么背景下谈论这个问题的。

Patrick: 谁在乎上下文，他用的是 SitePoint 产品(笑声)。

凯文:没错。

帕特里克:这真的没关系！扎克，谢谢你的邮件和友好的话语。这就像当路易斯收到一封电子邮件时，他在办公室里声嘶力竭地大喊，“我们收到了一封电子邮件！播客收到一封邮件！”这就像电话簿来的时候史蒂夫·马丁在蠢蛋，这是一种旧的参考，我不知道谁会收到，但是的，我们喜欢收到关于这个节目的电子邮件，所以一定要给我们写信，如果你有一些评论或你想添加或反馈的东西，我们一定会在节目中阅读。是的，所以如果你想给我们提供你的意见，请发邮件到 podcast@sitepoint.com。

路易斯:好了，今天的节目就到这里，让我们围着桌子踢吧。

凯文:我是凯文·迪斯，大家可以在 kevindees.cc 和 Twitter 上找到我，我的名字叫[@ kevindes](http://twitter.com/kevindees)。

我是 iFroggy 网络的帕特里克·奥基夫，我在推特上叫 [@iFroggy](http://twitter.com/iFroggy) ，i-f-r-o-g-gy。

好的，你可以在推特上关注 SitePoint[@ sitepointdotcom](http://twitter.com/sitepointdotcom)，那是 sitepointdotcom 你可以像 Patrick 说的那样在 podcast@sitepoint.com 给我们发电子邮件，你也可以在 sitepoint.com/podcast[的网站上找到我们，在那里可以找到这个节目，留下评论，订阅这个节目，所有这些；我们也在 iTunes 上。我是路易斯·西蒙诺。可以找我](https://www.sitepoint.com/podcast/) [@rssaddict](http://twitter.com/rssaddict) 。感谢收听，下周我们有一个凯文主持的采访，所以我会在两周后回来主持下一个专题节目。感谢收听，再见。

迈克·梅拉的主题音乐。

感谢收听！请随时让我们知道我们做得怎么样，或者使用下面的评论栏继续讨论。

## 分享这篇文章