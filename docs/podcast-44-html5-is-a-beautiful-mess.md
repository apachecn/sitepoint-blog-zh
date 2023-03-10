# SitePoint 播客#44: HTML5 是一个(美丽的)烂摊子

> 原文：<https://www.sitepoint.com/podcast-44-html5-is-a-beautiful-mess/>

*第 44 集 SitePoint 播客*现已推出！本周，凯文·杨克( [@sentience](http://twitter.com/sentience) )和 [Opera Software](http://opera.com/) 的布鲁斯·劳森([@布鲁塞尔](http://twitter.com/brucel))、SitePoint 作者[伊恩·劳埃德](http://lloydi.com/)([@劳埃德](http://twitter.com/@lloydi))以及 [CSSquirrel 网络漫画](http://cssquirrel.com/)的创作者凯尔·威姆斯( [@cssquirrel](http://twitter.com/@cssquirrel) )一起讨论 W3C HTML5 工作组内部的最新骚动。HTML5 标准的进展有脱轨的风险吗，或者这只是标准开发的狂野世界中的一部分？

## 在浏览器中收听

直接在你的浏览器里播放这一集！只需点击下方橙色的“播放”按钮:

<audio controls="" preload="metadata"><source src="https://s3.amazonaws.com/s3.sitepoint.com/audio/podcasts/sitepoint/sitepointpodcast044.mp3" type="audio/mpeg; codecs=&quot;mp3&quot;"></audio>

下面是采访的完整记录。

## 下载这一集

你也可以下载这一集作为一个独立的 MP3 文件。链接如下:

*   SitePoint 播客#44: HTML5 是一个(美丽的)烂摊子

## 采访记录

凯文:2010 年 1 月 15 日。一个专家小组考虑了 HTML5 工作组内部的最新冲突。我是凯文·杨克，这是 SitePoint 播客第 44 期:HTML5 是一个(美丽的)烂摊子。

今天，web 标准社区中的许多知名人士也加入了我的行列。我们邀请了 Bruce Lawson，他是 Opera 软件开发人员关系部的一名 web 标准宣传员。

布鲁斯:嗨，凯文。

凯文:你好。你是《html5doctor.com[的六位合作作者之一，对吗？](http://html5doctor.com)

布鲁斯:六个，还在数，是的。

凯文:六个，还在计数，这就像一个 Q &一个专注于 HTML5 的网站，回答人们的问题，揭开这个过程的神秘面纱。这样说公平吗？

布鲁斯:是的。这是因为规范不是为网络作者写的，规范是为伟大的大师写的，所以我们试图做翻译。

凯文:很好。它不是一个医学建议网站。

布鲁斯:(笑)不，不，不，绝对不是。

**凯文:**伊恩·劳埃德也加入了我的讨论，他是 SitePoint 网站新手开发者书籍的作者，[使用 HTML 和 CSS 以正确的方式构建你自己的网站](https://www.sitepoint.com/books/html2)。第二版已经出版了。

凯文:嗨，伊恩。

伊恩:你好。

**凯文:**伊恩也是 SitePoint 的[的作者，这是最终的 HTML 参考文献](https://www.sitepoint.com/books/htmlref1)，其的[在线版本最近刚刚更新了最新的浏览器支持信息，可在](https://reference.sitepoint.com/html/)[reference.sitepoint.com](https://reference.sitepoint.com/)免费在线获得。

最后，今天我们请到了凯尔·威姆斯，他是 CSSquirrel，或者是 CSS-squirrel 的发音？

凯尔:只是 CSSquirrel，这是一些与人的巨大困惑，是的。

凯文: [推特上的 C-S-S-Q-U-I-R-R-E-L](http://twitter.com/cssquirrel)，地点是 cssquirrel.com。Kyle 是一名标准意识很强的 web 设计师、web 标准评论员，同时也是一位总是咬人的 CSSquirrel web 漫画的创作者。

凯尔，你本周的漫画，HTML5 秀是我制作这个播客的动力，所以欢迎你。

凯尔:哦，谢谢你。

凯文:正如我所说，这是一个及时的播客。在过去的一周里，web 标准社区已经被这件事搞得焦头烂额…我想你可以称之为丑闻。在今天的节目中，我们将关注这个似乎已经占据了我本周所认识的每个网络开发者的大问题，那就是柯南·奥布莱恩威胁要离开《今夜秀》。

凯尔:(笑)

布鲁斯:谁？

凯文:(笑)作为一名美国参与者，我想凯尔可能是唯一一个得到这个答案的人，向今天船上的英国人道歉。

伊恩:是啊，我们现在只想谈论雪。

凯文:那里下雪了，越来越多的雪。你们国家这周全是白色的是真的吗？

布鲁斯:千真万确。

伊恩:几天前，美国国家航空航天局的卫星拍摄了一张非常棒的照片，照片上我们被完全覆盖，所以感觉它永远不会结束，而这才过了一周。我们是一群懦夫。

凯文:在雪下的隧道中穿行，我明白。

伊恩:绝对是。

**Kevin:** 但是说真的，[上周的播客](https://www.sitepoint.com/podcast-43-passive-over-sharing/)在 SitePoint 这里，我们提到了 HTML5 和一些开发人员试图利用这个仍在开发中的规范进行的实验。但在过去的一周里，我们在 W3C HTML5 工作组内部和外部爆发了新的争议，这是问题的一部分，所以我想在今天的节目中解决这个问题。我试图描绘出这是什么，这意味着什么，我们是否应该关注。公平地说，HTML5 规范的开发过程从一开始就是混乱的。当然，凯尔在这方面做得很好，他画了很多关于个性和观点冲突的漫画。Bruce 对于那些不太关注 HTML5 的人，你能给我们一个大概的想法吗？

布鲁斯:谁-呼-呼…是的。首先，让我们回到它是如何开始的。

凯文:当然可以。

布鲁斯:因为如果我们从头开始，这是一个非常好的起点。

如果你不了解它从何而来，你就无法理解它是如何走到今天这一步的。

布鲁斯:的确如此。我想是在 2001 年或 2002 年，W3C 对 HTML4 进行了最后的修改，推出了名为 HTML4.01 的版本，然后宣布他们放弃了 HTML 的工作，因为未来是 XML，他们说。许多人相信他们，我个人也相信，但后来他们开始幻想出一种叫做 XHTML2 的疯狂东西，这是一种规范——我以前曾将其描述为一种奇妙、美丽、哲学上纯粹的规范，与现实世界毫无关联。它与浏览器制造商正在做的事情无关，也与作者想要做的事情无关，它严重破坏了向后兼容性，甚至一度贬低了`<img>`标签。他们把那个带回来了，但那是他们要去的地方。

因此，一群为浏览器制造商工作的人——从我工作的 Opera 开始，但很快 Mozilla 和苹果的人也加入了我们——开始在他们自己的私人时间里编写一个名为 WHATWG 的游击队规范。我想那代表网络超文本应用技术工作组。他们称这个规范为 web 应用 1.0，因为它是为扩展 HTML 而设计的规范，这样它就可以为 Web 应用工作，你知道像 Yahoo！邮件、谷歌地图、谷歌文档、图片编辑软件 Picnik，这些网站都是应用程序，而不是静态文档。他们在自己的时间里做这些事情，然后蒂姆·伯纳斯·李说“我们在 W3C 犯了一个错误，我们想去接触并重新开始 HTML 的工作”，然后进行了投票，他们决定问 WHATWG 他们是否可以使用他们的规范作为被称为 HTML5 的基础。

所以 HTML5 是 W3C 虚构出来的一个词，WHATWG 称之为 Web Applications 1.0，而规范是由这两个组织同时制定的。所以它是由 W3C 和最初的监管者 WHATWG 开发的，完全可以说这两个组织之间有相当大的文化冲突，现在已经到了顶点，但上周的恶作剧并不比我们之前看到的更糟，我认为我们会再次看到它们。

凯文:所以，纸上谈兵是一次伟大的和解——或者至少我们希望这是朝着那个方向发展——但事实并非如此。这公平吗？我的意思是，这似乎仍然是一个失去联系的两个团体。

布鲁斯:绝对是。我不知道曾经有过什么伟大的和解。例如，当 W3C 决定结束 XHTML2 的工作时，即使它还没有完成，他们这样做是因为他们承认 WHATWG 是正确的，在我看来，WHATWG 中肯定有一些成员是不必要的胜利者，如果你愿意，他们几乎是在为这次战胜 W3C 而沾沾自喜。

我为 Opera 工作，我们是许多 W3C 委员会的成员。我自己也是一个工作组的成员，我是一个网站站布道者。我有很多时间给 W3C，但公平地说，W3C 真的放弃了 HTML，它需要 WHAT 工作组在背后给他们一脚，让他们意识到 HTML 没有死，也不会死，它不能破坏向后兼容性，但从来没有和解。当 WHATWG 和 W3C 走到一起时，这总是一个非常不舒服的会议。

凯文:这就把我们带到了本周，你说本周的恶作剧并不比我们之前看到的更糟糕，但是——好吧，凯尔，你本周的漫画，[html 5 秀](http://www.cssquirrel.com/comic/?comic=49)描绘了一幅整个过程已经退化成类似于布偶秀的画面。从你的角度来看，事情是变得更糟还是更好？

凯尔:我觉得它们今天比我第一次做的时候要好一点。

凯文:好的。

凯尔:但话虽如此，我认为这种情况不会持续下去。我认为作为一个过程，它将继续分解。

凯文:到底发生了什么？

Kyle: 我的意思是，你有这两个组织，因为据说他们有非常不同的哲学和关于规范该做什么，我感觉 WHATWG 不再真的认为 HTML5 是 HTML5 了。在编辑伊恩·希克森的领导下，他们正在前进。他们已经就此做了最后的决定。他们准备继续向“无版本”HTML 添加内容。与此同时，W3C 仍在试图解决 HTML5 的具体目标是什么，该规范还没有最后一次发布。

凯文:对。

Kyle: 当他们通过大量的人的贡献做出决定，哪些应该在规范中，哪些不应该在规范中。至少在我看来，像伊恩这样的人有一些相当严重的分歧，因为基本上他是主编，如果他不同意，他就选择了，“嗯，它仍然在规范中，但如果它让你感觉良好，你们不能在你的那里。”我是说它还在继续前进。这听起来像是沟通的崩溃，就像是即使他们真的理解对方，也没有办法弥合差距，友好相处。至少这是我看了各种邮件列表和聊天记录后的感觉。

凯文:这是一个奇怪的情况，我们说有两个阵营，一个是什么工作组，另一个是 W3C 工作组，但事实上伊恩·希克森被任命为这两个工作组的领导。

**Bruce:** 是的，虽然他是联合编辑——我相信 W3C 规范是与苹果公司的戴夫·海厄特联合编辑的。

凯文:对。

**Bruce:** 虽然我觉得 Ian 是 WHATWG 这边的唯一编辑。尽管我很坦白，我的兴趣主要是技术，而不是政治，因为我不太了解政治，这是一份全职工作。

凯文:伊恩，作为一个经常为刚入门的网络开发人员和真正完成工作的人写作的人，而不是痴迷于政治的人。我们在这里看到的辩论的基调对你的 HTML5 方法有什么影响？

伊恩:好吧，如果布鲁斯说他不太关注政治，那我就完全被蒙在鼓里了，因为我有意识地努力避开 HTML5 的细节。我肯定对它感兴趣，我肯定对它感兴趣，从我有兴趣做好我做的任何事情并遵循标准的角度来看，但我试图不在细节上过于臃肿，因为我知道它总是在变化。我真的真的很期待有一天，我真的可以拿起一本书，里面什么都有，我可以信任它，依赖它，那就是我最终真的开始投入 HTML5 的时候。

从我的角度来看，我目前在我工作的地方处于这种情况，我正在努力为可能几年内不会公开的项目制定一些标准，我们在这里谈论的是将有一个 web 前端，但它是一个更大的项目的一部分。所以我面临着这样的情况，我试图定义一些标准，不知道两年后我们会在哪里，也不知道我现在可以有把握地说什么。我能说什么是我们想要的标准呢？当我说标准时，我指的是某种内部记录的建议，而不是某种适当的标准，如果你明白我的意思的话。

这就是我在 HTML5 上遇到的问题。我现在能说什么是安全的呢？每次我听到 HTML5，并不是因为我在看 IRC 日志或类似的东西，因为我不看。我唯一一次真正听到 HTML5 发生了什么是通过 Twitter，不幸的是，我从 Twitter 上听到的大多数是呻吟和抱怨，这显然是上周发生的事情。这真的很有趣，也足够滑稽的是，我真正发现关于最近的恶作剧[发生了什么的页面是凯尔写的](http://www.cssquirrel.com/2010/01/11/comic-update-the-html5-show-aka-a-mess/)。

几天前，戴夫·雪伊在推特上说，他真的想找个人来总结一下这一切是怎么回事，所以终于发现这些恶作剧是怎么回事很好，但我不认为我真的完全理解政治，我可能真的不想理解。

凯文:对。我们当然看到了很多，某种程度上，我看到了很多来自“什么”工作组的有用的总结。他们喜欢发布他们工作的每周总结之类的东西。但正如我们所看到的，他们正把注意力转向未来。这就像他们——我不想不公平，但在我看来，他们感兴趣的是什么工作组，他们感兴趣的是开辟道路，为下一件事设定方向，并制定出所有细节，关于他们创造的最后一件事将如何形成一个连贯的标准，他们并不感兴趣。

**Bruce:** 不要忘记 Kevin，很多我们称之为 HTML5 的东西实际上是关于记录以前从未被记录下来的实际现实，所以 HTML5 的部分工作是记录 XMLHTTPRerequest 的第一个规范，这是微软发明的，我们每天都在使用 Ajax，但没有人写下来，像 Canvas 这样的东西是苹果发明的，每个人都反向工程到不同的浏览器中，像 ContentEditable 这样的东西是微软发明的，因为所有不同的浏览器都实现了它。因此，他们所做的许多事情实际上记录、规定和编纂了我们已经使用了很长时间的东西。HTML5 规范中肯定也有奇妙的新想法，但这并不意味着它们只是在梦想新的东西并继续运行。我认为他们认为他们已经做了很多事情，记录下我们目前使用的东西，现在他们已经有了这个基础，他们可以开始前进了。再说一次，我不属于那个团体。这是我个人对这个团体以外的看法。

凯文:说吧，伊恩。

伊恩:抱歉，我只是想说他们总是用一个很好的短语来形容这件事，不是吗，布鲁斯？“铺牛路”。

布鲁斯:的确如此。

伊恩:我相信这就是人们一直提出的观点。

Bruce: 是的，这就是 HTML5 和 XHTML2 在哲学和实践上的本质区别。HTML5 说不管是好是坏，这就是我们的处境。让我们记录它，让我们编纂它，让我们标准化它，这样如果其他人明天想做一个浏览器，这都是为他们写下来的。一切都可以互操作，然后让我们在现有的基础上继续发展，而不是进入一个 XML 统治世界的幻想世界，我们可以从头开始设计 Web，打破向后兼容性，因为那里有无数的网页，我们不能承受它们被破坏。我们需要他们继续工作，所以我们在这里。

凯文:所以，想想伊恩为他自己的公司在未来几年内将要建立的标准所做的努力。布鲁斯作为今天在这里的官方标准机构代表，听起来你的建议是忽略噪音和像什么工作组这样的人的宏伟计划，专注于 W3C 现在正在建立的严肃标准。这是稳定的核心，如果你需要坚持的话，你可以专注于此。

布鲁斯:是的。首先，让我说我不代表任何标准机构。我为 Opera 工作，但我不属于编写 HTML5 规范的团队。我的兴趣是作为一名开发人员，我有开发人员的背景。

凯文:那我就称你为网络标准精神的捍卫者。

布鲁斯:好的，好的。我是黄蜂中的一员，但我不是 W3C 的人，也不是 WHATWG。

凯文:当然可以。

**Bruce:** 是的，如果你看看 WHATWG.org 的 WHATWG 版本的规范，你会看到每个元素或部分旁边都有方便的小状态栏，告诉你在浏览器中的实现位置，一些东西如 Canvas 在五个大浏览器中的四个上得到了很好的实现，其他东西如 ContentEditable，你可以全面使用。其他东西，比如 Web 套接字，没有在任何地方实现，所以…

**伊恩:**微数据。

**布鲁斯:**微数据。…建议将这些作为内部标准是愚蠢的，但不难理解并找出规范的哪些部分接近真实世界，或者实际上只是记录我们已经使用了很长时间的内容。关于噪音，我的意思是伊恩提到他在推特上关注了很多东西。我认为有……我们生活在一个比我们写 HTML4 时更嘈杂的环境中，因为我们可以像我一样每天在 Twitter 上发 900 次亲密的想法，但我也认为人们假设现在比过去有更多的政治是天真的。W3C 的情况是，它一直是一个成员组织，人们坐在烟雾弥漫的房间里，相互争论，在酒店里面对面地踢对方的球时，相互扭打和微笑。

我们现在从 WHATWG 得到的是，他们开始了一个任何人都可以加入和评论的过程，伊恩·希克森一直说他不在乎好主意来自哪里。它可以来自 W3C 成员组织，也可以来自班加罗尔、柏林或伯明翰的个人。它可以发布在 IRC、Twitter 或博客上。如果是个好主意，那就是个好主意。所以我认为额外的噪音和有更多政治的想法实际上是 a)我们的 Twitter 化社会的功能，以及 b)这是一个非常开放的过程，争吵发生在公开场合，而不是在硅谷酒店大厅。

Kevin: 我曾经和 W3C 工作小组的人谈过，当他们做这个过程的封闭版本时，这确实看起来像是新的 HTML 做事方式，如果没有别的，对参与辩论的人的心理健康更好。但是从你的角度来看，在更大的网络社区的眼中，这种开放性是否损害了规范？

**Bruce:** 在我个人看来，如果在更广泛的社区中有损害，这是非常短暂的，从长远来看，它会导致一个非常优秀的规范。这是我个人的观点。我不知道其他人的感受。

伊恩，你会同意吗？

伊恩:这要看你问我今天是什么日子了。我的意思是，有些日子我会非常热衷于我实际上可以用 HTML5 实现什么，现在使用什么是安全的，而其他日子你确实有这些激烈的争论，让我思考，(叹气)“我们为什么要烦恼？”

凯文:我想知道的是，你是愿意听到所有这些声音，接受还是不接受，还是更愿意像以前那样，我们只是得到了这些经过深思熟虑、措辞得体的规范草案，让我们作为一个整体来考虑，并以文明的方式提交反馈。知道他们在争论什么或者仅仅是他们代表我们在争论，这种感觉好吗？

伊恩:我认为总的来说，我们听到争论的内容可能是一件好事，我的意思是你可以一直说“无知是福”。如果有人把什么东西放在你面前，说事情应该是这样的，你可以说，“哦，我接受。那也行。”但是如果你对此有问题，那么做任何事情都为时已晚。因此，人们就哪里是对的，哪里是对的，哪里是错的展开争论或讨论是件好事。如果我们回想一下 WCAG2 的可访问性指南发生了什么，它经历了绝对巨大的变化，如果不是因为它而引发的大风暴，它就不会成为现在这样的文档。因此，当 HTML5 出现这种情况时，你必须想好，道路上有一些颠簸，但最终会对最终结果有好处。

凯文:是的。我知道凯尔，你一定很支持这个公开程序。否则你就没什么好画的了。

Kyle: 没错，如果 HTML 不那么吵闹的话，我还会试着编更多关于 Opera 的笑话——无意冒犯，Bruce。我认为过程是一件好事，因为除此之外，它给了我每周画画的东西。是的，从理论上讲，它确实让每个人对他们周围发生的事情都有发言权，因为 HTML5 或无版本 HTML 或我们最终将称之为 Frank 的任何东西都将被使用数年，如果不是远远超过我认为一项技术应该持续数十亿网页的时间的话。所以你知道，如果我们现在不发表意见，那么很自然，我们以后只会抱怨。

我要说的是，它唯一让我个人有时感到不安的部分，我最终画出这些愤怒的松鼠漫画的原因是，当你有公共程序的表象，然后当大多数人做出决定时，你会喜欢——我喜欢挑他们的毛病，但是——编辑伊恩·希克森会说，“好吧，我们已经有了这个过程。这个过程已经完成了。我已经决定无论如何都要这样做。”当这种情况发生时，我有点受伤，因为这个过程是一种幻觉，它显示了它是什么，然后就像这样，我们怎么想又有什么关系呢？

凯文:所以在这种意义上，你的漫画是一种和平的抗议形式？

凯尔:绝对是。也许这只是一个原则，因为通常 HTML5 的最终结果是好的，所以不管过程如何，它可能是一个好产品，但如果你要有一个过程，我宁愿它至少是诚实的。

布鲁斯:我认为凯尔发现了一些非常有趣的东西，因为虽然我一直在谈论开放的流程，但这是真的，或者说我认为 WHATWG 和伊恩·希克森运行的是一种开源流程，希克斯是一个仁慈的终身独裁者，W3C 运行的是一个寻求共识的流程。希克斯公开表示他不寻求共识。他会权衡所有提供给他的证据，不管这些证据来自哪里或以什么形式出现，但最终他会做出决定，因为他认为当你在一个工作组中看到一千多名参与者时，你不可能有一个基于共识的方法。我无法评论，我甚至无法想象每天都要处理一千个像我一样的人在电子邮件中的慷慨陈词和咆哮是什么感觉，但我认为凯尔说的是真的，没有寻求共识的方法。显然，这不是一个寻求共识的方法，而是，“我已经听了你们所有人的意见，这就是我的决定。”伊恩送的。

Kyle 说了一些有趣的事情，即该规范可能会持续很长时间，比一项技术应该持续的时间还要长，但我认为 HTML5 已经延长了 HTML 的寿命，因为像 Flash 和 Microsoft Silverlight 这样的东西作为构建 web 应用程序的方式紧跟在 HTML 的后面，因为用 HTML 和 JavaScript 做这些太困难、太笨拙或太笨拙了。所以我认为 HTML5 进程已经是我们这些相信网络应该成为开放标准的人的一个胜利，因为它使我们所有人使用的工具更适合我们需要构建的东西，并因此有希望延长我充满热情地相信的网络开放标准的生命。

凯文:看起来我们有三个层次的开放性，如果我们把它分成不同的层次。您有像闪存这样的封闭技术，它是永远的替罪羊，但闪存是由一家公司开发并作为已发布的规范发布的，只是一家公司对事情应该如何发展的看法。然后，我们看到了 HTML5 的发展方向，每个人都有发言权，但最终决定和发布的内容，似乎归结为每个人都有发言权，而不是 W3C 希望在其典型的标准流程中达成的共识。最后，我们有了共识的理想，不管这意味着什么，在任何一天，不管你让工作组中 75%的人以同样的方式投票，你都称之为共识，如果你是另外 25%的人，那就太糟糕了。所以听起来 HTML5 是一个中间地带。它足够开放，每个人都有自己的发言权，这使得它比我们可能坚持的东西更好，但它不是我们可能希望的理想。

凯尔，你要接吗？

凯尔:嗯，当然。现在听起来有点像狂野的西部，你知道吗？呃，我们应该以它命名 HTML5。但是，我的意思是，Bruce 说的一件事我必须完全同意，那就是关于 Flash 和 Silverlight 等插件的那一点，作为一个每天都与代码打交道的人，作为一个对可访问性越来越了解的人，你看着这些插件，它只是在网页上产生了这种巨大的，令人讨厌的过剩，很难使用或做事情，当然是由制作它们的各种公司所拥有的。所以他们决定如何单独工作。我喜欢 HTML，它是一个面向所有人的开放标准，而且 HTML5 做了更多开发者做的事情。伊恩只是，如果我们可以让他跳进去，但他只是写了一个关于画布级可访问性的观点，这是一个很好的观点。

**Ian:** 是的，我的意思是你显然提到了 Flash，而 Flash 是，取决于你给谁，它可以是难以置信地不可访问的，或者它可以是非常可访问的，但是找到一个能在 Flash 电影或应用程序方面做得非常非常好的开发人员，并不容易找到一个能做到这一点的人。但是根据我的理解，我对 Canvas 在可访问性方面能做什么的理解很少，这实际上是目前的一个大障碍，所以它可能使用开放技术，但它有自己的行李。这样说公平吗，布鲁斯？

布鲁斯:是的。我认为这是因为 Canvas 是苹果公司的专利发明，是他们为仪表板发明的，追溯起来是一个规范。因此，还没有实现它，但我知道我在 Opera 工作的几个人对使 Canvas 可访问感兴趣，我向他们介绍了在 Adobe 或 Macromedia 工作的人，以追溯 Flash 5 的可访问性，他们一直在谈论。这也给了我一个机会，我可以说我不想参与，或者说我不想让人认为我是在抨击闪存。Flash 是一项很酷的技术。

当我去年在一次会议上发言时，有人对我说，一些让我印象深刻的事情，我一直在寻找参考，这个人告诉我，这是对 1900 年世界上 100 家最大的公司进行的研究，其中只有三家在 2000 年仍然存在。我认为任何一家公司有多好都没有关系，如果网络要持续下去，就不能委托给任何一家公司，不管这可能多么仁慈，因为你永远不知道那家公司是否会存在，如果所有的历史和我们在网络上建立的所有东西都因为一家公司的倒闭而消失，这难道不令人震惊吗？所以这就是为什么我确信它应该是一个开放的标准，不是因为我想抨击任何一个特定的公司，只是因为我认为网络太有价值了，不应该掌握在任何一个公司的手中。

凯文:那么，一家公司 Adobe 构建 Flash 并发布它，最终以一种文件格式发布，供其他人在认为合适的情况下实施，与苹果将 Canvas 带到 what 工作组，提出它是任何浏览器都可以实施的东西，这两者之间有什么区别呢？有什么区别因为看起来很微妙？

Bruce: 是的，我认为主要的区别是，Canvas 在我出生之前就被其他浏览器复制了，它是它们的原生版本，所以你不需要插件就可以使用 Canvas，而 Flash 的浏览器必须将数据传递给插件。举个例子，如果你把视频数据传给一个 Flash 插件，它就是一个黑匣子。如果它崩溃，你无法控制它，浏览器制造商无能为力，而对于原生视频，你可以选择任何方式操纵它，所以在我看来，Flash 作为一种产品没有任何问题。这只是出于历史原因，它是一个插件，Canvas 原生存在于五个浏览器中的四个，因此它是一个开放标准。

Ian:Bruce 今天在 Twitter 上提到了一些事情，你认为这种富媒体的最大障碍之一实际上是缺乏可视化的 ide。因此，对于 Flash，有一些工具已经存在了很长时间，可能是 Silverlight，但不是每个人都喜欢，但他们一直在努力开发这些工具，不管你是否想使用它们。但是，如果你想创作出可以在画布上输出的东西，那么你从哪里开始呢？

**Bruce:** 这很有意思，因为在我发了这条微博之后，我立即收到了一个为 Opera 工作的人的回复，他是 SVG 工作组的成员，这是一种与 Canvas 类似但不相同的技术，他给我看了一个 Adobe 工程师的演示，他把 Illustrator 文件导入 Dreamweaver，然后 Dreamweaver 就变成了 Canvas，这很有趣，我以前不知道它的存在。这真的很令人鼓舞，因为我被指向的那篇博客文章，那个 Adobe 的家伙指出，Adobe 并没有通过分发 Flash player 赚钱。Adobe 通过销售创作工具来赚钱，所以如果 Dreamweaver 和 Illustrator 以及 Flash 工具可以像 Flash 一样轻松地制作 Canvas，那么当然，人们仍然有很大的动力花钱购买 Creative Studio，因为在你想编写 Canvas 的时候，你必须编写 JavaScript，而不是每个人都想这样做。

Kevin: 让我们回到 HTML5，Kyle，通过你的漫画，我想每个人都很清楚你对标准过程的看法，但是就你每天所做的实际 web 开发工作而言，你对 HTML5 的态度是什么？我的意思是，我们在这个播客中听到一些开发人员已经开始使用 HTML4，其类名与 HTML5 元素相匹配，作为一种前瞻性的标记方法，而其他人实际上已经开始使用 HTML5 标记，尽管今天的浏览器带来了一些困难。你如何接近它？

**凯尔:**这实际上是一个非常好的观点，因为它与 HTML5 的宣传有某种联系，我稍后会试着回到这个话题。我在 Mindfly 网站设计工作室工作，所以我向老板汇报意见和诸如此类的事情，所以 HTML5 可能是一个棘手的销售，特别是一旦你开始做整个`<!DOCTYPE html>`位，因为某些尚未完全离开互联网的破旧浏览器可能会给你带来问题，当你开始在它上面做标记时，会带来严重的问题。特别是 Internet Explorer 7 和更年轻或更老的版本，当涉及到样式元素时，就相当成问题，这些元素在制作时并不存在，如页眉和页脚等等。

凯文:对。

Kyle: 所以实际上在工作场所发生的事情大体上是这样的，不幸的是，我仍然在用 HTML4、XHTML1.1 或任何你想叫它的东西编写代码，并做着类名称约定，以便在心理上为诸如页眉和页脚之类的东西做好准备。 然后，当我可以摆脱它时，当我们有一个客户想冒险，或者如果 HTML5 在某些浏览器中允许的功能在其他浏览器中不是百分之百重要，那么我们将继续冒险使用 HTML5，让他们做好准备，因为某些浏览器将无法使用这些功能。 所以从这个角度来说，我把它用在我的个人物品上，但当涉及到为客户制作东西时，很多时候我必须确保网站对每个人都有效，所以某些功能并不是对每个人都有效，不管怎样，这与某种渐进的体验相反。

倡导 HTML5，不仅在工作场所，而且在 web 开发社区，然后是所有 web 开发人员和设计人员的在线社区，当人们不确定规范的稳定性时，让他们冒险为客户创建网站以换取金钱是很棘手的，我认为这是这个公共过程中最大的问题。我的意思是，我很高兴这是一个公开的过程，但当各小组似乎没有相处好的时候，我们有一个项目在规范中，然后它不在规范中，或者你有一些东西改变了它的功能，这些人变得非常害羞，不敢贸然尝试采用这些功能，并承认这是一个正在进行的规范，所以这是预期的，但它可能会使它难以销售。

**Ian:** 关于宣传的想法，我想讲一个我和 Bruce 一起工作的小故事，我不会说一起工作过，但是我的一个同事问了我一个问题，他们问我关于在 HTML5 中实现视频的问题，我开始回答这个问题，然后停下来想，“你知道我不是询问这个问题的最佳人选。”我找到布鲁斯，说:“你有兴趣下来和我们的开发人员谈谈吗？”在我们的团队中，我们有一个由大约 12 名开发人员组成的小组，他们——嗯，并不是所有人都精通 web 标准，但是我们有相当比例的人了解 web 标准，他们会很热情，会渴望学习。

布鲁斯很高兴来到这里，谈论 HTML5，谈论一些 Opera 的东西，并给我们一些演示，这真的非常非常好。我们对这些人有一些真正热情的回应，我认为布鲁斯很高兴来到一个我们有数千名员工的公司。这是一个很大的组织，但是有人了解它。我们开始一步一步地使用 HTML5，我指的是非常非常小的一步。例如，我们现在使用 HTML5 文档类型。这是显而易见的。这只是一个简单的小改变，我把它作为我们不久前经历的重新燃烧练习的一部分。

回到我之前在播客中说的关于尝试定义一些标准，并从你所说的关于使用符合 HTML5 标准的类名中吸取教训。在我写的这些文档中，我实际指定的一件事是，我们应该使用 header 和 footer 元素，而不是`<div class="header">`或`<div id="footer">`,除此之外，我还创建了一些小 favelets，测试人员可以使用它们来测试它，所以如果有一个 div 的 ID 看起来像是 HTML5 的等价物，它就会提醒它，提醒他们注意它。

所以这些我们已经开始实现的小东西，但是在我们开始使用视频元素之前还有很长的路要走。我认为最终，我们将得到一个 HTML5 文档类型、XHTML 语法、Flash 和可能的 Canvas 的大杂烩。但我们面临的另一个问题是，你说的是较旧的浏览器，我想来这里工作是许多公司的典型情况，我们仍然在使用 IE6，我知道，我们不会放弃 IE6，可能在未来几年内，我们会在公司层面上有所突破。 我们将从 Windows XP 直接升级到 Windows 7，但我们仍然在谈论另一个两年的 IE6，这可能会使开发者非常非常有问题，“嗯，我们想使用 HTML5。 我们可以想出这些东西。”它们在新的浏览器中看起来很棒，但是人们不得不签署这些仍然使用 2001 年的粗糙的旧浏览器，这可能是我们在试图推动 HTML5 前进时面临的最大挑战。

布鲁斯:哦，伙计，我听到了。如你所知，伊恩，我曾在英国法律协会工作，直到 2005 年，网络团队只允许使用 IE5 检查我们的工作，下载不同的浏览器是一种违纪行为。所以我知道你在哪里。现在我要变身为 HTML5 福音传道者，但他们所做的哲学上的美妙之处在于它是如此向后兼容。因此，使用视频元素，您可以在标签之间放置后备，并且如果您愿意，可以嵌入 Flash 电影。因此，可以使用视频元素的浏览器就可以了，而不能使用视频元素的浏览器只会后退并导入 Flash 电影，它不一定具有原生视频的所有可爱之处，但会显示一些我认为很棒的东西。

如果有人想看的话，有个叫克罗克·卡门的人，我想是 C-A-M-E-N，他的网站是[camendesign.com](http://camendesign.com/)。如果你在你的搜索引擎中查看[为每个人](http://camendesign.com/code/video_for_everybody)准备的视频，你会找到它，他得到了[一些漂亮的代码](http://camendesign.com/code/video_for_everybody)，它验证为 HTML5，但将通过使用 Flash 作为 IE6 的备份，在所有浏览器中显示视频，等等。这是最棒的事情。

画布，有点复杂。header 和 footer 元素，Remi Sharp 我的一个朋友，也是 HTML5 的一位博士，添加了一点 JavaScript，我认为这将使它们在 IE5 以后变得有风格。它在 Netscape 4 中无法工作，因为我去年在一次会议上发言时尝试过它，它有点不稳定。

凯文:哦，天哪，那就排除了。(笑)

布鲁斯:是的，我想我们可以原谅网景——不在网景 4 中工作。

凯文:是的。

布鲁斯:很抱歉我戴上了福音传道者的帽子，但这就是为什么我原谅所有的政治胡说八道，因为它可以做到这一点。他们以一种允许最大限度向后兼容的方式来设计它，还有 Ian，如果你使用 XHTML 语法，一些 Canvas，一些 Flash 和 HTML5 doctype，这不是不匹配，你在做 HTML5 的设计目的，那就是反映真实世界中真实的开发者如何与真实的客户一起工作，这与我们使用 CSS 的方式完全相同。你知道，我们有渐进的增强，我们使用的东西将在现代浏览器中工作，但在那些不工作的浏览器中会优雅地降级。这是我们一直以来的工作方式。没有什么，没有必要让任何人感到羞愧，说这是一个真正的不匹配。我们这么做已经很久了。

凯文:这听起来像 HTML5，表面上你会看到像视频这样的标签，你会说，“哦，这将使视频变得更加简单。它只是一个标签。”但从短期来看，这是你在两个嵌入对象的官样文章周围加上的又一个标签，也许还有针对 Internet Explorer 的条件注释，所以它增加了现在的复杂性，作为一种投资，希望在未来降低这种复杂性。

Bruce: 但是从语言的角度来看，它非常非常简单，不需要用它来包装对象和嵌入。从必须服务 IE6、IE7 和 IE8 的角度来看，你必须这么做，但这种语言没有什么特别复杂的，但至少它有向后兼容的能力。当你将其与他们试图用 XHTML2 做的事情进行比较时，你就会明白为什么 W3C 的努力，在我看来，我个人的观点，是相当低迷的，而 WHATWG 的出现是如此之好。(背诵)*这是我个人的观点，不代表我的雇主等的观点。*

凯文:嗯，很明显，HTML5，如果你消除所有的噪音，如果你通过它看。这是一件好事，它将使网络变得更好。即使他们今天停止了这项工作，它也已经比我们以前拥有的好得多了，我想如果不是这样，当有迹象表明这个过程偏离轨道时，像凯尔这样的人也不会如此沮丧。

凯尔:那肯定是对的。我的意思是，如果它不是一个好产品，我可能不会注意，人们不会来看一个关于一只愤怒的松鼠谈论它的漫画。不管这个过程对我来说是怎样的，不可否认，我相当喜欢这个产品。现在对一些害羞的开发人员来说这是一个艰难的销售，这比我想象的要多，但我真的很兴奋，即使在今天，更不用说明天了。

凯文:嗯，谢谢你们。我认为这是一个很好的结尾，听起来我们已经得出结论，HTML5 的未来虽然不顺利，但至少现在看起来是安全的。再次感谢布鲁斯·劳森、伊恩·劳埃德和凯尔·威姆斯今天的参与。

布鲁斯:谢谢。

伊恩:谢谢各位。

凯文:感谢您收听 SitePoint 播客。如果你对今天的面试有任何想法或问题，请联系我们。

你可以在 Twitter 上找到 SitePoint[@ sitepointdotcom](http://twitter.com/sitepointdotcom)，你也可以在 Twitter 上找到我 [@sentience](http://twitter.com/sentience) 。

访问[sitepoint.com/podcast](https://www.sitepoint.com/podcast)给这个节目留下评论，并订阅自动接收每个节目。下周我们将带着我们通常的专家小组的另一个新闻和评论节目回来。

SitePoint 播客的这一集由 [Karn Broad](http://webkarnage.net/) 制作，我是凯文·杨克。再见了。

可以在 Twitter 上找到 site point[@ sitepointdotcom](http://twitter.com/sitepointdotcom)。

对这次采访有什么想法吗？请访问我们在[sitepoint.com/podcast](https://www.sitepoint.com/podcast)留下对这个节目的评论，并订阅自动接收每个节目。如果您有任何问题，请给 podcast@sitepoint.com[发电子邮件；我们很乐意在节目中把它们读出来，并给你我们的建议。](mailto:podcast@sitepoint.com)

SitePoint 播客的这一集由 [Karn Broad](http://webkarnage.net/) 制作。感谢您的收听，祝您 2010 年快乐、健康、成功。

迈克·梅拉的主题音乐。

感谢收听！请随时让我们知道我们做得怎么样，或者使用下面的评论栏继续讨论。

## 分享这篇文章