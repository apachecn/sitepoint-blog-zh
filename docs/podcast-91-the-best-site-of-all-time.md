# SitePoint 播客#91:有史以来最好的网站

> 原文：<https://www.sitepoint.com/podcast-91-the-best-site-of-all-time/>

SitePoint 播客第 91 集现已推出！本周你的主持人是帕特里克·奥基夫([@伊罗格](http://twitter.com/iFroggy))、斯蒂芬·塞格拉夫斯([@塞格拉夫](http://twitter.com/ssegraves))、布拉德·威廉姆斯([@威廉斯巴](http://twitter.com/williamsba))和凯文·扬克([@感知](http://twitter.com/sentience))。

## 下载这一集

你也可以下载这一集作为一个独立的 MP3 文件。链接如下:

*   SitePoint 播客#91:有史以来最好的网站

## 剧集摘要

以下是本期节目涉及的话题:

1.  谷歌 Chrome 操作系统、商店和浏览器公告
2.  域名扣押和分布式 DNS
3.  24 种方式，PHP 降临节，以及 SitePoint 圣诞节倒计时
4.  詹姆斯·克拉克:XML 在网络上的未来
5.  准备启动 Launchlist

在[http://delicious.com/sitepointpodcast/91](http://delicious.com/sitepointpodcast/91)浏览展览中引用的链接的完整列表。

## 主聚光灯

*   Stephan: [Lettering.js](http://letteringjs.com)
*   帕特里克:[周六夜现场:暴雪人](http://www.hulu.com/watch/198279/saturday-night-live-blizzard-man)，[回家音乐视频](http://www.youtube.com/watch?v=k-ImCpNqbJw)
*   凯文:[保镖本](http://benthebodyguard.com/)
*   布拉德:[介绍阿帕奇波](http://googlewavedev.blogspot.com/2010/12/introducing-apache-wave.html)

## 显示抄本

凯文:2010 年 12 月 10 日。Chrome 应用商店首次亮相；XML 在网络上的未来；“一天一件事”的狂热正在蔓延。我是凯文·杨克，这里是 SitePoint 播客#91:有史以来最好的网站！

又是一个星期五，这意味着又是一个 SitePoint 播客，这里的英雄通常是:斯蒂芬，布拉德，帕特里克，你好吗？

布拉德:你好。

斯蒂芬:做得很好。

帕特里克:当我们的环聚集在一起时，它们就会激活。播客的力量！

凯文:(笑)像往常一样，我们在我的时间周三录制这段视频，其他人则在周二，这意味着我们正在看着谷歌的大型新闻发布会尘埃落定。所以我们只是部分了解他们宣布的内容。布拉德，有一些铬的东西正在进行，对不对？

布莱德:你想谈谈商店吗？

凯文:有这么多的东西，有 Chrome 操作系统，Chrome 网络应用商店，还有什么，我错过了什么？

布莱德:这是 V8 引擎的新曲轴。

Kevin: 曲轴，哦，这就是 Chrome 的 JavaScript 性能改进？

**Brad:** 是的，很明显，就像你说的，信息刚刚出来，很新鲜，但他们已经完全——听起来他们已经重做了 JavaScript 引擎，或者至少推出了一个新的迭代，应该能真正提高性能，如果你能相信的话，从 Chrome 的 JavaScript 方面。他们发布了一些统计数据或图表来显示新旧之间的差异，基于此，它看起来显然要快得多。老实说，我不知道他们如何能让它更快，但他们肯定在努力。

凯文:如今，没有一个完全重写的 JavaScript 引擎的网络浏览器版本是完整的。几分钟前，我在 Twitter 上看到有人指出 CSS 过渡，CSS 的这一功能是实验性的，它只受 WebKit 浏览器的支持，如 Chrome 和 Safari，以及各种基于 WebKit 的手机浏览器，可以让你创建动画和类似内容的 CSS 代码，有人说目前使用这些是没有意义的，因为唯一支持它们的浏览器是 WebKit， WebKit 有一个最快的 JavaScript 引擎，所以你也可以用普通的 JavaScript 代码来做，使用像 jQuery 这样的动画框架。 我认为这是一个有趣的观点，如果他们要继续提高 JavaScript 的性能，也许你会想用 JavaScript 来制作动画，而不是像 CSS 这样可能看不到同样性能提高的东西。

Patrick: 欢迎来到 SitePoint 播客，我们有一个很棒的新网络商店，但我们喜欢的是 JavaScript 引擎，所以如果你喜欢，你来对地方了。

凯文:(笑)嗯，网上商店是我们不久前谈论过的东西，你可以出售网络应用程序或访问网络应用程序的想法，我真的不是很清楚。我现在正看着它。这是位于 chrome.google.com/webstore[T3 的 Chrome 网络商店。](https://chrome.google.com/webstore "Chrome Web Store - Apps, Extensions and Themes")

帕特里克:对。看起来这里有很多免费的应用程序，但也有一些付费的。我看到很多免费游戏，我不知道，我猜当我看主页时，他们有亚马逊应用程序，所以你的网络浏览器有亚马逊应用程序，你可以浏览 Amazon.com。我不知道，我想也许对一些人来说这是一个更好的经历，但我想这很难看出它的诱惑力。

凯文:据我所知，它们有点像类固醇上的书签。因此，你不需要将 Amazon.com 深入到你的书签菜单中，而是将亚马逊作为一个网络应用程序安装，你可以在你的 Chrome 浏览器上看到这个带有亚马逊图标的标签，你可以点击它，这就像在你的浏览器中启动亚马逊一样。这一切都回到了基本上它是一个书签，带有一些额外的集成功能，使亚马逊感觉更像是一个在你的浏览器中运行的应用程序，而不是一个在标签中运行的网站，但这一切都回到了同一件事。

Patrick: 所以这可能是给那些真正热爱他们的 iPhone 应用程序的人的。在 iPhone 上浏览亚马逊现在你可以在你的浏览器上做同样的事情。实际上，我打开了付费最高的类别，只是因为我对购买的东西很好奇，那里有 12 个应用程序，所以我不知道这是不是因为只有 12 个付费应用程序，这可能有意义——这是全新的。我不知道他们是否有任何顺序，第一个列出的是所谓的幼儿点唱机，“12 首经典歌曲的孩子一个鼠标点击，汽车上的轮子，ABC，划船和更多的 1.99 美元。”这里最贵的似乎是一个每年 4.99 美元的应用程序，它叫做 Wordico，“在这个令人上瘾的纵横字谜游戏中与你的朋友斗智斗勇。”

凯文:我闻到了拼字游戏克隆人的味道…

帕特里克:是的，当你点击产品页面时，它看起来就像拼字游戏，就像拼字游戏中的字母一样。

斯蒂芬:所以你有了所有这些很酷的技术，而我们选择了拼字游戏。一切都指向拼字游戏。脸书？拼字游戏。iPhone？拼字游戏。

Kevin: Stephan 另一个公告是关于 Chrome 操作系统和这些新的网络浏览器上网本的。我看到你在推特上写了这个，你能告诉我们些什么吗？

Stephan: 我正在阅读它，它看起来非常专注于你们刚刚谈论的内容，商店和应用程序，看起来整个操作系统将围绕这些应用程序和 Chrome 浏览器构建。我的意思是，我认为这很酷，对于没有太多计算机经验的人来说，这听起来是一个很好的概念——奶奶、爷爷、年幼的孩子——这听起来是一个很好的想法，因为它相当简单。但对于我们这些在路上工作的人来说，我不认为这是一款可行的笔记本。也许我错过了什么。虽然它看起来很整洁，但是操作系统看起来很快，就像浏览器一样。

凯文:所以你是说线下不行。

Stephan: 看起来不是这样的；他们在介绍中高度赞扬了云。

凯文:我怀疑一定有一些基本的离线功能，因为所有这些 HTML5 离线存储 API 和类似的东西，我怀疑如果你在你的 Chrome OS 上网本上安装了幼儿点唱机，它可能会离线运行。但是，是的，我想这取决于应用程序是否提供离线功能，他们不会默认离线；当然，我无法想象离线浏览亚马逊。

斯蒂芬:是的，我想就像谷歌应用程序、谷歌文档之类的谷歌文字应用程序，它们可以离线访问，谷歌邮件，所以我想这些可能会离线工作，但是……其他东西可能会。但是看起来很整洁！

凯文:我看到一些人抱怨 Chrome 网上商店，这个应用程序库，你知道，你去那个网站，它首先告诉你，如果你没有运行 Chrome，你需要谷歌 Chrome 来安装应用程序、扩展和主题。第一印象是这是谷歌 Chrome 独有的技术；谷歌试图在这里抢占地盘，并说我们正在重塑网络，我们正在应用程序方面重新定义它，如果你想玩，你需要运行我们的浏览器。你知道，几个月前，当我们第一次谈论这些东西时，他们发布了这些应用程序的标准，并邀请其他浏览器来实现它。因此，如果这项技术成功，人们会认为火狐将支持同样的应用技术，然后谷歌将能够稍微放松一下。我认为它被称为 Chrome 网上商店这个事实有点冒险。也许我们会看到 Mozilla 网络商店，它使用相同的标准，Chrome 将能够从 Mozilla 网络商店安装应用程序，Firefox 将能够从 Chrome 网络商店安装应用程序。在我看来，这似乎是他们想要前进的方向，但目前还不太清楚。

Patrick: 是的，现在还不清楚为什么 Mozilla 和 Firefox 不加入他们已经很强大的插件社区，如果你真的很关心应用程序，你可以增加一个新的部分或者改变措辞，把它们叫做应用程序。所以他们已经控制了那个市场，至少就我而言，这是我转向 Firefox 的原因之一，因为它是一个大的插件社区。

凯文:你们会不会觉得——实际上我想说的是 Chrome 网上商店主页上的广告，但这些不是广告，它们是广告，它们是特色应用。所以当我进入 Chrome 网上商店的主页时，Wiki invest 或“Wiki 'nvest”是右上方的广告，我认为这就像是一个网站的广告，但它实际上是 Chrome 网上商店中一个免费应用程序的广告。

**Brad:** 是的，我之前也在 Twitter 上看到有人指出，这看起来非常熟悉，几乎类似于 iTunes，iTunes Store，他们有类似的广告，看起来像广告，但它只是指向一个刚刚推出的应用程序或专辑。

凯文:这肯定是故意的，对，我的意思是，如果你现在谈论应用商店，你一定是受到了苹果的启发。只要向下滚动到顶部扩展和顶部主题，除了是一个应用商店，继续是谷歌 Chrome 的扩展和主题的来源。因此，他们将努力把这个特定的应用商店从 Chrome 浏览器中分离出来。我猜他们只是以身作则，希望——也许他们不想成为唯一的网上商店。

**Stephan:** 笔记本有个有趣的东西，没有大写锁定键。

凯文:我听说过这件事！他们用什么替换了大写锁定键？

Stephan: 我不知道，没有显示出来，我还没能找到键盘实际样子的好图片。

凯文:我猜这是一个后退按钮，因为我听到有人说他们开玩笑地转述了谷歌的说法:“因为大写锁定键对普通用户来说很困惑，我们已经删除了它，取而代之的是一个键，如果你不小心点击它，它会带你到一个完全不同的网页。”(笑声)

帕特里克:你会看到人们不再在论坛上发布“不要封顶，不要大喊大叫”的日子已经一去不复返了

凯文:但是，是的，所以我认为这是一个后退按钮或类似的东西。

斯蒂芬:还有一个内置的越狱模式。

凯文:哦！所以，现在的突发新闻是，我们很想听听你对它的看法，因为显然我们自己仍在理解它，但这里有很多来自 Chrome 的声明，如果你运行一个 web 应用程序，将其打包成一个免费应用程序放在 Chrome 网络商店可能不是最愚蠢的想法。我想大多数人可以利用他们所能得到的所有宣传，在未来的几天和几周内对这件事的关注可能不是不受欢迎的。我看到一些人在谈论为应用程序商店建立自动提交者，就像我感觉这个应用程序商店是开放的，如果你提交免费的东西，它会马上列出来，这可能会对垃圾邮件开放。你可以为网站上的每个页面自动生成一个 web 应用程序，并将其列出来。我认为谷歌会在问题出现时处理这些问题。但是，是的，目前在 Chrome 世界里有很多值得关注的东西。

但是回到我们定期安排的故事列表，第一个是布拉德提交的。布莱德:有人呼吁改进域名系统？

布拉德:是的，所以围绕维基解密有很多争议。我相信我们的大多数听众都以这样或那样的方式听过它。

凯文:显然，这在澳大利亚是个大新闻。

布莱德:是的。这是一个相当有趣的故事，对我来说也是一个更有趣的话题，它也与我们在海盗湾看到的其他一些行动和一些其他领域的查封有关，互联网上有一个新的运动正在发展， 他们基本上是说，现在是时候认真考虑并提出一种分散 DNS 系统的方法了，目前的工作方式并不尽如人意，将过多的控制权交给了一家公司或一个政府。 所以有这样一场运动，这个团体基本上是在试图想出如何实现这个目标。这显然无论如何都不容易，它的技术细节肯定超出了我的理解范围，但我认为只是遵循故事中的文章肯定是一个有趣的话题，也是可能发生的事情。

Patrick: 所以这首先是因为最近发生的[冰域扣押事件](http://news.cnet.com/8301-1023_3-20023918-93.html)，对此有很多讨论，让我印象深刻的是，我实际上发现立法机构和这种做法本身很有趣。我们在[版权 2.0 秀](http://plagiarismtoday.libsyn.com/)上谈论了很多，所以我已经多次详细地谈论了这个 ICE 的故事，我们都同意要有一个解决盗版的办法，如果你相信可以有一个解决办法，我知道很多人认为我们应该放弃我们的手，它只是不值得斗争，但是我相信希望有一天可以有一些解决办法， 今天，我和乔纳森·贝利对剽窃的一个共识是，它需要来自一些人——来自政府或类似的东西，因为它不会是一个 ISP，因为当一个 ISP 这样做时，它会处于竞争劣势，其他 ISP 会从中获利。 所以，如果你认为有一个解决方案，那么它确实涉及到一定程度的执法，对我来说，域名是一种财产，我们已经扣押了很长时间的财产的伪造者，他们实际上是主要受到 ICE 扣押的打击。有几个嘻哈网站确实是有问题的，我认为总共有一百多个域名，但大多数都是出售假冒商品的伪造者，所以对这个行业的查封如果你想称之为一个行业的话已经持续了很长时间，我认为域名作为一种财产只是它的延伸。所以对我来说，很多事情真的被戏剧化了，Techdirt 是一个真的会加入进来的网站，说实话。

凯文:是的，Techdirt 有[这篇报道，报道了网络分散式域名系统](http://www.techdirt.com/articles/20101129/01445312034/with-domain-name-seizures-increasing-its-time-decentralized-dns-system.shtml)的需求。可以预见的是，打电话的人是与一个被关闭的网站有关的人之一，所以来自点对点文件共享社区的人说，看，我认为是我们开始建立这个网站的时候了，如果你想帮忙，请联系我。所以现在还为时尚早，这通电话是否会带来什么还有待观察，但是如果你想看看到底是怎么回事，你可以去 torrent-finder.com 的这样的网站，这是被查封的网站之一，不要担心，去那里是安全的。如果你去 torrent-finder.com，你会看到一个看起来相当吓人的页面，上面有司法部、国家知识产权协调中心和国土安全部的印章，一个特工徽章；显然这个网站是国土安全部调查的特别探员。但这是一个大图片，我在图片上的信息中发现了至少一个错别字，但它基本上是在说，是的，我们，美国政府已经查封了这个域名。有趣的是，就在几周前，我们还在讨论在利比亚顶级域名中托管像 bit.ly 这样的网站是多么不安全。ly 显然属于利比亚这个国家，利比亚将自己的法律和价值观应用于托管的任何网站，或在顶级域名下购买的任何域名。嗯，我们现在看到的是，美国不是任何人用他们的网站做任何事情的避风港。同样，标准显然是不同的，但如果你违反了美国法律，是的，你也可以与这些东西发生冲突。当然，关于这种做法是否正确还有很多争论。就在这一切发生之前，人们还在为这项即将通过的立法争论不休——伙计们，你们得帮我一把。

**帕特里克:** COICA。

凯文:科伊卡，它正在国会通过，还是什么？

Patrick: 反正它正在通过立法机构，我不是——人们称它为互联网审查，但它是《打击网络侵权和假冒法案》。它正在参议院通过，只是为了把它全部拿出来；我发现了，所以我们没事了。

凯文:好的。好吧。所以他们说，如果这项法律获得批准，如果它获得通过，那么政府将能够在任何时候没收域名，我们需要与之斗争。这次扣押向我们展示的是这艘船已经在那上面航行了。我们不需要通过那项法律就能看到这种情况在美国发生。

**Stephan:** 我认为其中一个问题是。我想很多人会把它与美国联系起来，而事实是它们是全球性的域名。

凯文:嗯，对，就是这种观点。com 是事实上的顶级域名；如果你不想把自己和任何特定的国家联系在一起，你可以获得一个. com 域名，他们有最自由的政策，谁可以买什么，他们往往是最便宜的，结果它变成了这个事实上的标准，但我们忘记了它也可能是. co.us，因为。com 是一个美国域名，你最终要服从美国政府。

斯蒂芬:对，这是一个灰色地带。如果你是一家购买了. com 网站的瑞典公司，你在瑞典托管该公司的网站，但你正在做违反美国法律的事情，如果美国政府不喜欢你的所作所为，那么这个网站、域名是不是美国政府的财产？这是一个真正的灰色地带，我希望看到国际刑警组织或某个国际组织真正站出来说点什么，因为我认为这需要在国家之间更公开地讨论，而不仅仅是在美国。

凯文:关于这些措施的有效性，有一个有趣的讨论。显然，在这种情况下，他们从人们那里拿走了一堆名字，但这些真正想保持在线的网站在几个小时内就以不同的名字重新上线了。所以你可以用这些网站的名字玩打地鼠游戏，你知道这可能对已经达到主流意识的网站有一定程度的效果，比如说，海盗湾，或类似的东西，但对于较小的网站或当他们四处移动并移动到不同的名字时有一种沟通方式的网站来说，这不是那么有效。一旦他们的名字不在美国司法管辖范围内，他们将很难追上去。然后还有托管问题，我们看到维基解密本周被亚马逊网站服务托管。我相信，亚马逊引用了与侵犯版权有关的违反他们使用条款的行为，维基解密不得不迅速在瑞典或者斯堪的纳维亚的某个地方为自己找到替代的虚拟主机，如果我没弄错的话。所以你可以拿走他们的名字，他们得到其他名字，你可以拿走他们的主机，他们得到其他主机，这是值得的还是浪费纳税人的时间和金钱？

斯蒂芬:嗯，这和毒品战争的情况很相似。我是说真的是。争论的双方都有人说我们必须进行一场反毒品的战争，或者我们不这样做。这就像是在玩打地鼠游戏，所以我不知道，我认为政府会这样做，因为他们不得不这样做；他们必须保护大众的利益，我认为他们会继续这样做。我不认为我们会看到，因为人们大声反对，我不认为我们会看到美国政府没收域名的情况会减少。

Patrick: 我认为这也是一种威慑，我认为人们会重新出现，我处理禁止人们进入我的论坛的同样问题，对，所以有些人会告诉你不要禁止他们，他们会回来。好吧，再说一次，我必须有某种方式在这里设定某种标准，并有一个终点，当他们最终说我要以我想要的方式对待这个社区时，我不在乎工作人员说什么，我不在乎准则说什么，我们必须继续前进。现在，他们会回来吗？当然，有时会，但当他们注意到他们是谁时，我们又会禁止他们。因此，我认为这是一种威慑，因为你所做的，至少在论坛的情况下，以及在一些已经建立的域名的情况下，是把它们放回原点或更接近原点。因此，当我禁止某人进入我的论坛时，他们可能会从一千篇帖子变成一篇，当你开始一个新的域名或当你没收那个域名时，你确实会降低他们在搜索中的可信度。现在，如果你是海盗湾或维基解密，他们可以迅速传播消息，但你仍然没有尝试，我的意思是我认为你仍然必须尝试。我不知道查封域名本身就是答案，但我绝对认为这是有趣的第一步，我认为你必须尝试一些东西，然后改善你所做的一切。因此，也许这不是长期的答案，但也许这是更好地理解任何过程的第一步，我想这个过程是需要的，我认为就像我说的大多数网站可能是合理的，种子网站，就像其中一些网站一样，我很难捍卫。几个嘻哈网站，如果你不知道这是我感兴趣的领域，我自己也写了一些，我经营一个嘻哈音乐网站，那里有几个网站是有问题的，可能在最坏的情况下有点灰色，但仍然与唱片公司或许多唱片公司合作，并且是受欢迎的，知名的网站。其中一个网站叫做 OnSMASH，他们有一些人，艺术家，在推特上代表他们说他们应该“解放 OnSMASH”，等等，所以这是一个显然至少部分合法的网站，所以他们应该得到和其他人一样的待遇吗？是否应该有一种不同的流程，让更多的人有机会在扣押实际发生之前对其进行评论？这些都是很好的问题，我认为这将引发一场有趣的讨论，这场讨论将由此行动拉开序幕。

Brad: 他们关闭网站的方式很可怕，因为很多网站说，没有通知，没有投诉，没有要求关闭，它们就这样消失了。所以完全没有警告说这将会发生，他们只是走了。

斯蒂芬:这有点像他们没收财产。我是说这是一回事。他们只是出现在你家然后拿走。

凯文:他们引用了法庭命令，但是，是的，他们这么做的方式很有趣。这几乎就像一次缉毒行动。你不会在你的邮箱里放一封信，说我们发现你在贩毒，请停止，否则我们将被迫搜查你——他们只是搜查你。他们用同样的方式对待这些网站。看看 COICA 法的措辞，那个还没有通过的，因此实际上我们现在看到的看起来也一样糟糕，但至少如果 COICA 法通过了，他们说美国政府将有权查封任何网站，“除了提供对版权作品的未授权访问，没有明显的商业意义。”这真的很有趣。例如，我建议你试用的 torrent finder one 网站，在被查封之前，该网站实际上只是一个用于查找 torrent 的搜索引擎，所以从技术上来说，它们与非法活动相距一步，它们只是非法活动的索引，听他们辩论他们的案件时，他们说这对打击盗版和实施盗版一样有用，我们做错了什么？因此，这项法律要求做出判断，说你知道吗，你与盗版有关，我认为“商业”这个词是真正困扰我的。你“除了参与盗版之外，没有任何*商业*目的”所以我们要把你带走，如果你有非商业目的呢？我的意思是，你看看像电子前沿基金会这样的网站，他们是活跃在这类领域的非营利行动主义团体，他们有商业目的吗？不。这个播客不是要在法律的措辞上挑毛病，但我不得不说，它让我热血沸腾。

斯蒂芬:好吧，我要告诉你的是，作为总结，我仍然很高兴我没有生活在长城防火墙之后。我的意思是真的，因为这是我们可以拥有的，对，他们可以切断这些领域的服务，只是阻止他们甚至可以进入，从人们甚至可以浏览它们。所以我想两害相权取其轻，真的。

**帕特里克:**对，我甚至不知道这是否是一种罪恶，因为我认为比较这两者有点奇怪，因为，我也不知道，很多人对互联网有不同的看法，但是，这就是我们在社会上对待罪犯的方式，如果你认为他们是罪犯，你就会从社会上审查他们，对吗……

不，我同意。我同意。

帕特里克:…如果你想用审查这个词，这就是我们处理这个问题的方式，所以如果我们说法律可以适用于互联网，那么显然人们会受到审查。现在，再一次，我不一定同意已经做的一切，也许还有工作要做，我们可以做得更好，但人们仍然不得不开始或继续关闭。我知道有很多网站，他们的整个网站都是盗版的——从我的书来看，我敢肯定 SitePoint 看到这种情况一次又一次地发生，T2 超过 T3，T4 超过 T5，无数次地发生，作为一个内容创作者，这些人中有很多人，是的，DNS 对小角色来说很棒，我们可以发出通知，让很多人撤销它， 但是还有这些网站或这些辛迪加，人们的组织，这是很难追求的，我们不可能真正做到这一点。 我不得不说，对于这些网站明天消失，对于那些人被关进监狱，我一点也不同情。也许我只是冷酷无情，但这只是我的看法。

**Kevin:** 那么，这个为分散式 DNS 系统开创先河的提案怎么样？在我看来，如果这种情况发生，它将采取的形式是，这些运营这些网站的人会说，好吧，与其指望我会注册一个不会被某个地方的某个政府取消的名称，我会在这个替代 DNS 系统上注册自己，任何想访问这种网站的人都必须调整他们的互联网设置，而不是通过他们的 ISP，他们通过这个替代 DNS 系统来查找这些网站的地址。我们已经看到了替代的 DNS，例如，[谷歌提供了一种 DNS 服务](http://code.google.com/speed/public-dns/ "Google Public DNS")，它比 ISP 使用的标准服务要快一点，更有效率一点，所以有兴趣访问这类内容的人很可能会说，“嘟嘟嘟，好吧，我不再受美国政府的约束，什么是允许的，什么是不允许的互联网名称，”然后我想下一步就是

帕特里克:我有两个想法。第一，它永远无法抓住海盗湾的彼得·桑德，因为那里有太多的包袱，有太多的包袱，只要他是其中的一部分，它就永远是边缘的。

凯文:嗯，美国应该抓住他的名字，然后他可以想出一个新名字(笑)。

帕特里克:嗯，我不知道。他用什么域名——我想我们会看到的。

Kevin: 我说的不是领域；我说的是他的名字，如果他的名字对这些项目是毒药的话。

Patrick: 我的意思是，我们不要夸大其词，有 100 多个域名大部分是仿冒者，但我只是说彼得·桑德，我只是觉得他是一个边缘玩家，这仍然是一个边缘的东西。第二，我不知道会有多少——但是，在什么时候它也会受到攻击？如果你要说，我们将推出一个基本上无法无天的 DNS 服务，对，所以我不知道这将有很长的保质期。我认为，他们可能必须建立某种程度的合法性和责任，以接受任何牵引，任何人以外的编辑他们的主机文件或任何文件，这将允许他们使用您的 DNS 服务。那永远只是很小一部分人。

这不像是 Windows 会做这种改变或什么的，对吧，或者 IE 或 Firefox 会把它设为默认设置，他们不会的。原因很明显。

凯文:是的，这将是那些想让盗版成为你必须精通技术才能做到的事情的人的又一次胜利。我认为这是一个有效的措施。

现在是 12 月，这意味着有太多的网站每天都在发布新的东西。这些降临节日历类型的网站，当然我们不会被排除在 SitePoint 之外，在 sale.sitepoint.com 有 [SitePoint 圣诞节倒计时，每月每天都有 SitePoint 产品的交易，如果你还没有，请查看一下。但是随着那个无耻的插件的出现，这个月我密切关注了几个其他的网站。24ways.org](https://sale.sitepoint.com/)[和往常一样，每天都有一篇来自这个行业全明星之一的网页设计和开发文章。在我们录制的时候，它们已经排到了第八位，到目前为止，我已经阅读了每一篇文章，这里不要错过的东西，例如](http://24ways.org/)[克里斯蒂安·海尔曼的一篇关于 HTML5 本地存储的文章](http://24ways.org/2010/html5-local-storage "24 ways: Wrapping Things Nicely with HTML5 Local Storage")，以及你如何通过在用户的硬盘上缓存你网站的部分内容来加速你的网络应用。我喜欢 Drew Neil 的[Golden spiral one](http://24ways.org/2010/golden-spirals "24 ways: Golden Spirals")，这是如何使用简单的 CSS 百分比来创建网页布局，通过使用黄金比例来布局，有一种即时的和谐感，里面有很多非常棒的东西。伙计们，你们知道我们应该把我们的听众送到其他什么网站吗？

Brad: 事实上我知道一个(惊喜，惊喜)WordPress focused。修卡。这是 wpengineer.com 的[，它实际上是一个非常棒的网站，因为它更多的是技术方面，更多的是以开发者为中心的文章，他们去年做了一篇，同样的事情，24，25 天降临节，每天都有新的帖子，他们今年再次这样做，他们有一些非常棒的主题，所以一定要看看那个。](http://wpengineer.com/)

Kevin: “自定义 WordPress 登录页面”，“在 WordPress 中向附件添加自定义字段的插件”，“启动 WordPress Framework Extreme 1。”哦，WordPress 调试栏，太酷了。

凯文，你知道是谁设计了网站圣诞节倒计时吗？

凯文:应该是亚历克斯·沃克。

帕特里克:好吧，我正要猜呢。我不得不说这太棒了。

凯文:是不是很华丽？

帕特里克:我在这里没有任何一天要补充的，只是想再喝一点公司的可乐；我个人认为这是一个很棒的网站。你知道，我也喜欢它的一点点细节，因为我喜欢这些东西，图像实际上是完整的框，就像如果你单击并拖动它，你会得到图像的阴影，它实际上是页面上完整的框，是布局的一部分。所以我喜欢整件事，它真的很漂亮。

凯文:是的，没错，所以如果一个包裹被放在它上面的另一个包裹遮住了一部分，你可以把它拖出来，实际上就能看到那个包裹的顶部。是啊，很棒。亚历克斯非常注意细节，他喜欢当他可以在一些有点实验性的事情上放松，有点临时，不需要持续到这个月底，所以他可以真正打破我们可能有的任何惯例，是的，这很有趣。美丽，美丽的网站。当我们在 11 月的最后几天进行预览时，我真的很喜欢它。整堆礼物就像背光一样，所以我们从背后发出明亮的光，这是我最喜欢的页面外观，但显然你现在看不到了。是啊，看看吧，看着礼物一天天地被撕开真有趣。

斯蒂芬:你提到过 [PHP 问世](http://phpadvent.org/)吗？

凯文:不，那是我单子上的另一个。是的，我认为这可能是他们这样做的第二年或第三年，但是在 phpadvent.org 12 月的每一天都有一篇 PHP 相关的文章，是的，如果你是 PHP 开发人员，这是必读的材料。

我喜欢这些文章的原因是，即使你今年真的很忙，你知道你感觉你的头卡在了最后期限的沙子里，并且你没有能够在这一年里跟上 web 开发的艺术和科学的进展，你也可以在 12 月份关注这些网站中的一个，希望当事情对你来说慢了一点，或者甚至在你的假期中赶上。但是，如果你觉得今年有一年的新鲜事物和最佳实践，你可以通过这些东西一下子得到所有的东西，这就是为什么我真的喜欢它们。

詹姆斯·克拉克的一篇文章正在制造新闻。詹姆斯·克拉克，如果你不熟悉这个名字，他是 W3C 的 XHTML 工作组的成员，事实证明，他是命运多舛的 XHTML 工作组的成员。他的文章 XML vs. the Web 总结了他对 XML 如何未能抓住 Web 开发人员的心的想法，为什么我们坚持使用 HTML5，特别是将我们的 web APIs 转换为 JSON 之类的格式，而不是像最初想象的那样使用 SOAP for web services 之类的标准通过网络来回发送 XML。很多人都在讨论这个问题，但我认为提出这个问题很有意思。伙计们，布拉德，这可能是给你们的。你写过很多与 web 服务或 web APIs 交互的东西吗？

Brad: 是的，你说得对，这是一篇非常有趣的文章，他的一些观点实际上正合我的胃口，因为当 Twitter 刚出来的时候，我在玩 API，我实际上写了一些脚本，并把它们发布在我的博客上。信不信由你，那时候我做的是经典的 ASP，但我用的时候都是 XML，这就是为什么我对它很熟悉，因为我知道它。就像他说的那样，每个人都在走 JSON 这条路，这几乎是在强迫你，现在到了你必须知道它的时候了，你必须朝那个方向走，否则你就会被甩在后面。直到我读了这篇文章，我才知道 Twitter 关闭了他们 API 的 XML，所以我认为这也很有趣。

凯文:嗯，是的，他在这篇文章中似乎想说的是，虽然 Twitter 和 FourSquare 这两个服务已经去掉了它们 API 的 XML 版本。他说，虽然 JSON 在这些你需要的应用中明显胜出，比如 web APIs，你需要来回发送一点数据结构，你知道，你想要一个你最近的 tweet 列表，它发送回你最近的 tweet 列表，你想尽可能高效地将消息转换成你可以在你的程序中使用的东西，比如一个变量，可能是一个数组或一个对象。JSON 是一种比 XML 更自然的格式，在 XML 中，你知道，它是所有的标签，标签可能以任何顺序出现，你需要正式列出你正在使用的标签。JSON 只是被设计得更简单，与使用这些 API 的编程语言联系更紧密，所以很明显 JSON 在这些赌注上赢了。但他似乎在说，XML 最初并不是为此目的而设计的，XML 的优势在于他们所谓的非结构化数据。因此，当您想要接收一个文档或甚至一段数据，但该数据的结构不一定是可预测的，它可能包含这些，它可能包含那个，它可能是一个带有粗体文本的整个文档，或者它可能是一个错误消息。因此，来回发送的数据内容越不可预测，JSON 就越不合适，XML 就越开始发挥它的优势。所以他在这里说的是，如果 JSON 真的想接管 XML 今天在 Web 上所做的一切，JSON 将不得不变得和 XML 一样复杂，然后遭受 XML 所具有的所有相同的问题，即它的复杂性和学习成为 XML 专家所需要知道的一切的困难。所以我想这里的信息是两者都有空间。

Stephan: 是的，XML 的复杂性实际上让它变得简单了。

凯文:(笑)这是怎么回事？我不明白。

帕特里克:请告诉我这个，弗洛伊德。

Stephan: (笑)我想说的是，它太复杂了，当你真的要用 XML 实现某个东西时，如果你知道自己在做什么，它实际上是很简单的。

凯文:是的，我知道你的意思。你可能要花一周的时间来弄清楚你需要用什么库和什么 XML 特性来满足你的特定需求，但是一旦你弄清楚了，你就会说，“哦，所以我只要包含这个东西就可以了。”

斯蒂芬:对，没错。所以我觉得他对我一清二楚。也许我误解了他，但我认为他是对的。

Kevin: 但是似乎没有争议的是，对于像 Twitter API 和 FourSquare API 这样的简单 web APIs，JSON 确实是更好的选择，我想 XML 应该靠边站了。

让我们来看看本周的 SitePoint 民意调查。sitepoint.com 主页上的 SitePoint 投票是:“你最喜欢的浏览器调试器是哪个？”这是我们之前在播客中讨论过的，但是四个选项是 Firebug、WebKit Inspector、Opera 蜻蜓和 IE 开发者工具。具体来说，我们谈论的是内置于 Internet Explorer 9 中的工具栏，而不是你必须为 IE 7 或 IE 8 下载的开发者工具栏。IE 9 中的这个已经变得更好了，但是我怀疑还有很多开发者，包括我自己，没有仔细研究过它。你们觉得怎么样？Brad，在这四个工具中，你最喜欢哪一个？

布拉德:我肯定要说 Firebug，我的意思是督察绝对是第二名，在过去的一年里它取得了很大的进步。我所做的事情有 90%是因为我 100%的时间都在使用 Chrome 浏览器，我所做的事情有 90%可以通过 Inspector 来解决，但偶尔我不得不启动 Firefox 来使用 Firebug 做一些事情。说到底，我更喜欢使用 Firebug，我只是希望它能在 Chrome 上工作。

凯文:斯蒂芬？

Stephan: 我其实很喜欢 WebKit Inspector，是的，它很快。

凯文:我不得不说，我也是——我想说的是 Firebug 是我最喜欢的，但不是我用得最多的。就像你一样，我最近最常使用 WebKit Inspector，因为它是我大多数时间使用的浏览器，但如果我发现自己在做一些繁重的前端开发，并且我预计必须解决一些困难的问题，无论是 CSS 还是 JavaScript 调试，是的，我经常会启动 Firefox 来访问 Firebug。所以我会投票给 Firebug，这是我最喜欢的一个，但恐怕不是我用得最多的一个，那个是 WebKit Inspector。我想这就像关于照相机的争论；最好的相机是你口袋里的那种。

Patrick: 是的，我的意思是我也会选择 Firebug，因为老实说，我没有用过其他任何一款，我甚至没有安装 IE 9，所以我甚至没有用过其他任何一款，我用过 Firebug，而且我发现自己用得越来越多。我使用它是因为我管理着一个专注于论坛软件 phpBB 的社区，并且已经有近 10 年的时间了，当我在论坛中帮助人们时，特别是当我们谈论一种风格或某种表面或视觉变化时，他们想知道为什么这个图像会这样做，我通常会使用 Firebug 并找出它为什么这样做，这也很有帮助，所以它对我来说也是 Firebug。

来自 SitePoint 社区的官方结果是……一个大滑坡的萤火虫！因此 Firebug 获得了 84%的选票，WebKit Inspector 排名第二，好主意 Brad，9%。然后，我们有 IE 开发者工具在 4 %, Opera 蜻蜓在 3%的后面，我认为这可能有点卖短。事实是，这些都是令人惊奇的工具，如果我必须用 Opera 蜻蜓做我所有的网络开发，我一点也不会是一个不快乐的开发者。Opera 蜻蜓是一个非常强大的工具，它在远程调试方面有一些独特的功能。因此，如果你已经在手机上运行了 Opera 移动浏览器，你可以在桌面上启动 Opera 蜻蜓，连接到手机的浏览器并进行调试，这是非常令人印象深刻的。但是，是的，Firebug 是赢家，我想这并不奇怪，与这次投票相关的博客帖子是在 sitepoint.com，【Craig Buckler 的 Firebug 1.6 中的新功能，对我来说，这只是一个列表，列出了 Firebug 在竞争对手迎头赶上并变得与 Firebug 一样好的情况下取得的进步，这是我经常听到的关于这些其他工具的说法，“嘿， 它就像 Firebug 一样好，”嗯，Firebug 已经添加了一大堆新功能，如果你想了解这些，请查看 sitepoint.com 上的 Firebug 1.6 新功能博客，在展示笔记中会有一个链接。

今天播客的最后一个故事是关于一个叫做 [Launchlist](http://launchlist.net/) 的网站。这是由一些神秘的澳大利亚开发者开发的，他们自称 Collapps，就像协作应用程序一样，他们说他们是一群澳大利亚人，但他们没有说出自己的名字，所以有点神秘，但如果你去 launchlist.net 的或者如果你想查看这款应用程序的免费版本，请去 lite.launchlist.net 的。这个网站为你提供了一份清单，上面列有你需要检查或者你应该在启动一个新网站之前检查的东西。我不得不说，这本来会成为一个很好的焦点，但我真的想和你们一起看看这个列表，看看你们有什么想法。这些是必需品吗？它们是唯一的必需品吗？这个应用程序当然允许你把你自己的东西添加到这个清单中，这个应用程序的付费版本允许你实际存储你的清单，这样你就可以回来更新它们，创建模板并与你的团队成员分享你的清单，并说，好的，这个人负责检查拼写，这个人负责检查验证，等等。但是免费版本非常简洁。如果你是一名自由开发人员，实际上，当你填写完这个并点击“提交报告”时，它会通过电子邮件给你一份格式非常好的报告，然后你可以将它传递给你的客户，作为你已经在他们的项目上完成了所有工作的证据。这是一个相当长的列表，但让我们看看，“所有文本都没有拼写错误，”我不知道伙计们，你怎么看布拉德，你在网站上线前做过全面的拼写检查吗？

**Brad:** 不，除了我在 it 部门工作之外，但通常我们不对网站上的内容负责，所以我当然可以看到一些启动列表任务，它们不一定都是为开发公司服务的，我不认为，我也可以看到一些面向客户的任务。

凯文:哦，是的，我看得出来。

布拉德:这可能是一个可能的结果。

凯文:这里有一些与验证相关的有趣信息，比如“HTML 已通过验证”、“CSS 已通过验证”我想说，在现代网站上，不故意通过验证可能是一种规范，所以你从有效的东西开始，然后添加一些尖端的 CSS 功能，打破你的验证，但它们是前瞻性的变化。所以在这种情况下，我可能会说不，但是要加上一个注释，说明这些是不有效的，为什么。检查断开的链接，“在 IE 6，IE 7，IE 8，Firefox，Chrome，Safari，Opera 中显示和功能正常，”这些浏览器似乎建议你开箱测试。

Brad: 给 IE 6 打上一个大大的‘NA’，没人关心那个浏览器。

凯文:(笑)是的，在这一点上，我会用 IE 9 测试版取代 IE 6。

**Brad:** 现在，如果您将其中一项点击为“不可用”,我还没有看到最终报告，因为在报告的前端会有一条删除线；这是通过删除线延续到最终报告中，还是只是将其删除？

凯文:对，我不确定，我不确定。我认为它必须通过一个删除线来完成。它说我们看了这个东西，但是因为它是相关的而取消了它的资格。我认为这是报告中有用的内容。有趣的是，他们建议在 1024 × 768 下测试，然后在更大的分辨率下测试。因此，我们似乎已经彻底抛弃了 800 × 600 作为基本分辨率的时代，我想我绝对同意这一点，但 1024 × 768 正迅速被 1280 × 800 的宽屏分辨率所取代。

布拉德，你怎么看，你现在设计的是宽屏还是 4 × 3？

布莱德:通常 1024 × 768 是一个基数，然后再从这个基数上升。

凯文:嗯，很有趣。所以，是的，“web 统计软件包已安装并可运行”，这是一个很好的例子。所以，当我帮别人忙的时候，我经常会为我忘记安装 Google Analytics 的人建立一个网站，因为一个月后他们肯定会说，好的，谢谢你的网站，但是你能告诉我它有多少流量吗，如果我忘记安装 Google Analytics 了，我不得不说，哦，对不起，我刚刚安装了一个统计包，要再过一个月我们才能有你流量的好照片。如果你没有在第一天就发布，我想你会错过一个完整的图片，也许是你发布日的流量，这可能会很有趣。最后一个是我最喜欢的，“404 页的存在和信息。”我希望我们可以一直说我们在 SitePoint 这里得到了那个，但是我们没有。这是许多 web 开发人员的一个弱点，404 页面通常是事后才想到的，尤其是在非常活跃的网站上，在那里您可能会移动东西、添加和创建页面。还记得我们前阵子说的双彩虹 404 页面吗？似乎做一个迷人的 404 页面是这些天来*的惯例*。

帕特里克:是的，你的 404 必须可爱，否则你就不会成功。

凯文:(笑)我并不是说它属于名单上的*之首*，帕特里克！

帕特里克:在底部，对，所以。

凯文:没错。但是，是的，很高兴它在那里。

Patrick: 注意到了吗，在你击中所有东西之前，你是无法发射的？不建议发射，直到一切都是肯定的，这里没有宽容，没有同情。

凯文:没有。没有测试版发布。

**Brad:** 我认为他们应该做的一件事，也是我多次提到的一件事，就是确保你的 robots.txt 文件和所有内容对搜索引擎都是开放的，因为很多时候我们在开发时会确保所有内容都被锁定，以确保它们不会在开发 URL 上被索引，然后当我们启动时，我们会忘记这一点。然后，就像你说的那样，几周或一个月过去了，为什么我们还没有开始被索引，然后你意识到你在标题中留下了一些东西，或者你的 robots 文件正在发送搜索引擎，所以这可能是他们应该添加的一个。

Kevin: 让我感到惊讶的是，这些检查点真的需要像一篇小文章一样，即使只是几段解释这是什么，为什么它很重要，以及如何找到更多信息。因为我认为许多 web 开发人员会被这些检查点所困扰，并且会说，哦，好吧，我很确定我没有这样做过，但那是因为我不知道如何去做。所以我很想看一些指点。

帕特里克:只要链接到 sitepoint.com 的相关文章。

凯文:耶！…帕特里克，谢谢你领会言外之意。(清喉咙)

帕特里克:我一点也不含蓄。但我认为我们都有自己的个人清单，比如我有一个非常小的清单，它实际上包括 404 页，它包括分析代码，其中包括 Quantcast，我还有自动化数据库备份等内容，并确保这些内容都已设置好。我有 iFroggy Network 的东西，所以我们有网络栏和网络链接，所以我在这里也可以将新网站添加到网络栏和链接部分以及网络主页，并更新他们的相关配置文件，各种业务特定的东西也在那里播放，这就是为什么他们有自定义选项，所以这很好。

**Kevin:** 实际上，我们早在 2006 年就在 SitePoint 上写了一本书，名为[交付一流网站，101 个基本清单](https://www.sitepoint.com/books/checklists1/)。它实际上是一整本书，331 页的清单。所以有版权检查清单，有网络标准检查清单，有 SEO 检查清单，所以整本书都是，它附带了 PDF 文件，你可以打印出来并在纸上检查。但是我认为这个 Launchlist 网站特别好，它允许你在一个团队中划分责任区域，并生成一个非常漂亮的报告，你可以展示或放在你提交给客户的最终包中，这是一个很棒的网站。

说到伟大的网站，让我们开始我们的主聚光灯，斯蒂芬，你想带领我们吗？

斯蒂芬:当然，这周我得承认，我来到这个网站是因为它看起来很性感；很好看的网站，[刻字. js](http://letteringjs.com/) ，所以是 letteringjs.com。它是一个 jQuery 插件，适用于…

Patrick: 品牌问题！品牌问题警报！(笑声)对不起。

**斯蒂芬:**…激进网络拓扑的 jQuery 插件，它真的很酷。这是一个 JQuery 插件，它基本上分割一个标签并插入范围，我认为这是一个很好的想法，因为你可以做的一些例子是，你可以给它一个带有类的标题，然后你调用 jQuery 函数`.lettering`，它分割你的标题并使每个范围都有自己的类，所以你可以在不改变标记的情况下设置范围的样式。所以如果你愿意，你可以——你的客户或者你只能在浏览器上看到`<h1 class="whatever">`,这就是他们所看到的，然后这个 JavaScript 在后台自动插入 span。

凯文:是的，如果你在浏览器中禁用 JavaScript，然后加载 letteringjs.com 网站，再重新打开，你可以看到它到底在做什么，你可以看到它的区别。那一页的标题每一个字母都是不同的颜色，它有这种迷人的彩虹效果，当你把鼠标放在字母上时，它们会向下弹起，当你把鼠标放在“js”之前的时间段上时，它会下降到基线。所有这些都是用 JavaScript 完成的，而不是用所有这些跨度来填充你的标记，这样你就可以单独处理每个字母，我想这就是这个插件所做的，它为你输入这些跨度，然后你就可以对每个字母进行*最终控制*。非常性感。

Stephan: 是的，这真的很酷，因为它没有显示在标记中，所以如果你看它的标记，字体，标题，看起来就像是一个类。所以真的很整洁。对于那些想在网站上用排版做一些很酷的事情的人来说，这很有用。

凯文:帕特里克，你给我们带来了什么？

帕特里克:嗯，继续我的传统，在所有聚光灯下都不与播客联系，如果你了解我，你会知道我是迪迪的超级粉丝，他正在发行他的最新专辑，这是四年来的第一张专辑，名为“脏钱”乐队的《开往巴黎的最后一班火车》,所以如果你不是迪迪的粉丝，想象一下你在世界上最喜欢的乐队正在发行他们的新专辑，这就是我。因此，为了纪念这一点，我想分享一个快速的双聚光灯，我猜你会说。第一个是[，他周六在周六夜现场](http://www.hulu.com/watch/198279/saturday-night-live-blizzard-man "Hulu - Saturday Night Live: Blizzard Man")与安迪·萨姆伯格、罗伯特·德尼罗、杰森·苏戴奇斯、克里斯汀·韦格和基南·汤普森一起表演的一个小品，这只是我在这个节目中提供的通常的笑声，我猜你会说，所以看看这个，这是一个在录音室里的有趣的小品，萨姆伯格在里面扮演一个 Diddy 相信的艺术家，尽管不一定值得那个相信。第二个亮点是[回家的音乐视频](http://www.youtube.com/watch?v=k-ImCpNqbJw)，这是他们发布的歌曲，收录在 Skylar Gray 的专辑中，音乐视频有点让我回想起我们对版权有所控制的时候，音乐视频有预算，因为我不知道，很多音乐视频没有以前那么大的预算，所以这个视频有点让我回想起，一个非常好的拍摄，一个非常漂亮的视频。即使你不喜欢这首歌的音乐，它只是这首歌的一个极好的视觉效果，所以检查这两个链接，如果你是一个粉丝，拿起这张专辑，但无论如何享受欢乐。

凯文:我的焦点是在 benthebodyguard.com的保镖本，这是一个预览网站，我不一定推荐这个应用程序，因为我们甚至不知道这个应用程序将会是什么，这是一个 iPhone 应用程序或 iPod Touch 应用程序，他们说，将在 2011 年 1 月推出，这将与保护您手机上的敏感数据有关。但是预览网站是网页设计的杰作。你进入 benthebodyguard.com 网站，开始向下滚动，通过 CSS 和 JavaScript 的巧妙结合，他们创造了这种穿着皮革和黑色墨镜的人——他在《黑客帝国》电影中不会显得格格不入——晚上走在街上，告诉你手机上有敏感数据有多危险，以及我们将如何解决这一问题。当你向下滚动时，各种各样的事情发生了，这就像一本移动的漫画书。一个人的手机在巷子里被偷了，抢劫犯跑过了马路，这一切都与页面上的滚动位置有关，所以如果你向上滚动动画，向后滚动，如果你向下滚动动画，向前滚动，在街上行走的那个人会弹出小漫画泡泡，他正在戏弄这个即将推出的应用程序。当你最终到达底部时，他们会问你要你的电子邮件地址，如果你想在这个应用上保持更新的话。所以，如果你喜欢的话，就这么做吧，但至少要去 benthebodyguard.com 看看 JavaScript 和 CSS 效果的可能性。

帕特里克:我想表扬你，凯文，因为我检查了这个网站，它可能是有史以来最好的网站，我不是 100%确定，但它肯定是有争议的。我当然是在开玩笑，但这实在是太棒了。我要说的是，因为我第一次向下滚动时，我确实错过了强盗，所以到底部，阅读所有内容，然后回到顶部，在你的鼠标上点击鼠标中键，让它在页面上缓慢滚动。所以在页面上缓慢滚动，这样他就可以慢慢地走，你会注意到一切都是如何到位的，强盗，尤其是地铁或火车，如果你以缓慢滚动的方式移动，而不是鼠标向下移动，它实际上会像一辆真正的地铁列车一样在屏幕上缓慢移动。所以这只是一件美妙的事情，值得享受五到十分钟。

凯文:是的，完全同意。布拉德，你有什么？

布莱德:哦，天啊，我得跟进这件事！(笑声)

凯文:这是有史以来最好的网站*！*

布拉德:有史以来最好的网站…开始！

Patrick: 对不起，Brad，我会让你说完，但是 Kevin 刚刚发布了有史以来最好的网站。所以现在你可以走了。(笑声)

布莱德:谢谢你，坎耶。是的，所以我的聚光灯现在是假日季节，我心情很好，感觉很好，所以我想让我们从死水中捞出点什么，这总是很有趣的。我们在死池里放了很多东西，但是我们——有些东西出来了，所以我认为我们需要稍微展示一下。那实际上是 Google Wave，我们都认为是 DOA。早在 8 月份，当他们最初宣布 Google Wave 将不再是一个独立的产品时，我们认为它已经结束了。嗯，故事发生了一些变化，回到几个月前的 10 月，谷歌宣布他们将开放 Wave 的部分源代码，我想他们说他们将开放 20 多万行代码。不久之后，他们说他们将进一步开放并发布他们所谓的“Wave in a Box ”,这基本上是在你自己的硬件上运行 Wave 服务器和托管 Wave 所需的一切，所以你可以按照自己的意愿设置 Wave。现在它甚至更进一步， [Google Wave 已经被 Apache Software Foundation 的孵化器项目](http://googlewavedev.blogspot.com/2010/12/introducing-apache-wave.html)接受，所以 Apache 实际上将监督这一点，他们已经将其重命名并重新命名为 Apache Wave，所以看起来 Wave 实际上将作为一个开源项目存在。背后有 Apace 软件基础是巨大的，所以我真的希望看到他们在整个 Wave 协议和 Wave in Box 应用程序上取得一些很酷的进展。

Patrick: 对于我们这些使用过 Wave 的外行人来说，我会把自己算在这一类中，这是否意味着我们将会看到人们创建 Wave，并可能允许你将你的 Wave 文档转换到他们的设置中，或者这将会采取某种基于 web 的东西的形式，使用 Wave 的人可以带着他们的文档并访问它？我想对于那些只是用户的人来说，这里有什么安排呢？

凯文:我想我们将会看到，这个项目将会推出一个发行版，就像你可以下载 Apache web 服务器并使用它运行你自己的 web 服务器一样，你也可以下载 Apache Wave 服务器并使用它运行你自己的 Wave 服务器。这是 Wave 首次发布时承诺的事情，但如果你作为一家公司想在内部使用 Wave 作为内部通信工具，你只需下载服务器，就可以在防火墙后使用 Wave，在内部使用自己的登录系统等。但有趣的是，就像一个电子邮件服务器，虽然你可以运行一个私人电子邮件服务器，只用于内部电子邮件，但它符合这个开放标准，可以与其他电子邮件服务器通信，所以 Apache Wave 服务器同样可以与其他人的 Apache Wave 服务器通信，你可以在不同公共网站上不同公司运行的这些不同 Wave 服务器之间发送 Wave。因此，即使谷歌决定完全取消他们的 wave 服务，这并非不可能，如果有人想过来说，看，我们将在我们的 Wave 服务器上为任何想要的人提供免费的公共 Wave 帐户，微软 Hotwave 或其他什么，他们可以这样做，你将能够使用其他 Wave 服务器与其他人交流，这就是协议的设计。所以，你知道，谷歌做了很多工作来确保这个东西能够在谷歌自己的 Wave 服务消亡后继续存在。我认为有待证明的事情是，任何人都会使用足够多的 Wave，公共托管的 Wave 服务将变得有意义。

帕特里克:是的，因为我相信谷歌宣布他们将在今年年底，也就是 12 月 31 日，关闭它；我不知道他们是否已经延长或什么，但它即将到来。

凯文:是的，没错，所以如果你有很多波，你会认为会有某种导出工具。您可能希望在发行版发布后立即下载 Apache Wave 服务器，并建立自己的私有 Wave 服务器来托管所有内容。但它来了；看到这个真好。我想说的是 Apache 孵化器项目是 Apache Wave 或 Google Wave 的好去处——我已经称之为 Apache Wave 了！这是一个很好的地方，因为似乎有很多项目默默无闻了一段时间，直到他们的时间到来，然后突然流行起来，所以我认为这是一个很好的地方，如果 Wave 走在时代的前面，我们将把它放在这个程序中，它将继续由对它充满热情的人更新，当它到来时，它将免费供所有人使用。

很好的选择，布拉德，这个节目就此结束。伙计们，让我们围着桌子转。

**布拉德:**我是来自[网络发展工作室](http://webdevstudios.com/)的布拉德·威廉姆斯，你可以在推特 [@williamsba](http://twitter.com/williamsba) 上找到我。

Patrick: 我是 iFroggy 网络的 Patrick O'Keefe，我在 managingcommunities.com[写博客](http://www.managingcommunities.com/)，你可以在 Twitter [@iFroggy](http://twitter.com/iFroggy) 上找到我。

斯蒂芬:我是斯蒂芬·塞格拉夫斯，我在 badice.com[写博客](http://badice.com/)，你可以在推特[@塞格拉夫斯](http://twitter.com/ssegraves)上找到我。

凯文:你可以在推特 [@sentience](http://twitter.com/sentience) 和推特 [@sitepointdotcom](http://twitter.com/sitepointdotcom) 上关注我。请访问我们在[sitepoint.com/podcast](https://www.sitepoint.com/web/podcast/ "Podcast")留下对这个节目的评论，并订阅自动获得每个节目。

SitePoint 播客由卡尔·朗内克制作，我是凯文·杨克。再次感谢您的聆听。再见再见。

迈克·梅拉的主题音乐。

*感谢收听！请随时让我们知道我们做得怎么样，或者使用下面的评论栏继续讨论。*

## 分享这篇文章